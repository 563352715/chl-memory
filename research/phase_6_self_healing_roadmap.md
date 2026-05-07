# Phase 6+ Self-Healing Infrastructure Roadmap

> **Source:** PM Claude (`@pm-lead`) drafted 2026-05-07 per @dev-engineer task brief. North-star pillar: operator's 2026-05-07 directive — *"The design of this platform is to create an autonomous system that self heals and learns. I will need AI embedded into it to monitor and write code while the system is being used."*
> **Status:** Forward-looking architecture sketch (not a binding agenda). Designs the **detect → diagnose → patch → verify → learn** loop on top of CHL's existing Phase 7 throttle/health foundation. Full agenda when operator authorizes.
> **Distinct from:** the FMCSA auto-refresh architecture (4 crons wired today per CHL `501268a`) — that's about external data freshness; **this** is about platform self-healing of internal code + behavior. Cross-ref bidirectionally if/when an auto-refresh architecture spec doc lands in `chl-memory/research/`.

---

## Executive Summary

CHL today already has the **detection** half of self-healing in production: `health_monitor` reports module liveness across 14+ modules, `sla_monitor` tracks per-operation latency + miss-rate, `throttle_system` gates intake on degraded health (GREEN/YELLOW/ORANGE/RED), `failure_detectors.py` covers 12 documented business-logic failure modes, `circuit_breaker.py` auto-trips on per-API failure rate, and the new `client_error_logger` (CHL `cb2f15f`) captures frontend runtime errors. The gap is everything downstream of detection: when a novel anomaly fires today, a human (operator OR @dev-engineer Claude session) is the diagnosis-and-patch path. **This roadmap closes that gap with four new layers** — anomaly-routing dispatcher (auto-classifies events + dispatches Claude sub-agents with bundled context), patch-proposal pipeline with explicit LOW/MED/HIGH trust-gate matrix (LOW auto-merge, MED dev-DM, HIGH operator-approval), patch-specific smoke verification with auto-rollback, and outcome-feedback wiring back into the FractalEvaluator architecture (LoadEvaluator / LaneEvaluator / future CarrierEvaluator) so the learning pillar fires from production reality. **Trust-gate-with-operator-in-loop is intentional**: pre-revenue solo cannot absorb a self-deploying-LLM mistake, so HIGH-risk patches stop at "proposed + smoke-verified" and require operator promotion. The first iter (proposed: 145.1 Self-Healing Foundation, 4 stages, ~15h dev, ~20 smoke tests) builds the dispatcher + context bundler + trust-gate stub + outcome-feedback schema; subsequent iters fill in the patch-drafter, sandboxed verification, auto-rollback, and re-tuning cron.

---

## 1. Detection Layer

**Question this layer answers:** what observation surfaces feed an "anomaly detected" event?

### Existing primitives (BUILT, per `capability_inventory.md`)

- **`health_monitor`** — `/api/health/system` aggregates per-module status across 14+ registered modules (lane_evaluator, lane_scoring_cron, throttle_manager, sla_monitor, bmc84_watcher, etc.). Each module self-registers a health check; aggregate returns `{total, healthy, degraded, unhealthy, modules: [...]}`.
- **`sla_monitor`** — per-operation latency + miss-rate tracking via `@track_sla(name, target_ms)` decorator. Targets registered in `sla_targets.py`. `/api/sla/{summary,violations,operation/{name}}` exposes time-series + violation events.
- **`throttle_system`** — 4-state machine (GREEN ≥100% intake, YELLOW 50%, ORANGE 10%, RED 0%) gated on aggregate health + SLA-miss-rate. `auto_dispatch.try_auto_accept` reads throttle pre-accept; skips on not-GREEN. FAIL-OPEN if manager not initialized (never crash dispatcher).
- **`failure_detectors.py` + `failure_detectors_part2.py`** — 12 documented business-logic failure modes (GPS-silent, no-show, factor-decline, BOL-mismatch, detention overage, ghosting, double-broker, bankruptcy, lumper shortage, reweigh, cargo-claim, authority-lost). Each fires SMS/email alert + writes `db.failure_events` row.
- **`circuit_breaker.py`** — per-external-API auto-trip on failure-rate threshold; auto-reset on success streak. Fail-fast pattern.
- **`client_error_logger`** (CHL `cb2f15f`, 2026-05-07) — frontend `window.onerror` + React error boundary → `POST /api/_client_errors` → `C:/CHL/logs/client_errors.log`. Currently a write-only sink awaiting analyzer (`client_error_analyzer` cron in flight per dev's sub-agent).
- **Audit-trail collections** (already populated, currently human-read-only): `auto_accept_log`, `circuit_trip_log`, `repost_pulses`, `billing_dispatches`, `auto_accept_log`. These contain the substrate for novel-pattern detection.
- **`bmc84_watcher`** — auto-clears FMCSA "PENDING" banner; example of an autonomous remediation-loop primitive that's already in production.

### Gaps — proposed new primitives

- **Outcome tracker on completed loads** — `db.load_outcomes` populated post-delivery: `{load_id, margin_predicted, margin_actual, carrier_predicted_reliability, carrier_actual_reliability, eta_predicted, eta_actual, exception_count, operator_intervention_count}`. Surface for the Learning Layer §5 re-tuning logic.
- **Factor-payment-latency tracker** — `db.factor_payment_events` per factor company: `{factor_id, invoice_submitted_at, payment_received_at, latency_hours, amount}`. Detects degrading-factor-relationship anomalies (e.g., factor company starting to slow-pay → predictive of upcoming financial stress).
- **Carrier-no-show rate tracker** — derived view over `db.failure_events` filtered to no-show + assigned-carrier-mc. Updates `db.carriers.reliability_score` via Learning Layer feedback.
- **Novel-anomaly fingerprint detector** — for client errors (and SLA misses, throttle state changes), maintain a fingerprint registry. Anomaly = novel fingerprint OR known fingerprint exceeding rate-of-occurrence threshold. Fires `db.anomalies` row with classification metadata.
- **Cross-system event correlator** — multi-signal anomaly: e.g., throttle YELLOW + 3 SLA misses + 5 novel client errors within a 5-min window → "system stress" composite event with severity `critical`. Single Claude diagnosis dispatched for the composite, not 9 separate dispatches.
- **Outcome variance over time** — for any evaluator (Load/Lane/Carrier/future Macro), if 7-day rolling MAE between predicted-and-actual exceeds threshold → "evaluator drift" anomaly fires.

### Iter-stage mapping

| Proposed primitive | Target iter | Dependency / blocker |
|---|---|---|
| Outcome tracker (`db.load_outcomes`) | iter 142.x or 143.x | After first ~10 real loads complete (provides initial calibration data) |
| Factor-payment-latency tracker | iter 144.x (Phase 6 factoring) | Phase 6 factoring integration |
| Carrier-no-show rate tracker | iter 142.2 (Phase 4 tracking) | Tracking-derived signal |
| Novel-anomaly fingerprint detector | iter 145.1 stage 1a | This roadmap's first stage |
| Cross-system event correlator | iter 145.x or 146.x | Builds on fingerprint detector |
| Outcome variance over time | iter 145.x | Builds on outcome tracker + evaluator predictions stored |

---

## 2. Diagnosis Layer

**Question this layer answers:** when an anomaly fires, what dispatches a Claude sub-agent to investigate? Where does it run? What context does it get?

### Existing primitives

- **`auto_dispatch.try_auto_accept` pattern** — 8 hard rules + audit trail (`auto_accept_log`). Example of rule-engine dispatch with full provenance per decision. Pattern reusable for "anomaly → diagnosis dispatch" classification.
- **`lane_scoring_cron` pattern** — 6h scheduled background work, throttle-respecting (skip on not-GREEN), `@track_sla` instrumented, async Motor cursors, self-registered health check. The canonical pattern for autonomous background work in CHL.
- **AgentDM Tier 1** (`mcp__agentdm__send_message` / `read_messages`) — bridge between @dev-engineer (CHL backend Claude) and @pm-lead (PM Claude). Already exercises AI-to-AI coordination in production. Can extend to anomaly-diagnosis sub-agents.
- **Sub-agent dispatch** (Claude Agent SDK or anthropic library directly via `backend/`) — dev-engineer is already using sub-agents for FMCSA scrapers, doc audits, etc. (see today's PM-TASK-A/B/C sub-agent dispatches).

### Gaps — proposed new primitives

- **`backend/anomaly_dispatcher.py`** — subscribes to anomaly events (Detection Layer output). For each event:
  - Classifies into 6 categories: **PERF** (SLA miss), **DATA** (Mongo/integration data shape unexpected), **INTEGRATION** (external API failure), **UI** (client error), **SECURITY** (auth-related), **BUSINESS-LOGIC** (failure_detector fired).
  - Looks up category-specific routing rules (e.g., PERF → check throttle state + recent commits to slow module; UI → fingerprint fresh client error + check React boundary stack)
  - Dispatches a Claude sub-agent with bundled context (see `anomaly_context.py` below)
  - Logs dispatch + result to `db.diagnoses` with `{anomaly_id, category, sub_agent_id, context_size_tokens, diagnosis_summary, confidence, risk_class, completed_at}`
- **`backend/anomaly_context.py`** — for each anomaly type, gathers relevant context bundle:
  - Source-code files matching the anomaly's module/file fingerprint (via grep + AST + recent-edit recency)
  - Recent commits touching those files (`git log -p --since="7 days ago"`)
  - Recent throttle/SLA/failure-detector history (last 24h slice)
  - Related audit-trail rows from the relevant collection
  - For data anomalies: schema sample + Mongo aggregation summary
  - Token-budget cap: 50K tokens per dispatch (configurable; soft alarm at 70%, hard cap at 100%)
- **Trust scoring on diagnosis output** — each Claude diagnosis returns:
  - `confidence: 0.0-1.0` (Claude's self-reported certainty)
  - `risk_class: "low" | "med" | "high"` (based on file paths touched, code domain, dollar amount affected)
  - `recommended_action: "auto_patch" | "dev_dm_review" | "operator_approval"` (gated by trust-gate matrix from §3)
- **Anomaly debounce + de-dup** — fingerprint hash on incoming anomalies; >3 occurrences in 1h are bundled into one diagnosis dispatch (don't fire 60 sub-agents on a flapping signal). Per-day rate limit on total dispatches (default: 50/day; operator override).

### Iter-stage mapping

| Primitive | Target iter | Notes |
|---|---|---|
| `anomaly_dispatcher.py` | iter 145.1 stage 1a | Foundational |
| `anomaly_context.py` | iter 145.1 stage 1b | Depends on dispatcher |
| Trust scoring framework | iter 145.1 stage 1c | Combined with §3 trust-gate |
| Debounce + de-dup | iter 145.1 stage 1a (built-in) | Critical to ship simultaneously with dispatcher to avoid runaway costs |

---

## 3. Patch-Proposal Layer

**Question this layer answers:** once diagnosed, how does the agent draft a code patch? What's the trust-gate framework that decides what auto-merges vs. what waits for human review?

### Existing primitives

- **Manual override endpoint pattern** — universal endpoint for force-accept, waive-margin-floor, force-carrier-pay (per `capability_inventory.md`). Example of "human-in-the-loop override of automated decision" — the inverse pattern of what we need here, but the audit shape is reusable.
- **Audit-trail collections** — every consequential decision is logged with provenance. Patches must follow the same shape (`db.patches` collection — see below).
- **Git as the patch ledger** — every patch is a commit; rollback = git revert. The CHL repo's commit history is already the substrate for patch tracking.
- **PM-side existing pattern** — @pm-lead routinely amends in-place (see iter 142.1 agenda Load-Board-First amendment). Self-healing extends this from doc-amend to code-amend with the trust-gate.

### Gaps — proposed new primitives

- **Patch drafter (Claude sub-agent)** — given a diagnosis output, drafts a single-file diff. Constraints:
  - Single-file edits only at v1 (multi-file diffs require operator approval regardless of risk class — too easy to introduce subtle inter-file bugs)
  - Diff is small: <50 LOC change, <200 LOC of context
  - Includes test/smoke addition if applicable (patch should ship with the test that proves it fixed the anomaly)
  - Returns `{patch_id, files_touched, diff_unified, smoke_tests_added, risk_class, confidence, anomaly_id}` written to `db.patches`
- **Trust-gate matrix** — explicit risk classification rules. Default v1 rules (operator-tunable):

| Risk class | Auto action | Boundary |
|---|---|---|
| **LOW** | Auto-merge to a `self-healing/` branch + auto-deploy + smoke verify | Files: `*.md`, copy-text changes (error messages, log strings), threshold tunings within ±10% of current value, logging additions, comment changes. Domain: NOT in `*auth*`, NOT in `*billing*`, NOT in `*payment*`, NOT in `*.env*` |
| **MED** | Patch + smoke verify in sandbox; AgentDM @dev-engineer with patch + diagnosis context for review | Files: new defensive null/empty checks, retry logic adjustments, schema field additions (non-destructive), new logging instrumentation. Domain: anywhere except HIGH-risk file paths |
| **HIGH** | Patch + smoke verify in sandbox; AgentDM operator (and dev-engineer) with diagnosis + patch + risk rationale; **operator-approval-required before promotion** | Files: `*auth*.py`, `*billing*`, `*payment*`, `*.env*`, business-logic core (`auto_dispatch.py`, `auto_quote_draft.py`), schema migrations (anything renaming/dropping Mongo fields), trust-gate matrix itself, prompt templates that affect customer-facing copy |
| **REJECTED** | No patch; mark anomaly for human investigation | Out-of-scope domains (e.g., dependencies, OS-level config), confidence <0.5, multi-file diff, inability to produce a smoke test |

- **Patch metadata schema** (`db.patches`):
  ```
  {
    patch_id: str,
    anomaly_id: str (FK to db.anomalies),
    diagnosis_id: str (FK to db.diagnoses),
    files_touched: [str],
    diff_unified: str,
    smoke_tests_added: [str],
    risk_class: "low" | "med" | "high",
    confidence: float,
    proposed_by: "claude_self_healing_v1" (or sub-agent identifier),
    proposed_at: datetime,
    review_status: "auto_merge_pending" | "dev_dm_pending" | "operator_approval_pending" | "approved" | "rejected" | "merged" | "rolled_back",
    review_decision_at: datetime,
    review_decision_by: str (sub-agent or human alias),
    review_notes: str
  }
  ```

- **Branch convention**: all self-healing patches land first in `self-healing/<patch_id>` branch. LOW-risk auto-merges to `main` after smoke verify. MED/HIGH stay on the branch until reviewer (dev or operator) merges via UI/CLI.

### Iter-stage mapping

| Primitive | Target iter | Notes |
|---|---|---|
| Trust-gate matrix (v1 stub) | iter 145.1 stage 1c | Foundational; ships with stub patch-drafter that always returns "manual review required" |
| Patch drafter (real implementation) | iter 146.x | After trust-gate matrix proven in stub-mode |
| Patch metadata schema | iter 145.1 stage 1c | Combined with trust-gate |
| Branch convention + auto-merge tooling | iter 146.x | Can be a separate stage after patch-drafter |

---

## 4. Verification Layer

**Question this layer answers:** how does the system verify a patch worked before promoting it past the trust boundary? Smoke harness + rollback plan.

### Existing primitives

- **Per-stage smoke pattern** — every iter stage closes with `N/N smoke PASS` (committed to `chl-memory/progress/stage_completion/`). Pattern: targeted tests for that stage's behavior, run in-process or against synthetic data.
- **Synthetic load spawning** — `fake-load-at-stage` testing fixture (per `capability_inventory.md`). Lets the system create synthetic loads at any state in the lifecycle for testing without live setup.
- **Synthetic auto-wipe** — test data has 24h TTL. Already protects production from synthetic-data-bleed.
- **D: backup snapshots** — each iter close runs `backup_to_d.ps1`. Rollback insurance at the data layer; git history covers code rollback.
- **Throttle FAIL-OPEN posture** — never crashes the dispatcher. Even a bad patch in throttle code leaves intake working at 100% (degrades to "no throttle gating" rather than "no intake").
- **D: drive snapshot pattern** — already an iter-close ritual; can be extended to pre-patch snapshot for self-healing patches.

### Gaps — proposed new primitives

- **Patch-specific smoke battery generator** — given a patch's `files_touched` + diff context, generates targeted smoke tests:
  - For each function modified, smoke = pre-patch behavior on golden inputs vs post-patch behavior (regression check)
  - For each new code path, smoke = positive case + negative case + error path
  - For each schema change, smoke = backward-compat check (existing data still reads correctly) + forward-compat check (new data writes correctly)
  - Generator is itself a Claude sub-agent dispatched after patch-drafter; failure modes (can't generate test, can't reproduce anomaly) → patch downgraded to REJECTED
- **Sandboxed verification** — patch runs against synthetic-load fixtures BEFORE production promotion:
  - Spawn synthetic loads at relevant lifecycle states
  - Apply patch in a docker-isolated or Mongo-namespace-isolated sandbox
  - Run patch-specific smoke battery
  - Capture: PASS/FAIL count, latency, any new error logs
  - Promotion gate: 100% smoke PASS + no new error logs + latency within ±20% of baseline
- **Auto-rollback hook** — for LOW-risk auto-merged patches:
  - Post-merge, monitor `health_monitor` + `sla_monitor` + `client_error_logger` for 30 min
  - If any new degradation OR any error fingerprint that didn't exist pre-patch fires → auto-revert via `git revert`
  - Log rollback to `db.patches` with rollback reason; flag patch as `rolled_back` for post-mortem
- **Rollback ledger** — `db.rollbacks` tracks every revert: `{patch_id, rollback_reason, rolled_back_at, rolled_back_by, post_mortem_diagnosis_id}`. Operator-visible dashboard tile: "self-healing patches rolled back this week" → indicates self-healing system itself needs tuning.
- **Pre-patch backup hook** — for any HIGH-risk patch, trigger D: snapshot before promotion (~2 min wall). For MED-risk, snapshot if patch touches Mongo schema (regardless of risk class on file path). For LOW-risk, daily snapshot is sufficient.

### Iter-stage mapping

| Primitive | Target iter | Notes |
|---|---|---|
| Patch-specific smoke generator | iter 146.x or 147.x | Depends on real patch-drafter |
| Sandboxed verification | iter 146.x | Depends on smoke generator |
| Auto-rollback hook | iter 146.x or 147.x | Critical safety net for LOW-risk auto-merge |
| Rollback ledger | iter 146.x | Builds on auto-rollback |
| Pre-patch backup hook | iter 146.x | Reuses existing `backup_to_d.ps1` |

---

## 5. Learning Layer

**Question this layer answers:** how does outcome data feed back into the FractalEvaluator architecture (LoadEvaluator / LaneEvaluator / future CarrierEvaluator) so the system actually improves over time?

### Existing primitives

- **FractalEvaluator architecture** (per `FRACTAL_DECISION_FRAMEWORK.md` + iter 141.1 stage 1a) — abstract pattern at three scales (micro=Load, meso=Lane, macro=Market) using the same 4 parameters (profitability, reliability, risk, capacity) recursively.
- **`LoadEvaluator` (shadow-mode)** — runs in `auto_dispatch.try_auto_accept` for telemetry without behavior change. Already produces predictions; just doesn't measure outcomes today.
- **`LaneEvaluator`** — meso-scale, populates `db.lane_scores` via `/api/lanes/{o}/{s}/{d}/{s}/score` endpoint + 6h `lane_scoring_cron`. Composite formula: `(profitability × reliability) / max(risk, 0.01)`.
- **`db.lane_scores`** — populated by both endpoint calls + cron ticks. Ready substrate for outcome-feedback (just needs `outcomes` field added).
- **`business_settings.lane_thresholds`** — operator-tunable thresholds. Foundation for "system proposes new threshold based on outcome data, operator approves/overrides".
- **CarrierEvaluator (planned, iter 142.1 stage 1c)** — when shipped, uses the same fractal pattern at meso scale. Should ship WITH outcome-feedback hooks from day one (instead of retrofit).

### Gaps — proposed new primitives

- **`db.evaluator_outcomes` collection** — per-evaluator-prediction outcome record:
  ```
  {
    outcome_id: str,
    evaluator_class: "load" | "lane" | "carrier" | "macro",
    scored_entity_id: str (load_id, lane_key, mc_number, market_segment),
    predicted_score_at: datetime,
    predicted_composite: float,
    predicted_params: {profitability, reliability, risk, capacity} (the 4-param breakdown at prediction time),
    actual_outcome_observed_at: datetime,
    actual_composite: float (computed from realized data),
    actual_params: {profitability, reliability, risk, capacity},
    feedback_horizon_days: int (e.g., 7 for short-term load outcomes; 30 for lane drift; 90 for market drift),
    score_delta: float (actual - predicted; surface for variance tracking)
  }
  ```
- **Outcome observers** — hooks at lifecycle endpoints that populate `db.evaluator_outcomes`:
  - On load completion (POD verified, payment received): write `evaluator_class: "load"` outcome with realized margin + on-time + claim-rate
  - On lane refresh cron (weekly): write `evaluator_class: "lane"` outcome with rolling 7-day actuals
  - On carrier dispatch outcome (post stage 142.1 1e): write `evaluator_class: "carrier"` outcome with assignment success + delivery timeliness
- **Re-tuning cron** — weekly job, throttle-respecting, `@track_sla`:
  - For each evaluator class, computes 7-day rolling MAE between predicted and actual composites
  - If MAE > threshold (default: 0.15 absolute), proposes a parameter weight re-tune (Bayesian update, not full retrain — avoids overfitting at small sample sizes)
  - Re-tune proposal goes through trust-gate (likely MED-risk: dev-DM review for the first ~3 months; operator can promote LOW-risk after stability proven)
  - Logs to `db.evaluator_retunes` with `{evaluator_class, old_weights, proposed_weights, mae_pre, mae_post_estimate, sample_size, proposal_id}`
- **Predicted-vs-actual operator dashboard** — new tile: per-evaluator 7-day prediction accuracy (MAE + sample size + last-retune date). Operator sees at a glance whether the system is actually learning or drifting.
- **A/B testing framework** — shadow-mode evaluator variants: when re-tune proposed, run both old and new weights against the next ~50 predictions, compare MAE before promoting new weights. Critical for avoiding "we re-tuned based on a bad week of data" failure mode.

### Iter-stage mapping

| Primitive | Target iter | Notes |
|---|---|---|
| `db.evaluator_outcomes` schema | iter 145.1 stage 1d | Foundational |
| Outcome observers (Load) | iter 142.x or 143.x | After first ~10 real loads complete |
| Outcome observers (Lane) | iter 145.1 stage 1d | LaneEvaluator already shipped; just needs hook |
| Outcome observers (Carrier) | iter 142.1 stage 1c | Ship WITH CarrierEvaluator, not retrofit |
| Re-tuning cron | iter 145.x or 146.x | Needs ≥30 days outcome data for first re-tune |
| Predicted-vs-actual dashboard | iter 145.1 stage 1d | Operator visibility from day one |
| A/B testing framework | iter 146.x | Mature stage; safety net for re-tune-gone-wrong |

---

## 4-Stage Iter Outline (chopping block first)

**Proposed iter: 145.1 — Self-Healing Foundation**

> Pre-Phase-6 work that builds on Phase 7 (throttle/health) foundation. Total ~15h dev, ~20 smoke tests, ~30 min operator hands-on. Prerequisites: iter 142.1 Phase 3 carrier network shipped (so we have outcome data flowing); iter 141.3 Plivo migration shipped (so SMS-path Claude-dispatch alerts work).

### Stage 1a: Anomaly-Routing Dispatcher + Debounce (~3h dev, ~5 min operator)

**Goal:** Stand up `backend/anomaly_dispatcher.py` that subscribes to Detection Layer events, classifies into 6 categories, dispatches Claude sub-agents (or stubs them at v1), and writes audit trail. Includes critical debounce + per-day rate limit to prevent runaway costs.

**What operator does (~5 min):**
1. Review 6 anomaly categories + default routing rules in `memory/handoff_iter_145_1_stage_1a.md`
2. Sign-off on per-day token budget cap (default 50 dispatches/day; ~$5-15/day at typical context size)

**What dev does (~3h):**
1. `backend/anomaly_dispatcher.py` — subscribes to Detection Layer events (initially: client_error_logger fingerprints + SLA misses + throttle state changes)
2. 6-category classifier + routing rules
3. Stub sub-agent dispatch (returns "stubbed diagnosis pending §1b context bundler" — exercises the workflow without real Claude calls yet)
4. `db.anomalies` collection + `db.diagnoses` collection (with stub rows)
5. Debounce logic: fingerprint hash, >3 occurrences in 1h bundled into one dispatch
6. Per-day rate limit with operator-alert when 80% reached
7. Health-check registration (`anomaly_dispatcher` module appears in `/api/health/system`)

**Smoke battery (5 tests):**
1. Anomaly with novel fingerprint → fires dispatch, writes db.diagnoses row
2. Anomaly with seen-3-times-in-1h fingerprint → bundled, fires only one dispatch
3. Per-day rate limit at 80% → operator-alert fires
4. Per-day rate limit at 100% → subsequent anomalies queued, not dispatched
5. Health-check returns `anomaly_dispatcher: healthy` with last-dispatch timestamp

**Estimated time:** 3.5h (3h dev + 0.5h operator)
**Handoff doc:** `memory/handoff_iter_145_1_stage_1a.md`

### Stage 1b: Context Bundler (~3h dev, ~5 min operator)

**Goal:** Stand up `backend/anomaly_context.py` that gathers Claude-sub-agent-ready context bundles for each anomaly type. Token-budget aware.

**What operator does (~5 min):**
1. Review token-budget cap (default 50K per dispatch; ~$0.10-0.30 at Claude pricing) in `memory/handoff_iter_145_1_stage_1b.md`
2. Sign-off

**What dev does (~3h):**
1. `backend/anomaly_context.py` — per-category context gather:
   - PERF: `@track_sla` history (last 24h slice) + recent commits to slow module
   - DATA: schema sample + Mongo aggregation summary
   - INTEGRATION: external API call log + circuit-breaker state
   - UI: client-error fingerprint + React boundary stack + recent commits to affected component
   - SECURITY: auth log + recent commits to `*auth*`
   - BUSINESS-LOGIC: failure-detector type + `db.failure_events` rows + recent business-logic commits
2. Token-budget enforcement: gather greedily up to 50K tokens, drop lowest-priority context first
3. Wire into `anomaly_dispatcher.py` — replace stub dispatch with real Claude API call (using existing dev-side Claude API setup)
4. Logs context-bundle metadata to `db.diagnoses` (which files included, total tokens, etc.)

**Smoke battery (4 tests):**
1. PERF anomaly → bundle includes SLA history + recent commits, total <50K tokens
2. UI anomaly → bundle includes React boundary stack + component recent commits
3. Token-budget enforcement: synthetic 100K-token bundle → trimmed to 50K with priority drop
4. Real Claude dispatch end-to-end on a synthetic anomaly → diagnosis returned within 30s

**Estimated time:** 3.5h (3h dev + 0.5h operator)

### Stage 1c: Trust-Gate Framework + Stub Patch-Proposer (~5h dev, ~10 min operator)

**Goal:** Stand up the trust-gate matrix (LOW/MED/HIGH/REJECTED rules) + `db.patches` schema + stub patch-proposer (always returns "manual review required" at v1) + AgentDM integration for MED-risk patches.

**What operator does (~10 min):**
1. Review trust-gate matrix rules in `memory/handoff_iter_145_1_stage_1c.md` — especially the HIGH-risk file/domain list (operator can add files/domains based on their risk tolerance)
2. Sign-off on auto-merge boundary (LOW-risk: docs + log strings + threshold tunings within ±10%; explicitly NOT in `*auth*`/`*billing*`/`*payment*`/`*.env*`)
3. Confirm escalation-to-operator AgentDM channel (default: DM to operator's Plivo SMS post-iter-141.3-cutover; pre-cutover: email-only)

**What dev does (~5h):**
1. Trust-gate matrix as code (`backend/self_healing/trust_gate.py`) — file-path + domain rules
2. `db.patches` schema + indexes
3. Stub patch-proposer that always returns `risk_class: "high"` + `recommended_action: "operator_approval"` (exercises workflow without generating real diffs at v1)
4. Wire stub into `anomaly_dispatcher.py` — diagnosis output → patch-proposer → trust-gate → either AgentDM dispatch or db log
5. AgentDM integration: MED-risk patches → DM to @dev-engineer with patch + diagnosis context. HIGH-risk: DM to operator (via Plivo SMS or email pre-141.3) + DM to @dev-engineer (CC).
6. Endpoint: `GET /api/patches?status=...` (operator-facing list of pending patches)
7. Endpoint: `POST /api/patches/{patch_id}/decision` (operator or dev marks approved/rejected)

**Smoke battery (6 tests):**
1. Mock LOW-risk patch (markdown file change) → trust-gate classifies LOW, would auto-merge (stubbed at v1)
2. Mock MED-risk patch (new defensive null check in non-auth/billing file) → trust-gate classifies MED, fires AgentDM to @dev-engineer
3. Mock HIGH-risk patch (touching `auto_dispatch.py`) → trust-gate classifies HIGH, fires AgentDM to operator
4. Mock REJECTED patch (multi-file diff) → patch-proposer rejects, no trust-gate dispatch
5. `POST /api/patches/{id}/decision` flips review_status, audit-trail logged
6. AgentDM dispatch failure (recipient not registered) → patch flagged for human investigation, doesn't lose the proposal

**Estimated time:** 5.5h (5h dev + 0.5h operator)

### Stage 1d: Outcome-Feedback Schema + Operator Dashboard Tile (~4h dev, ~10 min operator)

**Goal:** Stand up `db.evaluator_outcomes` schema + outcome observer hooks (Lane only at v1, since LoadEvaluator is shadow-mode + CarrierEvaluator not shipped yet) + `/api/dashboard/predicted_vs_actual` endpoint + frontend tile.

**What operator does (~10 min):**
1. Review `db.evaluator_outcomes` schema in `memory/handoff_iter_145_1_stage_1d.md`
2. Visual sign-off on dashboard tile after dev demos (operator confirms predicted-vs-actual MAE per evaluator is rendering correctly)

**What dev does (~4h):**
1. `db.evaluator_outcomes` schema + indexes (compound on `evaluator_class` + `scored_entity_id` + `predicted_score_at`)
2. Lane outcome observer: `lane_scoring_cron` writes `evaluator_class: "lane"` row on each tick (predicted_composite + predicted_params snapshot); separate weekly cron computes actual_composite from realized lane history
3. `/api/dashboard/predicted_vs_actual` endpoint (broker-JWT-protected): returns per-evaluator 7-day MAE + sample size + last-retune date
4. Frontend tile in operator dashboard: rolling MAE chart + sample-size annotation + "evaluator drift" alert if MAE > threshold

**Smoke battery (5 tests):**
1. `db.evaluator_outcomes` schema validation (valid row inserts; invalid evaluator_class rejects)
2. Lane outcome observer: cron tick → row created with predicted_params populated
3. Weekly cron: computes actual_composite from realized data, writes back to row
4. Endpoint returns valid JSON with per-evaluator MAE
5. Frontend tile renders without crash on empty evaluator_outcomes (cold-start case)

**Estimated time:** 4.5h (4h dev + 0.5h operator)

### Iter 145.1 close ceremony

- [ ] Verify smoke totals across stages 1a-1d (**20 tests** total: 5+4+6+5)
- [ ] Write `memory/handoff_iter_145_1_close.md`
- [ ] Run `scripts/backup_to_d.ps1 -IterId "iter_145_1_close"`
- [ ] Update `current_status.md`
- [ ] Operator visual confirmation: synthetic anomaly → dispatcher → context bundler → diagnosis → trust-gate → AgentDM dispatch (full end-to-end exercise)
- [ ] Decide on iter 145.2 (real patch-drafter) or other priority

**Iter 145.1 milestone met when:** synthetic anomaly fires end-to-end through detection → dispatcher → context bundler → diagnosis → trust-gate classification → appropriate AgentDM dispatch (or stub-merge for LOW), with full audit trail in `db.anomalies` + `db.diagnoses` + `db.patches` + dashboard tile rendering.

---

## 5 Sleeper-Flag Items for Operator (pre-kickoff)

### Sleeper 1 — Trust-gate boundary needs operator definition

**Default v1 rules (LOW=auto-merge / MED=dev-DM / HIGH=operator-approval) are provisional.** Operator should override based on their risk tolerance + the specific failure modes that have hurt CHL historically. For example:
- If operator has been bitten by silent threshold-tuning regressions, may want to escalate threshold tunings to MED-risk
- If operator has high trust in dev-engineer's review judgment, may want to expand MED-risk auto-merge rules
- If operator wants extra paranoia on financial code, may want to extend HIGH-risk file paths to include `*invoice*`, `*payment*`, `*ach*`

**Action:** operator reviews stage 1c trust-gate matrix as part of pre-kickoff sign-off. Edits welcome.

### Sleeper 2 — Self-healing IS NOT self-deploying

**The roadmap intentionally stops at "patch proposed + smoke verified" for HIGH-risk.** Operator-in-loop remains for production deploy of HIGH-risk patches. This is by design — pre-revenue solo cannot absorb a self-deploying-LLM mistake.

**Action:** operator should expect that even after iter 145.x ships, HIGH-risk anomalies still wake them up. The system reduces operator-time on routine patches, not the volume of high-stakes decisions. Operator's mental model: "self-healing handles the boring stuff so I can focus on the hard stuff", NOT "self-healing handles everything".

### Sleeper 3 — Outcome data privacy

**When the system records "carrier X delivered late by 47 minutes" + sends that to a Claude sub-agent for evaluator re-tuning, that's per-carrier behavioral data.** Acceptable at v1 single-broker (one operator owns the data + carrier consent via signed BCA); needs explicit consent framework at multi-broker SaaS scale.

**Action:** operator should plan to add a "carrier behavioral data sharing" clause to the BCA template (per iter 142.1 Q6 — currently industry-standard TIA-published template) before scaling beyond single-broker. PM-side can draft the clause as a follow-up task when multi-broker roadmap activates.

### Sleeper 4 — Anomaly dispatcher rate limit + cost ceiling

**Without a rate cap, a flapping signal (e.g., one SLA miss per minute for an hour) could spawn 60 Claude sub-agent diagnoses.** Default debounce: any anomaly fingerprint that fires >3x in 1h is bundled into one diagnosis call. Default per-day cap: 50 dispatches × ~$0.30 each = ~$15/day max anomaly-cost (well within $20/wk operator threshold for routine ops; would burst above on a real incident day).

**Action:** operator confirms per-day cap. If $15/day feels too high, can tighten to 25/day (~$7.50/day) at cost of slower autonomous response on incident-rich days. If too low, can raise — but should set a HARD cap somewhere (defense against runaway loop).

### Sleeper 5 — Sub-agent token budget

**Each diagnosis dispatch = a Claude API call with potentially large context (code files + commit history + throttle state).** At pre-revenue solo, even ~$0.30/diagnosis cumulates. Budget enforcement happens at:
- Per-dispatch: 50K token cap (configurable; soft alarm at 70%, hard cap at 100%)
- Per-day: 50 dispatches cap (sleeper #4)
- Per-month: implicit via dispatcher rate limit (~1500 dispatches/month max = ~$450/month max anomaly-budget)

**Action:** operator should think about anomaly-cost as a separate line item in monthly cost projections. Pre-revenue: caps are aggressive (reduce to 25/day if needed). Post-revenue: caps can grow proportionally to revenue.

---

## Cross-references

- **`chl-memory/CHL_STRATEGIC_PLAYBOOK.md`** — Phase 7 (throttle foundation, COMPLETE per iter 140.1) is the substrate this roadmap builds on. Phase 10 (continuous improvement, ML training) is where the Learning Layer §5 culminates. This roadmap explicitly bridges the gap between Phase 7 (we detect problems) and Phase 10 (we learn from them) by adding diagnosis + patch + verification layers.
- **`chl-memory/FRACTAL_DECISION_FRAMEWORK.md`** — 4-parameter evaluator architecture (profitability / reliability / risk / capacity) at three scales (Load/Lane/Market). **Load-bearing for Learning Layer §5** — outcome data flows back through these evaluators. Re-tuning logic must respect the fractal structure (don't re-tune one scale's weights without considering propagation to adjacent scales).
- **`chl-memory/THROTTLE_PLAYBOOK.md`** — existing self-healing primitive: throttle GREEN/YELLOW/ORANGE/RED state machine with FAIL-OPEN posture. **Detection Layer §1 already references this**; new self-healing patches must respect throttle (don't dispatch on RED — system is already in known-degraded state and likely needs human intervention anyway).
- **`chl-memory/capability_inventory.md`** — BUILT primitives list. Many feed Detection Layer §1 directly (failure_detectors, circuit_breaker, audit-trail collections, bmc84_watcher). When iter 145.1 opens, re-read for fresh primitive inventory (capability_inventory was last refreshed 2026-05-06; will be stale by iter 145.x kickoff).
- **`chl-memory/E2E_LOAD_LIFECYCLE_AUDIT.md`** — where outcome data originates. Load lifecycle is the canonical source for Learning Layer §5 feedback (margin actuals, on-time actuals, exception counts, operator interventions). Re-read at iter 145.1 stage 1d kickoff to confirm outcome observer hook points are still accurate.
- **`chl-memory/agenda/iter_142_1_full.md`** — Phase 3 carrier network, current iter. **CarrierEvaluator (stage 1c) should ship WITH outcome-feedback hooks from day one** rather than retrofit later. Coordinate with this roadmap's Learning Layer §5 schema design.
- **`chl-memory/research/iter_142_2_phase_4_tracking_sketch.md`** — Phase 4 Tracking sketch. Stage 1b exception detection is upstream of this roadmap's Detection Layer §1 (each tracking exception is an anomaly that may benefit from auto-diagnosis).
- **FMCSA auto-refresh architecture** (4 crons wired today per CHL `501268a`) — DISTINCT scope. That's about external data freshness; this roadmap is about internal platform self-healing. Cross-ref bidirectionally if/when the auto-refresh architecture spec doc lands in `chl-memory/research/`. Overlap point: the auto-refresh crons are themselves objects that can fail and trigger self-healing dispatches — they should be registered with `health_monitor` (already are per `501268a`) so Detection Layer §1 sees them.

---

**Status:** ✅ ROADMAP COMPLETE. Operator/dev review before iter 145.x opens. Full iter 145.1 agenda drafted by @pm-lead when operator authorizes (post-iter-142.1 ship of stages 1c-1f + post-iter-141.3 ship + ideally post-iter-142.2 GPS-tracking ship for outcome-data richness).

**Source:** Drafted by @pm-lead 2026-05-07 EOD-3 per @dev-engineer task brief. North-star pillar: operator's "AI-embedded monitor + write code while running" directive. Forward-looking only; no binding commitments.
