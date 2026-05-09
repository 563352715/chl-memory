# Carrier-Tier Promoter + Credit-Override Revoker -- Design

**Stream:** RR1 (sub-agent of QQ3 walkthrough expansion)
**Iter:** 145.x
**Date:** 2026-05-08
**Author:** dev / sub-agent RR1
**Status:** SHIPPED (code merged into main on operator commit)

## Why this stream exists

QQ3's 25-stage `backend/tests/synthetic_load_walkthrough.py` walkthrough
expansion exposed 4 missing production services where the harness was
inlining the state-machine logic that production wasn't yet running.
This stream productionizes 2 of those 4 (the event-driven cron pair):

  * **Stage 22 -- carrier-tier promotion.** When a carrier crosses
    `successful_load_count >= 5` while still tagged `tier="watch"`,
    they should auto-promote to `tier="preferred"`. The walkthrough
    flagged: *"FLAG: no production carrier_tier_promoter.py exists
    today -- harness inlines the state machine."*
  * **Stage 23 -- shipper credit-override revocation.** When a shipper
    has `credit_score_override=True` and a `payment_failed` event
    lands in `db.payment_events`, the override should auto-revoke
    (flip to `False`, stamp `override_revoked_at`). The walkthrough
    flagged: *"FLAG: production hook MISSING -- recommend
    backend/credit_override_revoker.py cron that subscribes to
    payment_events.event_type=payment_failed."*

Both follow the same pattern (subscribe to event -> auto-flip a state
field), so they're bundled into one stream.

QQ3's other 2 missing services (the remaining pair from the 4 flagged)
are out of scope here -- separate sub-agent stream.

## North-star alignment

These are textbook self-healing modules: each one removes an
operator-touch surface that today only fires correctly when a human
notices the threshold cross. Score on the 3 axes from
`platform_north_star.md`:

  * **Removes operator-touch:** YES -- both flips happened manually
    today (PM-side or operator review). Cron now does both
    autonomously.
  * **Adds observability:** YES -- both emit `audit_trail` rows
    consumed by `/api/_carrier_tier/recent_promotions` and
    `/api/_credit_override/recent_revocations` for read-only operator
    visibility, plus a `_health` endpoint for cron heartbeat.
  * **Substrate for future self-healing:** YES -- the
    `audit_trail` rows feed `outcome_tracker` / `aether_governor`
    downstream, and the `tier_promoted_reason` /
    `override_revoked_reason` fields are introspectable so a future
    aether weight-tuning pass can adjust `DEFAULT_PROMOTION_THRESHOLD`
    (or per-shipper override windows) based on observed downstream
    outcomes.

## Architecture

### Module A -- `backend/carrier_tier_promoter/`

```
backend/carrier_tier_promoter/
  __init__.py             # package docstring, exports
  tier_promoter.py        # service module (~300 lines)
  promoter_router.py      # operator read-only HTTP surfaces (~80 lines)
```

**Service entry point:**

```python
async def promote_tiers_for_qualifying_carriers(
    *,
    db,
    threshold: int = 5,
    max_per_tick: int = 50,
) -> dict:
    """Find carriers with successful_load_count >= threshold AND tier=watch;
    promote each to tier=preferred; record audit_trail row per promotion.
    Honors integrity.operational_state.is_read_only +
    should_skip_tick. Returns {seen, promoted, errored, by_carrier_id, errors}.
    """
```

**Selection rule (boolean AND of all):**

  1. `tier == "watch"`
  2. `successful_load_count >= threshold`
  3. `blacklisted is not True` (defensive guard)
  4. `vetting_status` (when present) in `{"vetted", "approved", "active"}`

**Action per qualifying carrier:**

  1. `db.carriers.update_one({"id": cid}, {"$set": {tier="preferred",
     tier_promoted_at, tier_promoted_from, tier_promoted_reason}})`
  2. `integrity.audit_trail.record_audit(collection="carriers", ...,
     actor="auto:carrier_tier_promoter",
     action="carrier_tier_promoted", before={"tier": "watch"},
     after={"tier": "preferred"})`

**Idempotency:** the selector filters `tier == "watch"`, so a
re-run after promotion is a no-op (the `find()` returns nothing
new). Concurrent ticks converge -- same `$set`.

### Module B -- `backend/credit_override_revoker/`

```
backend/credit_override_revoker/
  __init__.py             # package docstring, exports
  revoker.py              # service module (~400 lines)
  revoker_router.py       # operator read-only HTTP surfaces (~80 lines)
```

**Service entry point:**

```python
async def revoke_overrides_for_payment_failures(
    *,
    db,
    since_minutes: int = 90,
    max_per_tick: int = 50,
) -> dict:
    """Find payment_failed events in the last N minutes; for each, look
    up the associated shipper; if shipper.credit_score_override is True,
    flip to False + stamp override_revoked_at + override_revoked_reason +
    override_revoked_event_id; record audit_trail row.
    Honors is_read_only + should_skip_tick.
    Returns {seen, revoked, skipped_no_override, errored, errors}.
    """
```

**Selection rule (boolean AND of all):**

  1. `payment_events.event_type == "payment_failed"`
  2. `payment_events.occurred_at >= now - since_minutes`
  3. `shippers.credit_score_override == True`
  4. `shippers.override_revoked_event_id != event_id` (idempotency guard)

**Action per qualifying event:**

  1. `db.shippers.update_one({"id": sid}, {"$set": {
     credit_score_override=False, override_revoked_at,
     override_revoked_reason="auto:payment_failed_event",
     override_revoked_event_id=event_id}})`
  2. `integrity.audit_trail.record_audit(collection="shippers", ...,
     actor="auto:credit_override_revoker",
     action="credit_override_revoked",
     before={"credit_score_override": True},
     after={"credit_score_override": False})`

**Idempotency:** after revocation `credit_score_override` is `False`,
so the selection rule short-circuits on subsequent ticks. The
`override_revoked_event_id` field is a defense-in-depth dedupe key
for the rare case where a separate path (operator manual flip) cleared
override before our cron processed the event.

## Cron registration (backend/cron_scheduler.py)

### `carrier_tier_promoter_cron`

  * `_TIER_PROMOTER_INTERVAL_SEC = 6 * 60 * 60`  (6 hr)
  * `_TIER_PROMOTER_INITIAL_DELAY_SEC = 600`     (10-min cold-start)
  * `_TIER_PROMOTER_MAX_PER_TICK = 50`

**Why 6 hr:** tier promotion is not time-critical. A carrier crossing
`successful_load_count >= 5` within the gap of one cron tick simply
gets promoted on the next tick; nothing downstream blocks on a faster
response. A longer interval keeps the carriers-collection scan
infrequent. We could plausibly run it on `delivered_load` event-emit
instead, but a periodic backfill scan also catches counter-correction
events (reconciliation) that an event-driven path would miss.

### `credit_override_revoker_cron`

  * `_OVERRIDE_REVOKER_INTERVAL_SEC = 15 * 60`   (15 min)
  * `_OVERRIDE_REVOKER_INITIAL_DELAY_SEC = 180`  (3-min cold-start)
  * `_OVERRIDE_REVOKER_SINCE_MINUTES = 90`       (6x interval re-process buffer)
  * `_OVERRIDE_REVOKER_MAX_PER_TICK = 50`

**Why 15 min:** payment-failed events should trigger override
revocation fast -- the longer a credit-override stays live after a
payment failure, the more new loads can accept the (now-risky) shipper.
15 min is fast enough to bound that exposure (max ~$3-5K of new load
exposure per 15-min window) while not thrashing the DB on every
event-table change.

**Why `since_minutes=90`:** 6x the interval gives a delayed/missed
tick a generous re-process buffer without dredging up ancient
failures. A `payment_failed` event older than 90 min has either
already been revoked (and its shipper now has `override=False`, so
it's a skip) or was processed by a manual operator path earlier.

## Audit-trail integration

Both services use `integrity.audit_trail.record_audit` as the
canonical write path. This guarantees:

  * Every auto-action is observable from
    `/api/_audit_trail/...` queries (PM-side proof of cron action).
  * The `actor` prefix `auto:<module_name>` distinguishes cron-driven
    flips from operator-driven flips when an operator audits a shipper
    or carrier doc later.
  * The 90-day retention window in `audit_trail_cron` applies, so
    these rows don't accumulate indefinitely.

Both services have a defensive fallback: if `integrity.audit_trail`
is not importable (in tests using bare `_FakeDB` without the
integrity package on path), they fall back to a direct `audit_trail`
collection insert preserving the same schema. This keeps the unit
tests trivial while preserving production behavior.

## Walkthrough wiring (stages 22 + 23)

`backend/tests/synthetic_load_walkthrough.py` stages 22 + 23 were
updated to call the production services instead of inlining the
state-machine logic:

  * **Stage 22:** the test still inserts a `delivered` load row +
    increments `successful_load_count` (these are upstream side-effects
    owned by `audit_gate` / `outcome_tracker`), then calls
    `promote_tiers_for_qualifying_carriers(db, threshold=5)`. Asserts
    on the resulting `tier="preferred"` state + audit_trail row.
  * **Stage 23:** the test still seeds a shipper with
    `credit_score_override=True` + writes a `payment_failed` event,
    then calls `revoke_overrides_for_payment_failures(db)`. Asserts
    on the resulting `credit_score_override=False` state + audit row.

The walkthrough's session-scoped `_FakeDB` is preserved so tests don't
need real Mongo. The `_record_audit` helper in
`synthetic_load_walkthrough.py` is no longer called from stages 22 +
23 (the production service now owns audit emission), but remains in
the file because other stages still use it.

**Test outcome:** all 26 walkthrough tests still pass after the
wiring change.

## Operator HTTP surfaces

| Endpoint | Auth | Returns |
| --- | --- | --- |
| `GET /api/_carrier_tier/recent_promotions?since_hours=72&limit=50` | `require_broker` | audit_trail rows action=carrier_tier_promoted |
| `GET /api/_carrier_tier/_health` | `require_broker` | module health snapshot |
| `GET /api/_credit_override/recent_revocations?since_hours=72&limit=50` | `require_broker` | audit_trail rows action=credit_override_revoked |
| `GET /api/_credit_override/_health` | `require_broker` | module health snapshot |

Both routers are read-only on purpose. Promotion / revocation are
autonomous; an operator who needs to force a flip can mutate the
underlying doc directly or use existing operator-write paths
(carrier_evaluator router for tier, shipper router for override).

## Test coverage (8 new + 26 walkthrough = 34 total)

`backend/tests/test_carrier_tier_promoter.py` (4 tests):

  * `test_promote_only_when_threshold_met` -- verifies threshold
    gating (below + at + above + already-preferred + blacklisted)
  * `test_promote_writes_audit_trail` -- verifies audit row schema
  * `test_promote_idempotent_skips_already_preferred` -- second-run
    no-op proof
  * `test_promote_honors_should_skip_tick` -- read-only +
    crons-paused both short-circuit cleanly

`backend/tests/test_credit_override_revoker.py` (4 tests):

  * `test_revoke_only_on_payment_failed_with_override` -- selection
    rule across 3 shippers (override+failed, override+success,
    no-override+failed)
  * `test_revoke_writes_audit_trail` -- audit schema + event_id in note
  * `test_revoke_skips_when_override_already_false` -- idempotency
  * `test_revoke_honors_should_skip_tick` -- kill-switch path

`backend/tests/synthetic_load_walkthrough.py` stages 22 + 23 -- now
end-to-end on the real services.

## Health surfaces

Both modules register their cron-loop health checks via
`health_monitor.register_health_check`:

  * `carrier_tier_promoter_cron` -- healthy when loop_running +
    last_tick_status in
    `{ok, success, never_run, skipped_read_only, skipped_kill_switch, partial_error}`
  * `credit_override_revoker_cron` -- same shape

These appear in `/api/health/system` so operator-side dashboards
(/api/_health, frontend Health tab) see the cron heartbeat.

## Pre-existing risks NOT addressed by this stream

  1. **`successful_load_count` increment path.** This stream assumes
     `successful_load_count` is incremented somewhere upstream
     (`audit_gate` / `outcome_tracker` on `delivered_load` event).
     If that path drops events, this cron's qualifying-carrier scan
     would miss carriers that semantically deserve promotion. We
     don't audit that upstream path here -- separate stream.
  2. **`payment_events` event emission.** This stream consumes the
     collection but doesn't write to it. The walkthrough stage 23
     simulates the event; production today has multiple potential
     emitters (Mercury ACH-fail webhook, factor portal chargeback,
     `aged_ar` dunning escalate). Verifying ALL of those write to
     `db.payment_events` with the canonical `event_type=payment_failed`
     shape is out-of-scope here.
  3. **Demotion path.** This stream auto-PROMOTES (watch -> preferred)
     but does NOT auto-DEMOTE (preferred -> watch on a string of
     failures). Demotion is more sensitive (revenue impact) and
     deliberately left for a follow-up stream after operator
     reviews promotion behavior in production.

## Follow-ups (priority-ordered)

  1. **HIGH -- Wire `successful_load_count` increment upstream.**
     Audit `audit_gate` / `outcome_tracker` to confirm every
     `delivered_load` event increments
     `carriers[matched_carrier_id].successful_load_count`. If not,
     this cron has nothing to act on. (Effort: 2-3 hr including audit
     + targeted test.)
  2. **MEDIUM -- Add demotion path** (`tier=preferred` -> `tier=watch`
     on N consecutive load failures or vetting-status regression).
     Symmetric to promotion but with stricter guardrails (e.g., audit
     + alert before demoting; operator-confirmation for high-volume
     carriers). (Effort: 1-2 days.)
  3. **MEDIUM -- Frontend visibility.** Add a simple "Recent
     Promotions" + "Recent Override Revocations" widget to the
     operator dashboard, consuming the new
     `/api/_carrier_tier/recent_promotions` +
     `/api/_credit_override/recent_revocations` endpoints. (Effort:
     2-4 hr; pure read-only fetch + table render.)

## Files shipped

  * `backend/carrier_tier_promoter/__init__.py` (~32 lines)
  * `backend/carrier_tier_promoter/tier_promoter.py` (~370 lines)
  * `backend/carrier_tier_promoter/promoter_router.py` (~110 lines)
  * `backend/credit_override_revoker/__init__.py` (~35 lines)
  * `backend/credit_override_revoker/revoker.py` (~400 lines)
  * `backend/credit_override_revoker/revoker_router.py` (~100 lines)
  * `backend/tests/test_carrier_tier_promoter.py` (~370 lines, 4 tests)
  * `backend/tests/test_credit_override_revoker.py` (~390 lines, 4 tests)
  * `backend/cron_scheduler.py` -- 2 cron loops added
    (`carrier_tier_promoter_loop`, `credit_override_revoker_loop`),
    both registered in `start_all`
  * `backend/server.py` -- 2 router mounts added
  * `backend/tests/synthetic_load_walkthrough.py` -- stages 22 + 23
    rewired to call the production services

Test results: 34/34 pass (8 new + 26 walkthrough).
Preflight: only pre-existing missing-deps failures
(reportlab, openai, twilio, stripe, cryptography, etc. -- all
unrelated to this stream).
