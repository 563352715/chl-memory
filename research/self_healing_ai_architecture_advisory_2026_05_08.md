# Self-Healing AI Platform Architecture -- Advisory (2026-05-08 EOD-16-late)

## 1. Operator's intent

### 1.1 Verbatim quote

> "we need to discuss how we intend to make this platoform self healing and
> self correcting. I need AI embedded into this platform with specific
> instructions to correct problems and modify code to keep this platform
> running. I dont know that a patch suggestion will cut it by itself."

### 1.2 Dev's read

Operator wants to move beyond the current state -- AI suggests, operator/dev
review-merge -- to a state where AI *runs* the platform: detects, diagnoses,
authors patches, tests them, applies them, monitors for regression, and
rolls back without human intervention. Operator only intervenes at strategic
decisions (architecture, money, integrations, scope).

The catch: "modifying code to keep the platform running" is the highest-risk
thing a self-healing system can do. Get it wrong and the platform writes
itself into a corner that requires a human rollback -- exactly the bottleneck
operator is trying to remove. So the design has to be aggressive about
auto-fix on safe classes and conservative about auto-fix on dangerous classes,
with a tight feedback loop between the two.

This advisory maps existing infrastructure, identifies the gaps for true
self-modification, proposes a 5-tier architecture, walks the risks, and
recommends a 4-phase rollout.

## 2. What CHL ALREADY has toward self-healing

### 2.1 Anomaly detection + dispatcher (Layer 2: diagnosis)

- `backend/self_healing/anomaly_dispatcher.py` -- 5-min cron polls
  `db.client_error_fingerprints`, `db.system_health_events`,
  `/api/carriers/freshness`. Categorizes + severity-scores anomalies, writes
  to `db.anomaly_dispatch_queue` with per-fingerprint cooldown (24h) and
  per-day budget cap (default 50).
- `backend/self_healing/context_bundler.py` + `_cron.py` -- enriches each
  queued anomaly with sample stack, last error, affected module; advances
  `db.anomaly_dispatch_queue` rows from `queued` -> `bundled`.
- `backend/self_healing/anomaly_digest_router.py` --
  `GET /api/_anomalies/digest` operator-facing read endpoint.

### 2.2 Sub-agent dispatcher (Layer 3: patch proposal)

- `backend/self_healing/sub_agent_dispatcher.py` -- reads `bundled` queue
  rows, dispatches `gpt-4o`, persists to `db.patch_proposals`. Each proposal
  carries: `root_cause`, `proposed_patch` (unified diff), `confidence`,
  `risk_level` (LOW/MED/HIGH), `requires_operator_approval`, `test_plan`,
  `affected_files`.
- `backend/self_healing/sub_agent_dispatcher_cron.py` -- background loop.
- Trust-gate matrix is **deterministic, rule-based** (not LLM-judged) -- it
  classifies based on `files_touched` count + LOC delta.
- v1 is **propose-only** -- never auto-merges.

### 2.3 Patch proposal API (operator review)

- `backend/self_healing/patch_proposal_router.py`:
  - `GET /api/_self_healing/patch_proposals?gate=HIGH&limit=25`
  - `POST /api/_self_healing/patch_proposals/{id}/approve`
  - `POST /api/_self_healing/patch_proposals/{id}/reject`
- Approval flips `review_status` to `approved` -- but **does not actually
  apply the patch**. Stage 1d (merge) is unwired.

### 2.4 Weight-tuning self-healing (Layer 5: re-tuning)

- `backend/self_healing/weight_tuning_proposer.py` -- reads
  `db.evaluator_outcomes`, computes residual error, proposes bounded weight
  delta (`MAX_DELTA_PER_CYCLE = 0.05`). Writes to
  `db.evaluator_weight_proposals`.
- `backend/self_healing/weight_tuning_router.py` -- approve/reject API.
- `backend/self_healing/weight_tuning_cron.py` -- 24h cron.
- v1 is **propose-only**; even an "approved" proposal does NOT mutate live
  evaluator weights. Stage 4 is gated on weeks of approved cycles.

### 2.5 Outcome tracking

- `backend/self_healing/outcome_tracker.py` -- predicted-vs-observed pairs
  for evaluator dimensions.
- `backend/self_healing/outcome_router.py` --
  `GET /api/_self_healing/outcomes`.

### 2.6 Health monitoring framework

- `backend/health_monitor.py` -- `register_health_check(module_name, fn)` +
  `/api/health/system` aggregator. 30+ modules register checks
  (ripgrep across backend/ confirms).
- `backend/system_health_monitor.py` -- per-cron transition events with
  `transitions[]` history. Feeds the anomaly dispatcher.
- `backend/sla_monitor.py` -- `@track_sla(module_name, target_ms)` decorator.

### 2.7 Operational kill-switches (`integrity/operational_state.py`)

| Env var                                | Effect                              |
| -------------------------------------- | ----------------------------------- |
| `CHL_READ_ONLY=true`                   | Platform refuses all writes (503).  |
| `CHL_CRONS_PAUSED=true`                | All cron loops skip every tick.     |
| `CHL_CIRCUIT_BREAKERS_DISABLED=true`   | Disables cb tripping.                |
| `CHL_BUDGET_CAPS_DISABLED=true`        | Disables LLM spend gating.           |

### 2.8 Budget caps (`integrity/budget_caps.py`)

Categories + default daily caps:
- `llm_self_healing` $15.00
- `llm_email_classifier` $15.00
- `llm_doc_extraction` $10.00
- `llm_analytics` $10.00

UTC day-bucket on `db.budget_ledger`.

### 2.9 Failure detectors

12+ documented detectors in `backend/failure_detectors.py` +
`backend/failure_detectors_part2.py` covering: stale GPS, missed checkin,
factor decline, shipper credit downgrade, inbox lag, etc.

### 2.10 Audit trail

`db.audit_trail` + 6 sibling collections (api_audit_log, broker_revenue_ledger,
factor_payment_events, reconciliation_alerts, etc.) -- every state-flip
write goes through `payment_rails.transition_payment_status`-style helpers
that mirror to audit_trail.

### 2.11 Inbox triage + URGENT detection

- `backend/inbox_triage/triage_classifier.py` -- gpt-classifies inbound
  emails with budget cap.
- `backend/inbound_email/action_dispatcher.py` -- auto-files + alerts.
- URGENT class triggers operator-facing notification.

### 2.12 Renewal calendar

- `backend/renewals/renewals_scanner.py` + `_calculator.py` +
  `_notifier.py` -- pre-scheduled compliance/insurance/license renewals
  with operator reminders.

### 2.13 Dispatch + reconciliation crons

- `backend/factors/dispute_cron.py`, `reconciler_cron.py`
- `backend/tracking/exception_detector_cron.py`
- `backend/inbound_email/tier2_cron.py`, `classifier_cron.py`,
  `action_dispatcher_cron.py`, `sender_classifier_cron.py`
- `backend/carrier_docs/expiry_cron.py`
- `backend/lane_scoring_cron.py`

## 3. What is MISSING for true self-modification

### 3.1 Auto-merge of AI-generated patches

The patch proposal pipeline ends at `review_status: approved`. Stage 1d
(merge) is unwired. Even an approved patch sits in `db.patch_proposals`
forever -- nothing applies it to disk, runs tests, restarts the affected
service. **100% of patches today require operator-side `git apply`**.

### 3.2 Live code-mod execution

There is no "AI applies a patch to disk" surface. To get there, the platform
needs:
- A worktree-isolated checkout where the patch is applied.
- A test-runner harness (already have pytest) that runs the relevant suite.
- A merge step (git commit + push) on test-pass.
- A deploy step (supervisor restart on dev / Render redeploy on prod).
- A rollback step on regression.

### 3.3 Auto-restart of crons on failure

Today: cron crashes -> operator sees alert -> operator restarts. There's no
`cron_supervisor` watching for "this cron has missed its expected
heartbeat by 3x its track_sla target_ms; restart it" pattern. Health monitor
*detects* but doesn't *act*.

### 3.4 Self-tuning weights without operator approval

`weight_tuning_proposer.py` writes proposals; operator approves; even
approved proposals don't mutate live weights. For the "stable-class" weight
deltas (small magnitude, multiple consecutive consistent observations,
non-money-axis), this should be auto-applied.

### 3.5 Self-issuing of cron schedules based on observed anomaly patterns

If a class of anomaly recurs at regular intervals (e.g., every Tuesday
0900 the FMCSA SAFER scrape fails), the platform should propose adding a
pre-emptive freshness check at Monday 1800. This requires meta-reasoning
over `db.anomaly_dispatch_queue` history.

### 3.6 Self-rollback on regression

Today, an approved patch (if it were applied) has no automatic
"watch metric X for Y minutes; rollback if degrades by Z%" wrapper. Every
self-applied change needs a `watched_metric` field + a rollback trigger.

### 3.7 Memory of what was tried

There's no `db.self_heal_actions` log keyed on
(failure_signature, attempted_fix). The platform retries the same failed
strategy over and over because nothing remembers "we tried this 3 times,
it didn't help, escalate to operator instead."

### 3.8 Embedded LLM reasoning loop (always-on)

`sub_agent_dispatcher_cron` runs every N minutes against the queue. There
is no continuously-running LLM agent that subscribes to events as they
happen and reasons across multiple signal sources. The current design is
batch + queue; the gap is event-driven + cross-source.

## 4. 5-tier proposal

### Tier A -- Trust-tiered auto-merge

Codify the existing trust-gate (LOW/MED/HIGH) into auto-merge boundaries:

| Class  | Auto-merge rule                       | Examples                                                      |
| ------ | ------------------------------------- | ------------------------------------------------------------- |
| GREEN  | tests pass -> auto-apply -> auto-deploy | log-level adjustment, dependency pin bump, test-fixture seed update, copy-text typo, retry-count bump within bounded range, env var rename in non-secret config, missing-import import, .gitignore add |
| YELLOW | tests pass -> dev-DM review -> manual approve -> auto-apply | new helper function, refactor that touches 1-2 files, schema-migration scaffolding, new cron spawn, error-message rewording, unit-test addition |
| RED    | tests pass -> operator-review -> operator approve | money-flow logic, payment-rail status transitions, factor submission, insurance/bond filings, FMCSA data writes, auth/session, replica config, new external-vendor integration |

Class boundaries are **deterministic + rule-based** (file-path + LOC delta +
keyword-match), NOT LLM-judged. The LLM cannot promote a RED change to
GREEN by claiming "small risk".

### Tier B -- Self-rollback metric watch

Every self-applied change is tagged with one or more `watched_metric` rows:

```
{
  patch_id,
  applied_at,
  metric_name: "/api/health/system overall_status",
  baseline_value: "healthy",
  rollback_condition: "value != 'healthy' for 3 consecutive checks",
  rollback_window_min: 30,
  rollback_action: "git revert + supervisor restart + alert operator",
  status: "watching" | "rolled_back" | "promoted"
}
```

If the rollback condition fires, the platform reverts the commit, restarts
the service, logs to `db.self_heal_actions`, and notifies operator. After
`rollback_window_min` clean, status flips to `promoted` and the watch ends.

Watched metrics for typical patches:
- `/api/health/system` overall_status
- error rate on the affected module's `register_health_check`
- p99 latency on `@track_sla`-wrapped functions in the diff
- domain-specific KPI named in the patch_proposal (`watched_metric` field
  becomes a required output of the LLM)

### Tier C -- Embedded LLM reasoning loop

A continuously-running OpenAI/Anthropic-backed agent process (separate
service, NSSM-managed on Windows) that:

1. Subscribes to (Mongo change-streams or polling):
   - `db.anomaly_dispatch_queue` insertions
   - `db.system_health_events` healthy->degraded transitions
   - `db.patch_proposals` new proposals
   - `db.inbound_emails` URGENT classifications
   - `db.factor_payment_events` reconciliation_alerts
   - `db.evaluator_outcomes` accumulation
2. Maintains a working-memory ring buffer of "what's currently degraded".
3. Reasons cross-source: e.g., "FMCSA freshness is stale + factor portal
   is degraded + inbox URGENT count is up -> operator is probably
   firefighting; defer non-urgent patches".
4. Issues actions: dispatch a sub-agent for diagnosis, request a
   weight-tuning cycle, propose a new cron schedule, escalate to operator
   via AgentDM.
5. Has a hard daily LLM budget cap (suggest: $30/day initially, raise after
   stable behavior is observed).

### Tier D -- Cron self-resurrection

A `cron_supervisor` module that:

- Reads `register_health_check` registrations + `@track_sla` target_ms
  values for every cron module.
- Polls each cron's last-success timestamp every 60s.
- If `last_success_age > 3 * expected_interval`:
  - Attempt soft-restart (re-import + re-spawn the cron's start_loop).
  - If 2 soft-restarts fail, fire anomaly_dispatcher with
    `category: cron_failure`, severity HIGH.
  - If 4 hours of HIGH-severity, escalate operator-DM.
- Logs every restart to `db.self_heal_actions` with
  `action_type: cron_resurrection`.

### Tier E -- Memory of what was tried

`db.self_heal_actions` collection:

```
{
  _id: sha(failure_signature + attempted_fix),
  failure_signature: "fmcsa_safer_cargo_cron:track_sla_breach",
  attempted_fix: "soft_restart" | "config_reload" | "patch:abc123" | ...,
  tried_at: iso8601,
  outcome: "success" | "no_effect" | "regression",
  outcome_observed_at: iso8601,
  retry_count: int,
  next_eligible_retry_at: iso8601,
  human_escalation_at: iso8601 | None
}
```

Pattern recognition rules:
- If `retry_count >= 3` and `outcome != success`, future occurrences of
  this `failure_signature` skip the LLM dispatch and route directly to
  human escalation.
- If `outcome == regression`, blacklist the attempted_fix for this
  failure_signature for 30 days.
- If a different `attempted_fix` succeeded for the same
  `failure_signature` previously, use that fix first on the next occurrence
  (cached strategy).

## 5. Risks + mitigations

### 5.1 Auto-applied bad change ships a regression

- **Mitigation A**: Tier A class boundaries -- only GREEN gets auto-applied,
  and GREEN is intentionally narrow.
- **Mitigation B**: Tier B rollback metric watch -- 30-min watch window
  with auto-revert on health regression.
- **Mitigation C**: tests-pass-required -- pytest must run + pass on the
  worktree-isolated patch before merge. Suggested: targeted-suite pytest
  (modules touched by the diff) + fast smoke suite, NOT full suite (cost).

### 5.2 LLM hallucinates a patch that "looks right" but breaks runtime

- **Mitigation A**: tests-pass-required.
- **Mitigation B**: dependency review -- if the patch adds a new import,
  upgrade trust-gate to YELLOW automatically.
- **Mitigation C**: blast-radius classifier -- count how many functions
  call into the changed lines; >5 callers upgrades to YELLOW.
- **Mitigation D**: forbidden-string regex -- patches that contain
  `db.payments`, `factor_`, `auth_`, `session_`, `insurance_`, `fmcsa_`,
  `stripe_` keywords auto-classify RED regardless of LOC delta.

### 5.3 LLM cost explosion

- **Mitigation A**: existing `integrity/budget_caps.py` -- per-day cap per
  category. Tier C embedded loop adds a new category
  `llm_self_healing_loop` with default $30/day.
- **Mitigation B**: anomaly cooldown -- a fingerprint that fires the
  dispatcher cannot re-queue for 24h. Already implemented in
  anomaly_dispatcher.py.
- **Mitigation C**: budget exhaustion fallback -- once daily cap is hit,
  the loop drops to read-only-reasoning mode (no dispatches) until
  midnight UTC.

### 5.4 Loss of operator visibility ("AI changed something and I don't know")

- **Mitigation A**: `db.self_heal_actions` log + frontend Self-Heal tab
  showing every auto-applied change, every rollback, every escalation.
- **Mitigation B**: Daily AgentDM summary to `@pm-lead` and operator:
  "Yesterday: N GREEN auto-applied (M rolled back), K YELLOW pending dev
  review, J RED awaiting operator."
- **Mitigation C**: Every commit message authored by the platform is
  prefixed `[selfheal:GREEN]` / `[selfheal:YELLOW]` / etc. Operator can
  always grep git log for self-applied changes.
- **Mitigation D**: kill-switch -- `CHL_SELFHEAL_DISABLED=true` env var
  pauses all auto-merge; existing pattern from
  `integrity/operational_state.py`.

### 5.5 Regulatory exposure (FMCSA / IRS / insurance data integrity)

- **Mitigation A**: RED-tier never auto-applies. Files matching
  `fmcsa_*`, `insurance_*`, `bond_*`, `tax_*`, `payment_*`, `auth_*` are
  hardcoded RED. Forbidden-string regex (Mitigation 5.2.D).
- **Mitigation B**: a separate operator-only allowlist for any RED
  auto-merge -- defaults empty, operator must opt-in per file path.
- **Mitigation C**: FMCSA data writes are append-only / event-sourced;
  even if a self-applied bug corrupts a write, the source-of-truth
  collection (e.g., db.fmcsa_census_raw) is untouched + can re-derive.

### 5.6 Self-applied change creates a feedback loop with another self-applied change

- **Mitigation A**: Tier E memory -- if changes A and B alternately
  trigger each other's regression watch, mark the failure signature as
  "ping-pong" and human-escalate.
- **Mitigation B**: rate-limit total self-merges per day (suggest: max 5
  GREEN auto-applies per day; configurable; raise after stable).

### 5.7 Patch tries to modify the self-healing system itself

- **Mitigation A**: hard-block path allowlist -- patches that touch
  `backend/self_healing/`, `backend/integrity/`, `backend/health_monitor.py`,
  `backend/sla_monitor.py`, `backend/cron_scheduler.py` are forced RED
  + require operator + dev double-approval.

## 6. Recommended phasing

### Phase 1 (1 sprint, lowest risk, biggest immediate operational value)

- **Tier D cron self-resurrection** -- soft-restart failed crons; alerts on
  persistent failure. Doesn't modify code; only restarts processes.
- **Tier E memory log** -- `db.self_heal_actions` collection + write paths
  from existing self-healing modules. Doesn't change behavior; just
  records.
- Frontend Self-Heal tab (read-only) showing
  `db.anomaly_dispatch_queue` + `db.patch_proposals` +
  `db.self_heal_actions` from one surface. Operator visibility win.
- Ship `register_health_check` audit -- confirm every cron has a check.

**Why Phase 1 first**: zero new code-mod risk, immediate operational
value (crons that die at 0300 self-restart), and the memory log is
pre-requisite for everything else.

### Phase 2 (2 sprints)

- **Tier A GREEN-class auto-merge** for the documented safe categories:
  - log-level adjustments (`logger.info` -> `logger.debug`)
  - dependency pin bumps (only patch versions, never major/minor)
  - test-fixture seed updates
  - typo fixes in user-facing copy
  - missing-import imports
  - retry-count bumps within bounded range
  - .gitignore additions
- Worktree-isolated patch apply + targeted-pytest harness.
- `[selfheal:GREEN]` commit-message prefix.
- Daily AgentDM summary.

**Why Phase 2 second**: GREEN-class is the ROI sweet spot -- many small
churn changes, low risk, big operator-time savings.

### Phase 3 (2 sprints)

- **Tier C embedded LLM reasoning loop** as a separate NSSM service.
- Cross-source event reasoning.
- AgentDM bridge to operator + `@pm-lead`.
- Budget cap `llm_self_healing_loop` $30/day.

**Why Phase 3 third**: highest value per operator-hour saved AND highest
risk. Needs Phases 1+2 stable + the memory log mature before turning on.

### Phase 4 (1 sprint)

- **Tier B rollback metric watch** wraps everything from Phase 2-3.
- Required `watched_metric` field in every patch proposal.
- 30-min auto-rollback window.
- `db.self_heal_actions` action_type: `rollback`.

**Why Phase 4 last**: rollback machinery is only useful once there are
self-applied changes to roll back. Phase 4 is "wrap the whole system in
safety" -- shipping it earlier wastes effort because Phase 1-3 don't yet
generate enough self-applied actions to justify it.

### Total: 6 sprints

Realistic with single dev + delegation; can compress to 4 sprints with
parallelization (Phase 1 + Phase 4 scaffolding can run concurrent).

## 7. Operator-decision items (no code change until decided)

### 7.1 Phase authorization scope

- (a) Phase 1 only -- safest, ships visibility + cron resurrection.
- (b) Phase 1 + 2 -- adds GREEN auto-merge.
- (c) Phase 1-3 -- adds embedded LLM reasoning loop. **Recommended.**
- (d) All four -- full self-healing stack.

Dev recommendation: **(c)** initially, ship Phase 4 only after Phase 1-3
is observed stable for 2-4 weeks (long enough to see real self-applied
churn at GREEN scale).

### 7.2 Daily LLM budget cap for self-heal

- Current default `llm_self_healing` cap: $15/day.
- Phase 3 adds embedded loop -- proposed new category
  `llm_self_healing_loop` with $30/day.
- **Operator decides**: keep $15+$30 = $45/day combined? Raise both?

Dev recommendation: **$15 + $30 = $45/day combined** for the first month,
review after we see actual burn rate.

### 7.3 Hard-"never" classes (operator-only forever)

Suggested file-path allowlist that NEVER auto-applies regardless of
trust-gate:

- `backend/integrity/*` -- the kill-switch and audit infrastructure
- `backend/self_healing/*` -- the self-heal system itself
- `backend/health_monitor.py`, `backend/sla_monitor.py`,
  `backend/cron_scheduler.py` -- the watchers
- `backend/payment_rails.py`, `backend/factors/*` -- carrier-AP money flow
- `backend/auth*.py`, `backend/session*.py` -- auth
- `backend/fmcsa_*.py`, `backend/insurance_*.py` -- regulator-facing
- `backend/server.py:**stripe**`, `backend/stripe*.py` -- payments
- `backend/integrity/budget_caps.py` -- the LLM budget gate itself
- Mongo replica config / database config files
- `.env`, `secrets.toml`, anything matching `*credentials*`, `*api*key*`

**Operator decides**: confirm this list + add or remove paths.

### 7.4 Rollback escalation policy

Three options when Tier B detects a regression:

- (a) Auto-rollback + notify operator (no recovery attempt).
- (b) Auto-rollback + recovery-attempt (try alternate fix from Tier E
  cache; if no cached strategy, escalate).
- (c) Require operator approval to rollback.

Dev recommendation: **(a)** for Phase 1-3, **(b)** as a Phase 4 add-on once
the memory log is mature.

### 7.5 Test-suite scope on patch verification

Patch verification cost vs coverage tradeoff:

- (a) Targeted suite -- only test files importing the modified files.
  Cheap (~10s), might miss cross-module breaks.
- (b) Targeted + smoke -- targeted plus a small "always run" smoke suite.
  Medium cost (~30s).
- (c) Full suite. Comprehensive but slow (~5min) -- might bottleneck
  Phase 2 throughput.

Dev recommendation: **(b)** targeted + smoke; bump to (c) on YELLOW class.

### 7.6 Operator-handed exceptions

These remain operator-only forever per existing standing directives
(`feedback_autonomous_continue_until_build_complete.md`):

- Hardware install
- External vendor signups (RTS / OTR / Apex / Triumph onboarding)
- Cost decisions >$20/wk pre-revenue
- High-blast-radius interactive ops
- Scope expansion

These are independent of the self-heal phasing.

## 8. Implementation order within Phase 1 (suggested batch breakdown)

### Phase 1 batch A (~3 days)

- New collection `db.self_heal_actions` with indexes
  on (failure_signature, tried_at) + (status, next_eligible_retry_at).
- Write helper `record_self_heal_action(db, ...)` patterned on
  `payment_rails.transition_payment_status`.
- Wire existing self_healing modules to write to this collection on every
  dispatch + on every weight-proposal cycle.
- Tests + verifier sub-agent over the diff.

### Phase 1 batch B (~3 days)

- `cron_supervisor` module (new file) using
  `register_health_check` + `@track_sla` registry.
- 60s polling loop, 3x-target_ms threshold for soft restart.
- Soft restart = re-import + re-call start_loop. Hard restart = NSSM
  service restart (require admin elevation; degrades to operator-DM if
  not elevated).
- Tests for restart logic, race conditions, double-restart prevention.

### Phase 1 batch C (~2 days)

- New frontend tab `/self-heal` aggregating:
  - `GET /api/_self_healing/dashboard` (new) -- joins
    `anomaly_dispatch_queue` + `patch_proposals` +
    `self_heal_actions` + `evaluator_weight_proposals` into one view.
  - Operator can filter by severity, source, status.
  - Read-only. No approve/reject buttons (those stay in existing
    tab-specific views).

### Phase 1 batch D (~2 days)

- `register_health_check` audit script -- list every cron module that
  registers a check; flag missing ones.
- Add health checks to any cron missing one.
- Integration test: start backend, hit `/api/health/system`, assert N
  modules report.

### Phase 1 verifier-gate

Sub-agent code-reviewer over batches A+B+C+D combined catches cross-module
contract drift per `feedback_verifier_gate_pattern.md`.

## 9. Cross-references

- `backend/self_healing/__init__.py`
- `backend/self_healing/anomaly_dispatcher.py`
- `backend/self_healing/sub_agent_dispatcher.py`
- `backend/self_healing/patch_proposal_router.py`
- `backend/self_healing/weight_tuning_proposer.py`
- `backend/self_healing/weight_tuning_router.py`
- `backend/self_healing/outcome_tracker.py`
- `backend/health_monitor.py`
- `backend/system_health_monitor.py`
- `backend/sla_monitor.py`
- `backend/integrity/operational_state.py`
- `backend/integrity/budget_caps.py`
- `backend/integrity/audit_trail.py`
- `backend/integrity/circuit_breakers.py`
- `backend/failure_detectors.py` + `failure_detectors_part2.py`
- `backend/inbox_triage/triage_classifier.py`
- `backend/renewals/renewals_*.py`
- `chl-memory/research/phase_6_self_healing_roadmap.md` (assumed
  pre-existing; referenced by self_healing/__init__.py)
- `~/.claude/projects/c--CHL/memory/feedback_autonomous_continue_until_build_complete.md`
- `~/.claude/projects/c--CHL/memory/feedback_verifier_gate_pattern.md`
- `~/.claude/projects/c--CHL/memory/project_platform_north_star.md`

## 10. Summary

CHL has roughly 60% of a self-healing platform shipped: detection,
diagnosis, propose-only patch generation, weight-tuning proposals, health
monitoring, kill-switches, audit trail, budget caps. The 40% gap to true
self-modification is:

- Auto-merge of approved patches (currently 0%; deterministic-rule-gated
  GREEN class is achievable).
- Self-rollback metric watch (currently 0%; required before any auto-merge
  is safe).
- Cron self-resurrection (currently 0%; lowest-risk highest-value first
  ship).
- Self-heal action memory (currently 0%; pre-requisite for not retrying
  failed strategies).
- Embedded continuous LLM reasoning loop (currently 0%; highest value
  per operator-hour saved, highest risk).

Recommended path: Phase 1 (cron resurrection + memory log + frontend tab)
in 1 sprint -- ship immediately. Phase 2 (GREEN auto-merge) in 2 sprints
once Phase 1 is stable. Phase 3 (embedded loop) in 2 sprints. Phase 4
(rollback wrapper) in 1 sprint after Phase 1-3 generates real self-applied
churn.

The patch-suggestion-only state operator is rejecting today is a temporary
gate; the architecture to push past it exists in pieces; the work is
sequencing those pieces into a safety-by-default rollout that doesn't put
the platform at risk while saving operator hours.

End of advisory.
