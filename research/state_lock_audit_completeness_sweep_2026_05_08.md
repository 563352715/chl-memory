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
