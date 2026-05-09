# LaneEvaluator ETA-accuracy extension (Stream X eta_accuracy hook)

**Date:** 2026-05-08
**Stream:** Sub-agent SS2 (lane_evaluator.eta_accuracy field extension)
**Trigger:** QQ3 stage 24 wrote ETA-slip reconciliations to a parallel
`db.lane_eta_accuracy` collection because `FractalEvaluator.get_reliability_data`
exposed only a scalar `float` for reliability and had no per-(carrier, lane)
ETA-slip metric. This stream closes the gap.

---

## 1. Problem statement

The QQ3 stage 24 walkthrough block surfaced a documented hook gap:

> "production lane_evaluator does NOT have an eta_accuracy field today --
>  recommend extending FractalEvaluator.get_reliability_data with an
>  eta-accuracy sub-component, or adding a parallel lane_eta_accuracy
>  collection."

QQ3 wrote to a parallel collection (`db.lane_eta_accuracy`) with an
`{lane_key, sample_count, total_slip_minutes, slip_breach_count, ...}`
shape. That worked for the walkthrough, but two costs accrued:

1. **Reliability scoring couldn't see ETA performance.** LaneEvaluator's
   `get_reliability_data` returns a scalar (`vetted+mc carriers / total
   carriers`) -- the model has no signal for "this lane consistently
   delivers 75 minutes late." Risk-rated scoring is therefore blind to
   ETA breaches even when the data exists in the parallel collection.
2. **Two writers, one collection.** QQ3 stage 24 wrote ad-hoc per-load
   reconciliations. Any future writer (re-dispatch loop, post-delivery
   reconciler, GPS check-call processor) would also need a write hook,
   and the schema was load-driven (`$inc total_slip_minutes`) rather
   than rolling-window aggregated.

This stream introduces an authoritative source-of-truth for the
`eta_accuracy` metric and wires it into the LaneEvaluator-side
reliability surface.

---

## 2. Schema extension

### 2.1 Canonical `eta_accuracy` sub-dict

The new shape returned by `get_lane_eta_accuracy()` and embedded in
`lane_eta_accuracy` rows under the `eta_accuracy` key:

```python
{
  "predicted_vs_actual_avg_min": float,    # signed; positive = late
  "predicted_vs_actual_p95_min": float,    # 95th percentile slip (nearest-rank)
  "sample_size": int,                      # qualifying loads in the window
  "window_days": int,                      # rolling window the metric covers
  "last_updated": iso,                     # write timestamp
}
```

### 2.2 Extended-reliability dict (additive to FractalEvaluator)

Returned by `lane_evaluator_eta_accuracy.get_reliability_data_extended()`.
This is the OPT-IN extended path; the base
`FractalEvaluator.get_reliability_data` still returns a `float` and
existing `evaluate()` / `decide()` consumers keep working.

```python
{
  "on_time_rate": float,              # legacy LaneEvaluator scalar (vetted+mc carriers / total)
  "successful_delivery_count": int,   # delivered loads in the same window
  "eta_accuracy": { ...above shape... },
}
```

### 2.3 `db.lane_eta_accuracy` row shape (NEW canonical)

The cron upserts using this key tuple:

- `carrier_id` (or `None` for lane-aggregate rows; cf. follow-up #2)
- `origin_state` (uppercased)
- `destination_state` (uppercased)

Top-level fields written:

```
{
  "carrier_id": str | None,
  "origin_state": str,
  "destination_state": str,
  "eta_accuracy": { ...sub-dict... },
  "predicted_vs_actual_avg_min": float,    # mirror, for $sort/index queries
  "predicted_vs_actual_p95_min": float,
  "sample_size": int,
  "window_days": int,
  "last_updated": iso,
  "first_observed": iso,                   # $setOnInsert
}
```

The legacy QQ3 stage-24 fields (`lane_key`, `sample_count`,
`total_slip_minutes`, `slip_breach_count`) are NOT removed -- the
parallel-write path stays in place this stream per operator-confirmed
"migration path needed first" constraint. See section 6 for the
deprecation plan.

---

## 3. Sampling logic

`compute_eta_accuracy(db, carrier_id, origin_state, dest_state, window_days)`
runs the canonical computation:

1. **Cutoff:** `now - window_days`. Default window = 30 days (lane stats
   are slow-moving; 30 days is enough samples without too much weight on
   stale carriers/seasons).
2. **Filter:** `status="delivered"`, lane match by uppercased state codes,
   `delivered_at >= cutoff`. Optional `carrier_id` filter; with fallback
   to `matched_carrier_mc` when the explicit filter yields zero rows
   (legacy load shape carries the MC token but no `carrier_id` link).
3. **Per-load delta:** `(actual_arrival - predicted_eta).total_seconds() / 60.0`,
   signed. Loads missing either timestamp are skipped (NOT zero-counted;
   absence of the prediction is not a slip signal).
4. **Aggregations:**
   - `predicted_vs_actual_avg_min = mean(deltas)` -- sensitive to outliers
     by design; we WANT a single 4-hour breach to show up.
   - `predicted_vs_actual_p95_min` = 95th percentile, nearest-rank. See
     section 4.
   - `sample_size = len(deltas)`.
5. **Zero-fallback:** zero deltas -> canonical zero-shape with
   `sample_size=0`. Consumers MUST check `sample_size` before trusting
   the avg/p95 -- with `sample_size=0` the values are zeros by
   convention, not signals.

### 3.1 Why state-only lane matching, not city?

LaneEvaluator's `_normalize_lane` matches city + state with a regex.
The eta_accuracy cron pulls a coarser view (state-only) intentionally:

- Sample size on a city-pair is often <10 even at 30 days; p95 from 3
  samples is noise, not signal.
- The downstream consumer (re-dispatch evaluator, carrier scorecard) is
  already blending eta_accuracy with the city-pair-level reliability
  scalar; adding a state-pair signal alongside the city-pair signal
  gives the model two complementary views.

The base `LaneEvaluator.get_reliability_data` still does city-pair
matching for the float scalar; only the `eta_accuracy` sub-dict
generalizes to state-pair. Follow-up #2 covers refining this when
sample size warrants it.

---

## 4. p95 calculation

Nearest-rank percentile: sort the deltas ascending; the p95 sample is
at index `ceil(0.95 * N + 0.5) - 1`, clamped to `[0, N-1]`. For small N
this returns the largest sample (sensible -- with 5 samples, "95th
percentile" naturally collapses to "the worst").

We chose nearest-rank over interpolation because:

1. **Interpretability.** "p95 = 120 min" means an actual delivery in the
   sample was 120 minutes late, not an interpolated value.
2. **Robustness to small N.** The metric stays sensible for new lanes
   even with 3-5 samples, while interpolation can produce values below
   the worst observed delta.
3. **Cheap.** No interpolation function dependency; pure `sorted()` +
   index math works in the existing async-Mongo fake without
   monkey-patching.

The implementation lives in `_percentile()` in
`backend/lane_evaluator_eta_accuracy.py`.

---

## 5. Wire-up to FractalEvaluator

### 5.1 Constraint: no breaking changes to the base contract

`FractalEvaluator.get_reliability_data` is `async def -> float`. Multiple
consumers compare it against `thresholds["reliability"]` with `>=`. Any
change to a dict return would silently coerce in Python (`dict >= float`
raises `TypeError`) and crash all current LaneEvaluator-using code paths.

We therefore did NOT modify the abstract base. Instead:

1. The base `LaneEvaluator.get_reliability_data` continues to return a
   float (vetted-carrier ratio).
2. A new module-level helper,
   `lane_evaluator_eta_accuracy.get_reliability_data_extended(db, lane,
   carrier_id, window_days)`, returns the EXTENDED dict with
   `eta_accuracy`. Consumers who want the new signal opt in by calling
   the helper instead of (or alongside) the base.
3. Existing `evaluate()` / `decide()` paths remain untouched.

This satisfies the "additive only" constraint AND the "DO NOT modify the
FractalEvaluator base contract beyond adding the optional sub-field"
constraint -- the optional sub-field is exposed via the helper, not by
mutating the abstract method.

### 5.2 Future: rolling eta_accuracy into the reliability scalar

Once cron has been running long enough to populate rows for active
lanes, the base `LaneEvaluator.get_reliability_data` can blend in the
eta_accuracy signal:

```python
async def get_reliability_data(self, lane):
    base = await self._vetted_carrier_ratio(lane)         # current logic
    eta = await get_lane_eta_accuracy(db=db, ...)
    if eta["sample_size"] >= 10:
        # penalize chronic late-running lanes by up to 30%
        penalty = min(0.3, max(0.0, eta["predicted_vs_actual_p95_min"] / 240.0))
        return base * (1.0 - penalty)
    return base
```

This is follow-up #1 (LaneEvaluator scoring weight integration). It
deliberately does NOT ship in this stream; integrating into the scoring
weight changes the meaning of the `reliability` threshold and needs an
operator-side decision on the penalty curve.

---

## 6. Deprecation path for QQ3's parallel collection

The QQ3 stage 24 parallel-write path stays untouched in this stream. The
canonical `lane_eta_accuracy` collection now has TWO row schemas:

1. **NEW canonical** (written by `update_lane_eta_accuracy_cron`):
   keyed by `(carrier_id, origin_state, destination_state)`, embeds the
   canonical `eta_accuracy` sub-dict.
2. **LEGACY QQ3** (written by walkthrough stage 24 BEFORE this stream;
   migration target): keyed by `lane_key` (Springfield_MO__Memphis_TN__MC-X),
   incremental `sample_count` + `total_slip_minutes` + `slip_breach_count`.

`get_lane_eta_accuracy()` is forwards-compatible: it tries the canonical
shape first, then falls back to reading top-level legacy fields if
`row.eta_accuracy` is absent. New consumers should always read the
sub-dict.

### Migration plan (follow-up stream)

1. **Phase A** (this stream): canonical writer in production; legacy
   QQ3 stage 24 walkthrough block converted to call the canonical API
   (no longer writes the legacy shape to the production DB; walkthrough
   asserts only the canonical shape).
2. **Phase B** (next stream): one-shot back-fill script that scans
   `db.loads` for the trailing 30 days, recomputes per
   `(carrier_id, lane)` tuple, and upserts into the canonical schema.
3. **Phase C** (after Phase B + 1 week of clean cron runs): drop the
   legacy `lane_key` / `sample_count` / `total_slip_minutes` /
   `slip_breach_count` fields. Update the
   `get_lane_eta_accuracy()` legacy fallback to remove the dual-shape
   handling.
4. **Phase D** (post-cleanup): delete any orphan rows where neither the
   new nor legacy keys are populated.

Each phase is independently reversible (the canonical writer is purely
additive; the walkthrough stage assertion change is a code-only edit;
the back-fill is read-only off `db.loads`).

---

## 7. Cron registration

| Setting              | Value     | Rationale |
|----------------------|-----------|-----------|
| `LOOP_INTERVAL_SEC`  | 21600 (6h)| Lane stats are slow-moving; 6h matches `lane_scoring_cron` so the two crons stay in lockstep on refresh granularity. Also matches the renewals cron in `cron_scheduler.py` for the same reason (day-granular signals get nothing from sub-hour polling). |
| `INITIAL_DELAY_SEC`  | 1200 (20m)| Cold-start delay so DB connection settles + other crons (lane scoring, throttle, SLA) arm first. Long enough that boot logs aren't dominated by eta_accuracy chatter. |
| `MAX_TUPLES_PER_TICK`| 1000      | Hard cap on the active-tuple enumeration; protects against runaway lanes. The active-window query pulls delivered loads in the trailing 30 days; even a high-volume broker rarely exceeds 1000 distinct (carrier, lane) tuples. |
| Read-only honored    | yes       | `is_read_only()` -> skipped_read_only |
| Crons-paused honored | yes       | `crons_paused()` -> skipped_crons_paused |
| `should_skip_tick`   | yes       | OR of the above; checked at the top of every tick |
| Flag-gated           | NO        | This is regular production data infra (operator-confirmed in the stream brief); not feature-flagged. |

The cron is wired via `attach_to_app(app, db)` in
`lane_evaluator_eta_accuracy.py`, mirroring the `lane_scoring_cron`
pattern. NOT wired into `server.py` in this stream because the brief
constrains "DO NOT commit code"; operator-side wire-up will run after
the doc commit.

---

## 8. Walkthrough stage 24 wiring

Before this stream, stage 24 wrote directly to `db.lane_eta_accuracy`
with the legacy shape and asserted on `sample_count` /
`slip_breach_count`. After:

- The seeded load row is the same (predicted_eta + actual_arrival 75min
  late on Springfield_MO -> Memphis_TN with carrier `MC-WALK-9999`).
- Stage 24 now calls `update_lane_eta_accuracy_cron(db=db)` instead of
  inlining the metric write. The cron enumerates the active tuple
  `(MC-WALK-9999, MO, TN)` from `db.loads` (using the
  `matched_carrier_mc` -> `carrier_id` fallback in
  `_enumerate_active_tuples`) and upserts the canonical row.
- Stage 24 then calls `get_reliability_data_extended(...)` to verify
  the eta_accuracy sub-dict is surfaced to consumers.
- Per-load `eta_reconciliations` row write + audit row stay (forensic
  record of the per-load decision; complements the rolling metric).

Test-side outcome: 26 walkthrough tests pass (including new stage 24
asserts on the canonical sub-dict shape) plus 6 new unit tests = 32/32.

---

## 9. Files

- `backend/lane_evaluator_eta_accuracy.py` -- new module, ~485 lines,
  the eta_accuracy + cron + extended-reliability surface.
- `backend/tests/test_lane_eta_accuracy.py` -- new tests, ~330 lines,
  6 tests.
- `backend/tests/synthetic_load_walkthrough.py` -- stage 24 rewired to
  call the canonical API.
- `chl-memory/research/lane_evaluator_eta_accuracy_extension.md` -- this
  doc.

Naming: the module is `lane_evaluator_eta_accuracy.py` (sibling) rather
than `lane_evaluator/eta_accuracy.py` (sub-package) because the existing
`lane_evaluator.py` is a flat module; converting to a package would
require renaming to `lane_evaluator/__init__.py` AND deleting the
`.py` file (Python prefers the package over the same-named .py), which
is a riskier change than the brief warrants. The sibling-module path
keeps imports stable and lets the package conversion happen in a future
stream when more lane_evaluator-adjacent surfaces accumulate.

---

## 10. Three highest-priority follow-ups

1. **LaneEvaluator scoring-weight integration** -- blend
   `eta_accuracy` into `LaneEvaluator.get_reliability_data` once the
   cron has populated enough rows. Decide the penalty curve with the
   operator. (Section 5.2.)
2. **Carrier-specific vs lane-aggregate rows** -- the cron currently
   writes one row per `(carrier_id, lane)` tuple. Add a parallel
   `(None, lane)` lane-aggregate row that pools across carriers, so
   consumers can fall back to the lane average when a carrier is
   new on a lane. The `get_lane_eta_accuracy()` helper already has the
   carrier-then-lane fallback wired; the cron just needs to emit both.
3. **On-time-percentage with grace-period bands** -- the avg/p95 are
   useful but operations also wants a "what % of loads delivered within
   30 min of ETA?" metric. Add `on_time_rate_30min`, `on_time_rate_60min`,
   `on_time_rate_120min` bands to the canonical sub-dict so the
   carrier-tier-promoter and rate-con re-dispatch paths can decide on
   thresholds rather than averages. Backwards-compatible (new fields,
   default 0.0).
