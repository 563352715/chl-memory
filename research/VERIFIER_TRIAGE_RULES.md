# Verifier Triage Rules — When to dispatch a verifier-gate sub-agent

> **Purpose:** explicit decision tree for "should this batch get a verifier
> sub-agent before merge?" Replaces the implicit pattern with rules so
> orchestration cycles don't pay verifier overhead on bounded single-stream
> additive work.
>
> **Standing pattern:** verifier-gate is a code-reviewer-framed sub-agent
> dispatched AFTER parallel sub-agents return + BEFORE dev applies fixes
> + commits. See `feedback_verifier_gate_pattern.md` and
> `chl-memory/research/ORCHESTRATION_PLAYBOOK.md` for the full seven-step
> orchestration loop.
>
> **Validated baseline:** verifier catches ~3 HIGH-severity bugs per
> 4-5-stream batch with cross-module surface (iter 142.1 stages 1c-1f
> proof; EOD-13 carrier-comm batch). Catches near-zero on bounded
> single-stream additive work.
>
> **Last updated:** 2026-05-08 EOD-14.

---

## 1. MANDATORY verifier (no exceptions)

Dispatch a verifier-gate sub-agent if **any** of these conditions hold:

### Cross-module contracts
- Batch touches **DB schema** (new collection, new field on existing
  collection, change to indexed field).
- Batch introduces / modifies a **shared callable seam** (a function
  imported by more than one module).
- Batch reads / writes **env vars consumed by other modules** (e.g.,
  `FMCSA_BROKER_AUTHORITY_STATUS`, `CHL_SPAM_BLOCK_DISABLED`).
- Batch changes the **dispatch state machine** (any module touching
  `db.loads.dispatch_status` or the carrier assignment / outreach flow).

### Sensitive seams
- Batch touches **carrier-comm credentials** — anything Plivo, Twilio,
  outreach orchestrator, SMS provider abstraction, voice TTS.
- Batch touches **money paths** — factor APIs, factor reconciler /
  dispute, Mercury, Stripe, billing, payment, ACH, invoice generation,
  broker_invoice, broker_carrier_agreement, factor_submission_router.
- Batch touches **auth** — login, JWT, magic-link, require_broker,
  get_current_user, role checks.
- Batch touches **PII** — SSN, EIN, full carrier contact info, full
  shipper contact info, anything written to a billing or compliance
  ledger.

### Auto-action paths
- Auto-DM carrier (sender_classifier auto-block, action_dispatcher
  outbound DM, outreach orchestrator first-contact).
- Auto-block message (spam, IMAP MOVE, sender-type auto-classification
  actions).
- Auto-flip submission status (factor_autopair, dispatch state-machine
  transitions, prevet auto-approve / auto-deny).
- Auto-set assignment_blocked / vetted-status / blacklisted / any field
  that gates downstream eligibility.
- Cron writing reliability / risk / outcome rows used by evaluators
  (carrier_evaluator, weight_tuning_proposer).

### Multi-stream parallel
- **>=2 sub-agents in parallel** (the entire pattern of cross-stream
  contract drift is what the verifier catches).

If ANY of the above applies, the verifier IS dispatched. No
case-by-case override.

---

## 2. OPTIONAL verifier (case-by-case judgment)

These cases call for dev-engineer's judgment. Default to verifier UNLESS
verifier overhead clearly exceeds review value.

- **Single-stream additive batch** with **some** cross-module surface
  but well-bounded (e.g., adding a single read endpoint that consumes
  an existing schema). Lean toward verifier if the new endpoint
  participates in observability, integrity, or operational state.
- **Frontend-only changes** to App.js when the change adds a new tab /
  view that consumes existing endpoints (no backend surface change).
  Verifier is overkill for typography / layout / new dashboard tiles.
  Use verifier when the FE change adds a state-mutating button (POST /
  PATCH endpoint invocation that triggers backend auto-actions).
- **Documentation-only commits** to chl-memory or memory/. Verifier is
  unnecessary; dev review is sufficient.
- **Memory file updates** (operator feedback codification, standing
  directives). Same rule — dev review is sufficient.
- **Test-only additions** that don't change production code (regression
  tests for already-shipped behavior). Verifier can spot weak assertions
  but the cost is usually higher than the yield.

When in doubt: **ASK in the orchestration step**. The verifier is a
~8-15 min sub-agent dispatch; cheaper than a production rollback.

---

## 3. SKIP verifier (overhead exceeds yield)

These cases skip the verifier-gate. Dev's own review is the sole gate.

- **Single-stream backend addition with well-defined external contract.**
  Example: Stream W (specialty hauler nationwide-list) — bounded SAFER
  cargo mapping, no auto-actions, pure read endpoints, no cross-module
  surface. Verifier yielded zero findings.
- **Hotfix commit** (single-line bugfix, often a typo / off-by-one /
  config flip). Verifier overhead far exceeds review value when the
  diff is < 10 lines and the fix is obvious from the bug report.
- **Pure refactor with no behavioral change** AND a comprehensive
  pre-existing test suite that all passes post-refactor. (Rare; usually
  refactors imply behavioral risk somewhere.)
- **Sub-agent ALREADY caught its own issue + fixed it** before reporting
  back, AND the issue was within its own module surface (no cross-stream
  contract). Re-running the verifier on a self-corrected single-stream
  fix is cheap-but-zero-yield.

---

## 4. Decision flowchart

| Condition | Verifier? |
|---|---|
| Touches DB schema | MANDATORY |
| Touches shared callable seam | MANDATORY |
| Touches money path | MANDATORY |
| Touches auth | MANDATORY |
| Touches PII | MANDATORY |
| Touches carrier-comm creds | MANDATORY |
| Touches dispatch state machine | MANDATORY |
| Adds auto-action path | MANDATORY |
| >=2 parallel sub-agents | MANDATORY |
| Single-stream additive, cross-module surface unclear | OPTIONAL (default yes) |
| Frontend-only, state-mutating button | OPTIONAL (default yes) |
| Frontend-only, read-only display change | OPTIONAL (default no) |
| Documentation only | SKIP |
| Memory file only | SKIP |
| Single-stream additive, bounded external contract, no auto-action | SKIP |
| Hotfix, < 10 lines, single file | SKIP |
| Pure refactor, full test coverage, no behavioral change | SKIP |

---

## 5. Examples from EOD-6 through EOD-13

### EOD-6: integrity layer (circuit_breakers + budget_caps + operational_state + audit_trail)
- **What we did:** dispatched 4 sub-agents in parallel + verifier.
- **Rule says:** MANDATORY (cross-module shared callable seam — every
  future cron consults integrity gates; multi-stream).
- **Retroactive judgment:** correct. Verifier caught HIGH on circuit-breaker
  threshold computation that would have allowed reset oscillation.

### EOD-7: health_monitor v1
- **What we did:** single-stream sub-agent, no verifier.
- **Rule says:** MANDATORY (introduces shared callable seam —
  `register_health_check` is consumed by every subsequent cron).
- **Retroactive judgment:** narrow miss. Should have run verifier;
  schema for the health-check return dict drifted vs subsequent
  consumers and we ate ~30 min of integration debugging on EOD-8.

### EOD-8: lane_scoring_cron
- **What we did:** single-stream additive, no verifier.
- **Rule says:** OPTIONAL (touches `db.loads.score` — a read-mostly
  field but consumed by evaluator). Default yes.
- **Retroactive judgment:** mild miss. Verifier likely would have caught
  the float-vs-int mismatch on score field that we found in iter 142.1.

### EOD-9: 4 FMCSA refresh crons (census + sms + safer_cargo + qcmobile)
- **What we did:** 4 sub-agents in parallel + verifier.
- **Rule says:** MANDATORY (multi-stream + DB schema additions on
  carrier collection + cross-cron contention on rate limiter).
- **Retroactive judgment:** correct. Verifier caught HIGH on
  qcmobile graceful-degrade path that would have crashed startup if the
  WebKey env var was missing.

### EOD-10: client_error_analyzer + carriers_freshness_router observability
- **What we did:** single-stream additive (one sub-agent + observability
  tile) + verifier.
- **Rule says:** OPTIONAL (introduces a new auto-fingerprint write seam,
  but bounded; not money / auth / PII). Default yes.
- **Retroactive judgment:** correct. Verifier caught MED on stack-trace
  fingerprint hash collision risk.

### EOD-11: system_health_monitor + TONU calculator
- **What we did:** single-stream sub-agent + verifier.
- **Rule says:** MANDATORY (TONU touches money path; system_health_monitor
  introduces orphan-fingerprint seam consumed by ops dashboards).
- **Retroactive judgment:** correct. Verifier caught 3 HIGH (the
  MAX_LINES_PER_TICK silent data loss, the TONU eligible=False
  fallthrough money-leak, and the orphan currently_degraded fingerprint
  bug). All 3 caught pre-merge; documented in
  `f7edfe3` commit + `9bce9d7` regression test commit.

### EOD-12: iter 142.1 stages 1c-1f (carrier_evaluator + outreach + assignment + dispatch)
- **What we did:** 4 sub-agents in parallel + verifier.
- **Rule says:** MANDATORY on every axis — multi-stream, DB schema
  (`db.evaluator_outcomes`, `db.carrier_offers`, `db.dispatch_packets`),
  carrier-comm creds (Twilio Voice TTS), money-adjacent (rate-con
  generation), auto-action paths (auto_assign_carrier, failure_detector
  redispatch).
- **Retroactive judgment:** correct + critical. Verifier caught the
  outreach module-path mismatch that would have silently no-op'd
  redispatch forever.

### EOD-12 stream U: inbound_email sender_classifier
- **What we did:** single-stream + verifier.
- **Rule says:** MANDATORY (auto-action path — auto-block via IMAP MOVE;
  auto-classification feeds operator decisions on prospects).
- **Retroactive judgment:** correct.

### EOD-12 stream T: carrier_contact_enrichment
- **What we did:** single-stream + verifier.
- **Rule says:** MANDATORY (touches PII — populates carrier email /
  phone / physical address; LLM disambiguator gated on budget_caps).
- **Retroactive judgment:** correct.

### EOD-13 Stream W: specialty hauler nationwide-list
- **What we did:** single-stream sub-agent, NO verifier.
- **Rule says:** SKIP (bounded SAFER cargo mapping, no auto-actions,
  pure read endpoints, no cross-module surface — list_metrics_cron is
  read-only / write-once).
- **Retroactive judgment:** correct. Skipping saved a verifier-cycle
  with near-zero expected yield.

### EOD-13 carrier-dispatch milestone close (commit 3067f6e)
- **What we did:** orchestrated 4 worktree-isolated sub-agents in
  parallel + verifier sub-agent. Verifier caught 3 HIGH bugs pre-merge
  (per `handoff_iter_142_1_close.md`).
- **Rule says:** MANDATORY across the board.
- **Retroactive judgment:** correct. Validates the "verifier on
  multi-stream + cross-module" rule cleanly.

### EOD-14 (this batch — 3 process improvements)
- **What it is:** 3 single-purpose artifacts (preflight script + 2 docs).
  No cross-module surface; preflight script is stand-alone; the two
  docs are reference material.
- **Rule says:** SKIP (single-stream-equivalent + documentation +
  bounded external contract). Tests are sufficient gate.
- **Retroactive judgment:** TBD; emit as control case.

---

## 6. How to dispatch the verifier (when MANDATORY)

Quick recipe — full pattern in `ORCHESTRATION_PLAYBOOK.md`.

```
Agent tool, general-purpose framing:

You are a code-reviewer sub-agent. The following <N> sub-agents
shipped files for orchestration cycle <iter>:

  Sub-agent A: <files A wrote, with line counts>
  Sub-agent B: <files B wrote>
  ...

Specs they were built against:
  <absolute paths to spec docs in chl-memory>

Review for:
  1. Cross-module schema / contract drift (sub-agent X writes field F
     but sub-agent Y reads field F2 — mismatch?)
  2. Missing test coverage on real-world failure modes
  3. Idempotency / re-entry safety on every cron + auto-action path
  4. Auth gating on every state-mutating endpoint
  5. PII handling correctness (no leaks to logs, no broad selects)
  6. Money-path correctness (integer cents, no float, audit trail)

For each finding, output: HIGH / MED / LOW + file:line + 1-paragraph
reproduction + suggested fix. Per-stage SHIP / HOLD verdict + top-3
must-fix-before-merge items.

DO NOT commit. DO NOT push. Output is the report only.
```

Dev applies fixes directly post-verifier (per playbook step 4) — does
NOT re-dispatch the original sub-agents, since fixes are typically 1-5
lines and need integration context.
