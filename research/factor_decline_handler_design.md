# factor_decline_handler design

**Iter / Stream:** QQ3 stage 25 missing hook #4 (RR2)
**Date:** 2026-05-08
**Author:** dev (RR2 sub-agent)
**Status:** shipped (code merged-pending; doc committed to chl-memory)

---

## 1. Why this exists

QQ3 stage 25 expanded the synthetic walkthrough to cover both branches
of the factor approval-vs-decline flow. The APPROVAL branch already had
a production orchestrator (the existing reconciler in
`backend/factors/reconciler.py`). The DECLINE branch did not -- the
walkthrough inlined the decline-side logic (alt-factor resubmit +
fallback collection workflow) directly in the test, so the only place
the policy lived was a test fixture.

That meant: when a real factor portal returns `status=declined` for a
submission, NOTHING in production handles the consequences. The invoice
sits at `status=declined`, no alt-factor is dispatched, no collection
workflow is queued, and the operator has no surface to action it.

This module is the missing parallel of
`backend/carrier_redispatch/redispatch_workflow.py`: a tick-level
orchestrator that consumes declined `factor_submissions` rows,
classifies the decline reason, and routes per a fixed policy.

## 2. Scope

| In scope | Out of scope |
|---|---|
| Decline-reason classification (regex-based, deterministic) | LLM-based reason classification |
| Per-class action policy (alt-factor / op-review / manual collection) | Direct factor-portal API calls (delegated to `factor_autopair`) |
| N-attempt cap with manual-collection fallback | Auto-write-off / accounting decisions (operator-driven) |
| Audit-trail mirror to `db.audit_trail` (FF1+GG1 contract) | Email/SMS notification dispatch (delegated to alerts queue) |
| Operator manual-resolution override (require_owner) | Re-flipping the original submission state on cure (handled by reconciler) |
| Cron registration + health endpoint | UI / dashboard frontend |

## 3. Decline-reason classification

5 classes (constants in `factor_decline_handler.decline_handler`):

| Class | Trigger phrases (regex) | Action |
|---|---|---|
| `SHIPPER_CREDIT_RISK` | "shipper credit", "credit risk/insufficient/denied/limit", "bad credit", "underwriting declined" + machine code `shipper_credit_insufficient` | alt_factor_resubmit |
| `INVOICE_QUALITY` | "amount mismatch", "wrong amount", "duplicate invoice", "rate mismatch", "tax id mismatch" | operator_review |
| `FACTOR_OUT_OF_CAPACITY` | "out of capacity", "portfolio limit", "capacity exceeded", "funding cap reached", "monthly limit reached" | alt_factor_resubmit |
| `DOCUMENTATION_INCOMPLETE` | "missing pod/bol/rate-con/signature", "documentation incomplete", "need pod/signed", "incomplete paperwork" | operator_review |
| `UNKNOWN` | nothing else matches | operator_review |

**Why regex, not LLM:** classification gates auto-resubmit vs
operator-review on a money path. A regex makes the class deterministic +
auditable; an LLM would add latency + hallucination risk. The patterns
are conservative -- when in doubt, route to `UNKNOWN`, which still
routes to operator review (safe default). Future iteration can layer a
learned classifier on top once we have ~500 historical declines to
train on.

**Precedence on overlap:** SHIPPER_CREDIT > DOCUMENTATION_INCOMPLETE >
INVOICE_QUALITY > FACTOR_OUT_OF_CAPACITY > UNKNOWN. This ordering
prefers the most actionable signal (credit risk has clearer remediation
than the others) and disambiguates the "missing pod" -- "pod illegible"
overlap (DOC_INCOMPLETE wins).

## 4. Per-class action policy

Two distinct branches:

**Branch 1: alt_factor_resubmit**
1. Look up the load's history of factor submissions.
2. Pick the next-priority factor not already tried (priority order =
   `factor_matrix._REGISTRY` sorted by `reliability_score` desc).
3. Insert a fresh `factor_submissions` row with `status=submitted` and
   `retry_origin=factor_decline_handler`.
4. The existing `factor_autopair` cron picks it up on next tick to
   actually call the factor's API. We don't make the external API call
   here -- that keeps this orchestrator fast and avoids re-implementing
   per-factor adapter logic.

**Branch 2: operator_review**
1. Insert a row in `db.operator_review_queue` with class + reason + notes.
2. Idempotent on `(submission_id, decline_class)` -- re-running the cron
   doesn't spam the queue.

**Branch 3: manual_collection (terminal)**
Triggered when (a) the alt-factor branch hits the attempt cap, OR (b)
the alt-factor branch can't find a fresh factor not already tried.
1. Insert a `db.collection_workflows` row with `stage=direct_collection_queued`
   and `trigger=factor_decline_attempt_cap_reached`.
2. Insert a high-severity `db.alerts` row.
3. The submission is flipped to `post_decline_status=manual_collection_required`.

## 5. N-attempt cap rationale

`ATTEMPT_CAP_DEFAULT = 3`. Configurable via `CHL_FACTOR_DECLINE_ATTEMPT_CAP`.

Reasoning:
- Try 1 = primary factor (the one that just declined).
- Try 2 = next-priority factor.
- Try 3 = third factor.
- After 3 declines, the marginal expected-success of attempt 4 is low
  (factors share a lot of the same shipper-credit data feeds), and
  burning more factor relationships on a single problem invoice has
  long-term cost (factors track our submission-to-fund ratio).

Operator-driven manual collection at attempt 4+ is the right
escalation: at that point the bottleneck is shipper-side, not
factor-side, so the workflow shifts from "find a factor that'll fund"
to "call the shipper and collect direct."

## 6. Audit-trail integration (FF1 + GG1)

Every per-row transition mirrors a row to `db.audit_trail` via
`integrity.audit_trail.record_audit`. Captured fields:
- `collection = "factor_submissions"`
- `doc_id = submission_id`
- `actor = "system:factor_decline_handler"` (or `user:<email>` for the
  manual-resolution path)
- `action = "factor_decline_<verb>"` where `<verb>` is the action class
  (`alt_factor_resubmit` / `operator_review` / `manual_collection` /
  `manually_resolved`)
- `before` snapshot: original `status`, `decline_reason`, `factor_slug`,
  `decline_handler_status`
- `after` snapshot: `decline_class`, `decline_action`,
  `post_decline_status`, `alt_factor_slug`, `alt_factor_submission_id`,
  `operator_review_id`, `collection_workflow_id`

Failure of the audit_trail write is logged but does NOT abort the
handler -- audit is forensic, not load-bearing on the decision.

A separate `db.factor_decline_attempts` collection captures
per-attempt forensic rows (one per submission processed) with the
audit_trail_id back-reference for cross-collection joining.

## 7. Operator override surface

`POST /api/_factor_decline/{decline_id}/manual_resolution` (require_owner)

Use case: operator calls the shipper, gets corrected paperwork sent
direct, wants to mark a decline-attempt resolved without waiting for the
3-attempt cap to flip the load to manual collection.

Action:
1. Stamps `manual_resolution_status=resolved` on the
   `factor_decline_attempts` row.
2. Closes any open `operator_review_queue` row tied to the attempt.
3. Records an audit row.

Does NOT auto-flip the original submission state forward -- that's the
reconciler's job once the alt-factor (or re-submitted primary) actually
funds.

Gated on `require_owner` because it can short-circuit the 3-attempt
collection escalation (a money-path decision).

## 8. Operational concerns

**Kill-switches:** `is_read_only()` and `should_skip_tick()` are honored
at the top of every cron tick. CHL_READ_ONLY=true short-circuits with
`status=skipped_kill_switch`.

**Cron interval:** 10 min (`_FACTOR_DECLINE_INTERVAL_SEC = 600`).
Tighter than the reconciler's day-granular windows (declines should be
handled fast for cash flow) but loose enough that the alt-factor
resubmit pipeline (`factor_autopair`) doesn't churn its own cron tick.

**Initial delay:** 240 sec. Consistent with `carrier_redispatch_cron` --
the upstream factor_submission auto-submitter and reconciler need
cold-start headroom before our first tick.

**Health endpoint:** `GET /api/_factor_decline/_health`. Reports last
tick state, totals, attempt cap, and the cron's loop-running flag.

**Idempotency:** `decline_handler_status=pending` on the submission row
gates re-processing -- a second cron tick that reads the same row sees
`handled` and skips. Operator-side resets via direct DB write if a
re-attempt is needed.

## 9. Test coverage

8 unit tests in `backend/tests/test_factor_decline_handler.py`:
- 4 classifier tests (one per non-UNKNOWN class + one for UNKNOWN
  routing)
- 1 alt-factor resubmit test (end-to-end submission insert)
- 1 manual-collection-after-3-attempts test
- 1 audit_trail mirroring test (3 declines, 3 audit rows, attempt rows)
- 1 kill-switch test (CHL_READ_ONLY=true)

Plus walkthrough stage 25 (BRANCH B) now invokes the production
`handle_factor_declines` instead of inlining decline logic. Combined
walkthrough = 26 tests; 8 + 26 = 34, all passing.

## 10. Data model summary

New collections:
- `db.factor_decline_attempts` -- per-attempt forensic rows
- `db.operator_review_queue` -- operator surface for non-auto-resubmit
  declines

Schema additions on `db.factor_submissions`:
- `decline_handler_status`: pending | handled | error
- `decline_handler_at`: ISO timestamp
- `decline_class`: one of the 5 class constants
- `decline_action`: alt_factor_resubmit | operator_review |
  manual_collection
- `decline_attempt_idx`: 1-based int
- `post_decline_status`: alt_factor_resubmitted | operator_review_required
  | manual_collection_required
- `alt_factor_slug` + `alt_factor_submission_id` (when alt-factor path
  taken)
- `operator_review_id` (when op-review path taken)
- `collection_workflow_id` (when manual-collection path taken)

## 11. Follow-ups

Priority-ordered:

1. **Wire factor_autopair to consume the new `retry_origin=
   factor_decline_handler` rows.** Today it picks up any `submitted`
   row, but the next-iteration improvement is to add a per-row priority
   bump for declined-resubmits so they jump the queue ahead of fresh
   first-time submissions.
2. **Per-factor decline-rate telemetry.** With audit_trail rows landing
   on every decline, build a 30-day rolling decline-rate per factor and
   feed it into `factor_matrix.reliability_score` recomputation. Today
   the score is a static estimate; this would make it learned.
3. **Operator-side dashboard for the operator_review queue.** The
   router exposes the data; a frontend page (parallel to the existing
   factor disputes view) would close the loop. Backend-side is shipped
   on this PR; frontend is follow-up.

## 12. Files

| Path | Lines |
|---|---|
| `backend/factor_decline_handler/__init__.py` | 28 |
| `backend/factor_decline_handler/decline_handler.py` | 666 |
| `backend/factor_decline_handler/decline_handler_router.py` | 207 |
| `backend/tests/test_factor_decline_handler.py` | 459 |
| Cron registration in `backend/cron_scheduler.py` | +135 lines |
| Router mount + walkthrough stage 25 wire-up | small edits |

## 13. Acceptance checklist

- [x] Module + router built at expected paths.
- [x] Cron registered with 10-min interval, 240-sec initial delay,
      kill-switch honored, health endpoint exposed.
- [x] 5 decline classes defined + regex classifier built.
- [x] Per-class action policy implemented + tested.
- [x] 3-attempt cap + manual-collection fallback implemented + tested.
- [x] Audit_trail mirror on every transition (FF1+GG1 contract).
- [x] Operator manual_resolution override built + gated on require_owner.
- [x] 8 new unit tests + walkthrough stage 25 still passes (34 total).
- [x] Preflight: no NEW issues introduced (pre-existing 20 missing-pkg
      issues unchanged).
- [x] Doc committed to chl-memory.

---

End of design.
