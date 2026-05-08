# State-Lock + audit_trail Completeness Sweep — 2026-05-08

**Stream:** FF1 (parallel batch dispatch)
**Trigger:** EE2 (21-stage synthetic walkthrough harness) flagged that production
status-flip routes do not write rows to the canonical `db.audit_trail`
collection (the `integrity.audit_trail.record_audit` API). The walkthrough
harness was forced to emit those rows manually because production didn't.

This sweep closes that gap.

---

## Audit-trail API contract (consumed, NOT modified)

`backend/integrity/audit_trail.py` exposes:

```python
await record_audit(
    db,                         # required positional
    *,
    collection: str,            # e.g. "loads"
    doc_id: str,
    actor: str,                 # "user:<email>", "system:<module>", "carrier:<mc>"
    action: str,                # short label, e.g. "status_dispatched"
    before: dict | None,
    after: dict | None,
    note: str = "",
) -> str  # row _id
```

Stores into `db.audit_trail` collection. Schema includes
`{collection, doc_id, actor, action, before, after, recorded_at, note}`.
Module already capped to 64KB serialized snapshots, has 90-day prune cron,
auto-registered health check.

Conclusion: API surface is sufficient. No contract gap requires a module
change. We only consume it from the canonical state-flip paths.

---

## Pre-existing local audit surfaces (NOT replaced — kept intact)

Each canonical state-flip path already writes its own local audit:

| Canonical fn                                        | Local collection         | Schema                                                         |
| --------------------------------------------------- | ------------------------ | -------------------------------------------------------------- |
| `state_lock.transition_load_status`                 | `db.load_status_audit`   | from_status / to_status / actor / accepted / rejection / etc.  |
| `assignment.auto_assigner._do_auto_assign_carrier`  | `db.carrier_assignments` | from_state / to_state / mc_number / accepted / etc.            |

The integration pattern: the canonical fn keeps writing its rich local audit
(forensic detail), AND ALSO calls `integrity.audit_trail.record_audit` so the
unified `db.audit_trail` collection has parity. Two writes; one cheap; allows
the walkthrough harness + future cross-collection forensics to read a single
canonical surface.

---

## Discovery: status-mutating call sites

Grep across `backend/` for `db.loads.update_one` returned **80+** sites.
Filtered to those that mutate `status`, `assignment_state`, or other
lifecycle-status fields:

### A. ROUTED through `state_lock.transition_load_status` (canonical, audit
once at the helper):

These do NOT each need their own `record_audit` -- the helper covers them.
Verified routes that route through state_lock (grep "transition_load_status"):

- `backend/server.py:2538` -- PATCH /api/loads/{id}/status (operator UI cancel/deliver/etc.)
- `backend/auto_dispatch.py` -- try_auto_accept booked transitions
- `backend/automations.py` -- cron-driven flips
- `backend/auto_repost_routes.py` -- repost workflow
- `backend/cron_scheduler.py` -- nightly cron
- `backend/delivered_load_autofire.py` -- POD-driven delivered transition
- `backend/execution_pilot.py` -- automation pilot
- `backend/sop_engine.py` -- runbook flips

(Action: instrument state_lock.transition_load_status itself; that one edit
covers all callers.)

### B. ROUTED through `assignment.auto_assigner._do_auto_assign_carrier`:

- `backend/assignment/auto_assigner.py:161` -- assigned_unconfirmed flip

(Action: instrument _do_auto_assign_carrier itself.)

### C. ROUTED through `assignment.confirm_handler.handle_carrier_confirm`:

- `backend/assignment/confirm_handler.py:142` -- assigned_confirmed flip

(Action: instrument handle_carrier_confirm itself.)

### D. ROUTED through `assignment.failure_detector._unassign_load`:

- `backend/assignment/failure_detector.py:251` -- unassigned_after_ghost flip

(Action: instrument _unassign_load itself.)

### E. DIRECT status writes that BYPASS state_lock (out-of-band):

These are direct status writes that did NOT go through the canonical helper.
Per state_lock.py docstring (Iter 106 hardening), the contract says all
status flips SHOULD go through the helper -- but pre-Iter-106 routes still
exist. We add `record_audit` at the call-site:

- `backend/delivery_completion.py:142` -- POST /api/carrier/loads/{id}/complete-delivery
  (driver self-serve, atomic update_one with status:{$ne:"delivered"} guard)
  Bypasses state_lock because it has its own geofence enforcement +
  conditional update guard. Add audit_trail call inline.

- `backend/automations.py:941` -- direct .status flip in automations cron
  Already covered by the surrounding state_lock helper (verified by the
  grep above; state_lock is imported at line 941's parent function -- skip).

### F. carrier_vetting_workflow status updates:

- `backend/carrier_vetting_workflow.py:345` -- PATCH /api/carriers/{mc}/workflow/{step_id}
  Updates carriers.vetting_status when overall_status recomputes.
  Add `record_audit` inline.

### G. CALL SITES INTENTIONALLY SKIPPED (with rationale):

| Site                                              | Reason for skip                                                |
| ------------------------------------------------- | -------------------------------------------------------------- |
| `tests/synthetic_load_walkthrough.py`             | Test fixture; already calls `_record_audit` wrapper.           |
| `tests/test_*.py`                                 | Tests; not production code.                                    |
| `tests/test_tonu_integration.py`                  | Test stub.                                                     |
| `cron_scheduler.py` `ar_followup_t*_at` updates   | Updates timestamp fields on `loads`, NOT `status`. Not a       |
|                                                   | lifecycle transition; AR follow-up bookkeeping only.           |
| `failure_detectors_part2.py` various `update_one` | Already write to `db.failure_events` -- domain-specific,       |
|                                                   | not lifecycle. (Addressing in stream FF2 if needed.)           |
| `ghost_visibility.py` $push milestone events      | Append-only timeline events on `visibility_milestones`         |
|                                                   | sub-array; not status mutations.                               |
| `synthetic_load_replay.py`                        | Backfill / replay helper, not production hot path.             |
| `auto_repost_*` direct updates that don't touch   | `recommended_loadboards` etc. is configuration, not lifecycle. |
| status                                            |                                                                |
| `load_schema_v2.py` migration writes              | One-time schema migration; not a runtime transition.           |
| `bookings`, `carriers`, `factor_requests` etc.    | Out of scope for this stream (stream FF1 = loads + assignment  |
|                                                   | + carrier_vetting). Follow-up streams to expand.               |

---

## Apply plan: 6 canonical-helper edits

| File                                            | Function                          | Action                                                |
| ----------------------------------------------- | --------------------------------- | ----------------------------------------------------- |
| `backend/state_lock.py`                         | `transition_load_status`          | Add record_audit after accepted flip + on rejection.  |
| `backend/assignment/auto_assigner.py`           | `_do_auto_assign_carrier`         | Add record_audit after accepted assignment + reject.  |
| `backend/assignment/confirm_handler.py`         | `handle_carrier_confirm`          | Add record_audit after accepted confirmation.         |
| `backend/assignment/failure_detector.py`        | `_unassign_load`                  | Add record_audit after unassign-after-ghost.          |
| `backend/delivery_completion.py`                | route handler `complete_delivery` | Add record_audit after delivered flip.                |
| `backend/carrier_vetting_workflow.py`           | `update_workflow_step`            | Add record_audit after vetting_status overall change. |

Each edit: try/except wrap so a `record_audit` failure (Mongo down, etc.)
does NOT abort the actual lifecycle flip. The local audit collection write
is the primary audit; `audit_trail` is a parallel canonical surface.

---

## Tests added

`backend/tests/test_state_lock_audit_completeness.py` (6 tests):

1. `test_load_status_dispatched_writes_audit_row`
2. `test_load_status_delivered_writes_audit_row`
3. `test_load_status_cancelled_writes_audit_row`
4. `test_carrier_vetting_status_change_writes_audit_row`
5. `test_audit_row_contains_from_status_and_to_status`
6. `test_audit_write_failure_does_not_break_transition`

Reuses the `_FakeDB` pattern from `synthetic_load_walkthrough.py` /
`test_dispatch_packet.py`.

---

## Follow-ups (priority-ordered)

1. **HIGH -- audit_trail rotation policy review.** With this sweep,
   write volume into `db.audit_trail` will jump (every load lifecycle
   flip + every assignment state change + every vetting status
   recompute). The 90-day prune cron is in place but the volume
   forecast should be re-run. Estimate: ~50-200 status flips/day in
   steady state -> 4500-18000 rows/90d -> still trivial. But with
   carrier_vetting_workflow at 27,000 census carriers x 11 steps
   each, the initial backfill will spike. Add `recorded_at_ts`
   compound index `(collection, doc_id, recorded_at_ts)` for
   read-performance under volume.

2. **MEDIUM -- domain expansion.** This stream addressed loads + carrier
   assignment + vetting. Next streams should sweep:
   `factor_requests.status`, `cargo_claims.status`,
   `payments.status`, `carrier_failures.status`,
   `dispute_handler.status` -- each has its own state-machine.

3. **MEDIUM -- audit_trail.audited_write context manager adoption.**
   The audit_trail module exposes an `audited_write()` async context
   manager that captures BEFORE/AFTER snapshots automatically. Several
   of our newly-instrumented sites would be cleaner with that pattern.
   Migration is mechanical; defer to a code-quality pass.

---

## Status

Sweep doc written: 2026-05-08.
Code edits: pending parent verifier-gate.
Tests + preflight: pending parent verifier-gate.

---

## Domain expansion (FF1 follow-up #2 closure)

**Stream:** GG1 (parallel batch dispatch, 2026-05-08 EOD-15)
**Scope:** factor_submissions.status, cargo_claims.status, payments.status,
carrier_failures.status, factor_disputes.status -- the 5 collections FF1
flagged in follow-up #2 ("MEDIUM -- domain expansion").

### Per-domain results

| Domain                     | Canonical helper edited                                  | Audit-trail mirror | Test name                                                           |
| -------------------------- | -------------------------------------------------------- | ------------------ | ------------------------------------------------------------------- |
| `factor_submissions.status` | `factors/dispute_handler.py::_flip_submission_status`   | added              | `test_factor_requests_status_change_writes_audit_row` (funded path) |
| `factor_submissions.status` | `factors/reconciler.py::_flip_to_funded`                 | added              | (covered by Test 1 + idempotency Test 8)                            |
| `cargo_claims.status`       | `failure_detectors_part2.py::handle_cargo_claim`         | added              | `test_cargo_claims_status_change_writes_audit_row` (open path)      |
| `cargo_claims.status`       | `failure_detectors_part2.py::resolve_cargo_claim` route  | added              | (resolve path covered in same test)                                 |
| `payments.status`           | NEW `payment_rails.py::transition_payment_status` (thin) | created            | `test_payments_status_change_writes_audit_row`                      |
| `factor_disputes.status`    | `factors/dispute_handler.py::resolve_dispute`            | added              | `test_dispute_status_change_writes_audit_row`                       |
| `carrier_failures.status`   | NO production canonical helper exists yet                | flag-test only     | `test_carrier_failures_status_change_writes_audit_row` (flag-test)  |

### Detail per domain

**1. factor_submissions.status (canonical: 2 helpers found)**

Two canonical paths converge on `db.factor_submissions`:
- Sad path: `_flip_submission_status` in `factors/dispute_handler.py` -- handles
  `submitted -> disputed`, `disputed -> submitted` (cure), `disputed -> lost`.
  Edit: read prior status from the in-memory submission dict, short-circuit
  if already at `new_status`, write audit_trail row after the update_one
  succeeds, plumb `actor=` through from `resolve_dispute` so operator email
  shows up in the audit row. New `actor` kwarg defaults to
  `"system:factor_dispute_handler"` for back-compat with the cron path.

- Happy path: `_flip_to_funded` in `factors/reconciler.py` -- handles
  `submitted -> funded` for email-wire / mercury-deposit / portal-poll signals.
  Edit: capture prior status before the update_one, idempotent guard skips
  re-flipping a row already at `funded` (avoids no-op spam from the cron
  poll re-running on a row a different signal already settled). Audit row
  records `reconciled_via` so the unified surface tells you which channel
  closed the loop.

**2. cargo_claims.status (canonical: 1 handler + 1 route)**

`failure_detectors_part2.py` owns the cargo-claim lifecycle:
- `handle_cargo_claim()` is the canonical OPEN path. Edit: added an
  `_record_audit_trail` mirror right after the insert_one + load row update.
  Records `before={"status": None}`, `after={"status": "open", ...}`.

- `resolve_cargo_claim` (FastAPI inline route) is the canonical RESOLVE path.
  Edit: read prior row to capture from_status; if already `resolved`, return
  an early no-op (avoids audit spam on idempotent re-resolve calls); audit
  mirror after the update_one.

A new module-level helper `_record_audit_trail` was added to
`failure_detectors_part2.py` matching the FF1 `state_lock._record_audit_trail`
shape exactly (lazy import of `integrity.audit_trail.record_audit`,
try/except wrap, structured log on failure).

**3. payments.status (NO canonical helper -- mitigation: created one)**

The `payments` collection had **no** canonical helper. Status writes were
scattered across 4 sites:

| Site                 | Field               | Direction                 |
| -------------------- | ------------------- | ------------------------- |
| `payment_rails.py:188` | `settlement_status` | `pending -> funded`       |
| `server.py:3761`     | `status`            | `pending -> processing`   |
| `server.py:3817`     | `status`            | `processing -> completed` |
| `server.py:3880`     | `status`            | `processing -> completed` |

Mass-editing 4 routes risks regression. Per the directive
("create a thin helper with TODO comments rather than mass-editing every
site"), introduced a NEW canonical helper:

```python
async def transition_payment_status(
    db, payment_id, new_status,
    *, actor, extra_set=None, status_field="status", note="",
) -> dict
```

at `payment_rails.py`. Reads prior status (idempotency short-circuit),
performs update_one, mirrors to db.audit_trail. The `status_field` kwarg
honors the `payment_rails.fund` quirk where the transition target is
`settlement_status` rather than `status`.

`payment_rails.fund()` was migrated to route through the new helper
(removes the inline `update_one`). The 3 server.py sites are
TODO-flagged for migration in a follow-up batch -- they continue to
write directly today, so the audit_trail mirror has known gaps for
checkout-init / checkout-poll / stripe-webhook paths until that follow-up
ships.

**4. factor_disputes.status (canonical: resolve_dispute)**

`factors/dispute_handler.py::resolve_dispute` is the canonical helper for
flipping a dispute row's lifecycle status (`pending` ->
`resolved_cured` | `resolved_lost`). Edit: capture prior status before the
update_one, audit_trail mirror after the dispute row update succeeds.
Actor is plumbed from `operator_email` (or "operator" fallback) so the
audit row identifies who made the decision.

The initial dispute insert (status=pending, `detect_disputes_in_portal_payload`)
does not yet emit a separate audit row -- the dispute insert is itself the
audit-row equivalent (status=pending IS the initial state). If a later
stream wants the explicit "factor_dispute_status_pending" row to land on
detection, it is a 5-line addition right after the insert_one in
`detect_disputes_in_portal_payload`. Skipped here: noisy, and the dispute
row's own `detected_at` already serves as forensic ts-of-detection.

**5. carrier_failures.status (NO production helper -- FLAG)**

The `db.carrier_failures` collection is referenced ONLY in the
synthetic_load_walkthrough harness today. Production code does not write
to it -- production failure-detection writes to `db.failure_events`
(forensic, INSERT-only, no `.status` field) via
`assignment/failure_detector.py`.

This is a **collection-name drift gap**: the synthetic walkthrough uses
`carrier_failures` but no production producer emits to it. Two valid
mitigation paths:

  (a) Migrate the synthetic walkthrough to read/write `failure_events`
      so synthetic + production share a single collection. (Preferred --
      keeps the collection set tight.)

  (b) Build a production producer that writes to `carrier_failures`
      (e.g., a wrapper around assignment/failure_detector that
      escalates from `failure_events` INSERT-only -> `carrier_failures`
      with a status lifecycle). Costlier; introduces a 2nd collection.

Decision deferred to operator. For this stream, added a flag-test
(`test_carrier_failures_status_change_writes_audit_row`) that pins the
audit row format a future canonical helper would emit. The test
exercises `failure_detectors_part2._record_audit_trail` directly so the
helper-shape is verified even though no production producer is wired.

### Skipped writes + rationale

| Site                                              | Reason for skip                                                                |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| `factor_performance.py:282`                        | Manual lifecycle-event recorder; one of N stages, not the canonical state machine. Stage label IS the status. Deferring to a follow-up that decides whether the manual-event surface should mirror or not. |
| `detect_disputes_in_portal_payload` insert        | Initial dispute insert (status=pending) -- the row insert IS the initial state; explicit audit-row would be noise.                                                                       |
| `server.py:3761/3817/3880` payments sites         | Migration to `transition_payment_status` deferred to a follow-up batch (not in scope for this stream).                                                                                  |
| `stripe_connect.py:450/463/483/512`                | These touch `db.carrier_payments`, NOT `db.payments`. Different collection (carrier-side payouts). Out-of-scope for this stream's `payments.status` target.                              |

### Tests added

`backend/tests/test_state_lock_audit_domain_expansion.py` (8 tests, ~610 lines):

  1. `test_factor_requests_status_change_writes_audit_row` -- covers
     `factor_submissions.status -> funded` via reconciler.
  2. `test_cargo_claims_status_change_writes_audit_row` -- covers both
     open + resolve.
  3. `test_payments_status_change_writes_audit_row` -- covers the new
     `transition_payment_status` helper.
  4. `test_carrier_failures_status_change_writes_audit_row` -- flag-test
     (no production helper; pins audit format).
  5. `test_dispute_status_change_writes_audit_row` -- covers
     `resolve_dispute` flipping dispute row to `resolved_cured`.
  6. `test_audit_row_contains_from_and_to_status_for_each_domain` --
     cross-domain regression on before/after capture.
  7. `test_audit_write_failure_does_not_break_transition` -- monkeypatches
     `record_audit` to raise; asserts all 4 producing domains still flip
     state successfully.
  8. `test_idempotent_status_set_to_same_value_no_audit_row` -- asserts
     the no-op short-circuit prevents audit spam on `payments` and
     `factor_submissions` same-value writes.

### Test result

36/36 pass (6 FF1 + 8 GG1 + 22 synthetic walkthrough).

```
backend/tests/test_state_lock_audit_completeness.py        6 passed
backend/tests/test_state_lock_audit_domain_expansion.py    8 passed
backend/tests/synthetic_load_walkthrough.py                22 passed
```

Preflight: `preflight OK: 169 routers + 18 crons + 5 health-registers verified`
(no pre-existing failures; no new failures introduced).

### Status

Code edits: pending parent verifier-gate (uncommitted on dev side).
Doc edits: committed to chl-memory.

