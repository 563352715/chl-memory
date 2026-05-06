# Stage Completion — Iter 141.1 Stage 1b

**Stage:** 1b — LaneEvaluator + `/api/lanes/{o_city}/{o_state}/{d_city}/{d_state}/score` endpoint
**Status:** ✅ COMPLETE
**Commit:** [`2a4d167`](https://github.com/563352715/Continental-Haul-Logistics/commit/2a4d167) on CHL `main`
**Completed:** 2026-05-06

## Outcome

Built the meso-scale `LaneEvaluator(FractalEvaluator)` and the `/api/lanes/.../score` endpoint that aggregates 90 days of delivered loads on a given origin-destination pair into the four-parameter framework. Endpoint persists evaluation to `db.lane_scores` (upsert; updated_at refreshes on repeat calls). `business_settings.lane_thresholds` seeded on startup with the FRACTAL_DECISION_FRAMEWORK hypothesis defaults. Module-13 registered in `/api/health/system` (was 12).

Smoke 6/6 PASS, including empty-history lane returning `score=False` with `0.0` metrics (the framework's correct cold-start posture).

## Spec deviations (intentional, both documented in code)

1. **`$setOnInsert` → presence-check + `$set`.** The agenda's `business_settings` initializer used `$setOnInsert: {lane_thresholds: ...}`, which is a no-op when the doc already exists (and it does). Replaced with a presence check + targeted `$set` so the field is actually populated on existing docs. Idempotent. (See `lane_evaluator.ensure_lane_thresholds`.)
2. **`broker_margin_pct` is stored as a percentage value (e.g. `18.0`), not a decimal.** Confirmed by `lane_rate_history.py:107` (`avg_broker_margin_pct` returns the percentage form). LaneEvaluator divides by 100 before returning, so `get_profitability_data` returns a decimal that can be compared directly to `thresholds["margin"] = 0.18`.

Operator-authorized via Path A; documented here as required by the threshold policy.

## Files

| File | Type | Lines | Notes |
|---|---|---|---|
| `backend/lane_evaluator.py` | NEW | ~245 | LaneEvaluator + router + ensure_lane_thresholds + health check |
| `backend/server.py` | MODIFIED | +11 | Per-stage authorized monolith edit (import + init + include_router + on_event seed) |

## Per-action-step summary

### Step 1: lane_evaluator.py module

`LaneEvaluator(FractalEvaluator)`. Four async data methods over `db.loads` aggregations:

| Parameter | Returns | Logic |
|---|---|---|
| profitability | decimal margin from avg(`broker_margin_pct`) | aggregate over delivered loads on lane in 90-day window; `/100` to get decimal |
| reliability | ratio of vetted+mc carriers / total distinct carriers | `db.loads.distinct("carrier_id", lane match delivered)` then count those that satisfy `vetted_trusted=True AND mc_number != null/""` |
| risk | failed_loads / total_loads (capped at 1.0) | denominator: ALL loads on lane in window using `created_at`; numerator: `status IN [cancelled,disputed,cargo_claim] OR has failure_alert` |
| capacity | True iff daily_avg_volume < 20 | `count_documents(delivered) / 90 < 20` |

Helper: `_lane_match(norm, delivered_only, time_field)` builds the canonical Mongo filter (state caps, city case-insensitive regex, `>=` cutoff_iso). `_normalize_lane()` uppercases state codes, trims/preserves city case-insensitively.

`init(db)` sets module-level `_db` ref, mirroring `load_evaluator` / `throttle_system` / `sla_monitor`. Self-registered health check.

### Step 2: /api/lanes/{...}/score endpoint

Module-level `router = APIRouter()`. Single route. Reads `lane_thresholds` from `business_settings`, falls back to `DEFAULT_LANE_THRESHOLDS` if absent. Computes evaluation, decision, and per-metric raw values. Upserts `db.lane_scores` row keyed by the four lane fields. Returns `{lane, score, evaluation, metrics, thresholds}`.

### Step 3: server.py wiring

```python
from lane_evaluator import init as _init_lane_evaluator, router as _lane_router, ensure_lane_thresholds as _ensure_lane_thresholds
_init_lane_evaluator(db)
fastapi_app.include_router(_lane_router, dependencies=[Depends(require_broker)])

@fastapi_app.on_event("startup")
async def _seed_lane_thresholds():
    try:
        await _ensure_lane_thresholds()
    except Exception as _e:
        logging.getLogger("lane_evaluator").warning(f"ensure_lane_thresholds failed: {_e}")
```

11-line addition immediately after the `_init_load_evaluator(db)` block.

### Step 4: lane_thresholds seed

Idempotent on startup via `ensure_lane_thresholds()`. Inserts `{margin: 0.18, reliability: 0.85, risk: 0.08, capacity: 20}` only if the field is absent on the existing `business_settings` doc.

## Pre-flight (8 checks)

| # | Check | Result |
|---|---|---|
| 1 | Working tree + recent commits | ✓ HEAD = `6040b8c` (agentdm bug doc), prior = `c368f73` (stage 1a) |
| 2 | Stage 1a commit `c368f73` present | ✓ |
| 3 | lane_evaluator.py NOT exists | ✓ |
| 4 | Schema: `origin_city/origin_state/destination_city/destination_state` (URL uses `dest_*`) | ✓ confirmed in 20+ files |
| 5 | Schema: `delivered_at` is ISO string, `status="delivered"` | ✓ confirmed in delivery_completion.py + multi_leg.py + server.py |
| 6 | `broker_margin_pct` exists on delivered loads | ✓ confirmed in lane_rate_history.py aggregation |
| 7 | Backend service running | ✓ Status=Running, /api/health/system 12 modules baseline |
| 8 | Stage 1a modules registered (fractal_evaluator + load_evaluator) | ✓ both healthy |

## Smoke results

Harness: `backend/.smoke_141_1_1b.py` (deleted post-run).

| # | Test | Method | Expected | Actual | Status |
|---|---|---|---|---|---|
| 1 | Endpoint returns dict (in-process call) | direct `await lane_score_endpoint("Chicago","IL","Dallas","TX")` | dict with `score` key | dict; score=False (no Chicago→Dallas history) | ✅ PASS |
| 2 | Response has required keys | set check | `{score, evaluation, metrics, thresholds, lane}` | all present | ✅ PASS |
| 3 | db.lane_scores entry persisted | `find_one` | row exists | updated_at=2026-05-06T19:30:14.967Z | ✅ PASS |
| 4 | Repeat call updates updated_at | second call → compare | timestamp differs | first ≠ second by 93ms | ✅ PASS |
| 5 | Empty-history lane (`ZZZZZZ/ZZ/YYYYYY/YY`) | call score endpoint | score=False, all metrics 0.0, capacity=True | matches exactly | ✅ PASS |
| 6 | Briefing regression (HTTP, JWT-minted broker token) | GET `/api/ops/briefing?force=true` | HTTP 200, source=llm | HTTP 200 in 14988ms; source=llm | ✅ PASS |

Smoke harness needed `dotenv_values` to read backend/.env so it used `chl_local` (the running service's DB) not `test_database` (the venv default). Briefing test mints a 10-min JWT against an existing broker user using the JWT_SECRET from `.env`.

## Verify yourself

```powershell
git log --oneline -3
# Expected:
#   2a4d167 iter 141.1 stage 1b: LaneEvaluator + /api/lanes/{o}/{s}/{d}/{s}/score endpoint
#   6040b8c docs: AgentDM bug report standalone doc (mirrored to chl-memory for public fetch)
#   c368f73 iter 141.1 stage 1a: FractalEvaluator base + LoadEvaluator (shadow mode in auto_dispatch)

# Module count went from 12 -> 13
Invoke-RestMethod -Uri http://127.0.0.1:8001/api/health/system |
  Select-Object -ExpandProperty modules | Get-Member -MemberType NoteProperty |
  Where-Object Name -eq 'lane_evaluator'

# business_settings was seeded
& "C:\CHL\.venv-local\Scripts\python.exe" -c "
import asyncio, os
from motor.motor_asyncio import AsyncIOMotorClient
async def t():
    db = AsyncIOMotorClient('mongodb://localhost:27017')['chl_local']
    s = await db.business_settings.find_one({})
    print(s.get('lane_thresholds'))
asyncio.run(t())
"
# Expected: {'margin': 0.18, 'reliability': 0.85, 'risk': 0.08, 'capacity': 20}
```

## Caveats

1. **`broker_margin_pct` storage convention.** Encoded as percentage (e.g. `18.0`), not decimal. LaneEvaluator divides by 100 in `get_profitability_data`. If a future writer ever stores it as a decimal, the lane scoring will under-report margin by 100×. Mitigation: a single canonical helper or a stored-as-decimal migration. Out of scope for stage 1b.
2. **Reliability is total-distinct-on-lane, not weighted by volume.** A carrier that ran 20 loads on the lane counts the same as one that ran 1. Stage 1c+ may want to volume-weight if shadow data shows the unweighted form is misleading.
3. **Risk uses `created_at` cutoff for the denominator** (so failed loads that never delivered are still counted), but **delivered_at for the profitability/reliability/capacity numerators**. This is intentional but worth re-validating once shadow data exists — the time-field asymmetry could double-count cancellations.
4. **No min-sample gate.** A lane with 1 delivered load gets a score; the `is_reliable` concept from `lane_rate_history.py` (MIN_LOADS_FOR_RELIABLE=10) is not yet applied at the LaneEvaluator level. Stage 1c (the background loop) is a natural place to add `sample_size` to the persisted row so dashboards (1e) can warn on thin lanes.
5. **Endpoint is broker-only.** `dependencies=[Depends(require_broker)]` on the include. Public/ops dashboards (stage 1e) inherit this gate.
6. **No automatic recompute.** Each call recomputes from scratch. Stage 1c will add the 6h cron to populate `db.lane_scores` ahead-of-time so dashboards don't pay the aggregation cost on every render.

## Open items / next stage

Stage 1b is COMPLETE. Stage 1c is next (lane scoring background loop, 6h cron, batch population of `db.lane_scores`):
- Background task on app startup (separate from existing `_seed_lane_thresholds` startup)
- Iterate distinct lanes in `db.loads`, recompute, upsert
- 6-hour cadence (matches `lane_rate_history` cache TTL convention)

Pre-conditions for entering 1c:
- 1b commit landed: `2a4d167` ✓
- Smoke green: 6/6 ✓
- LaneEvaluator framework live + endpoint responding ✓
- `db.lane_scores` collection writable (verified by tests 3+4) ✓

Handoff: this file (`progress/stage_completion/iter_141_1_stage_1b.md`).

DONE
