# Stage Completion — Iter 141.1 Stage 1a

**Stage:** 1a — FractalEvaluator base class + LoadEvaluator (shadow-mode integration into auto_dispatch)
**Status:** ✅ COMPLETE
**Commit:** [`c368f73`](https://github.com/563352715/Continental-Haul-Logistics/commit/c368f73) on CHL `main`
**Completed:** 2026-05-06

## Outcome

Built the fractal decision framework foundation: net-new `fractal_evaluator.py` (abstract base class with 4 abstract methods + concrete `evaluate`/`decide`/`get_rejection_reason`) and `load_evaluator.py` (concrete `LoadEvaluator` mirroring `auto_dispatch.try_auto_accept` gate semantics into the four-parameter framework). Wired into `auto_dispatch.try_auto_accept` in **shadow mode** — production logic untouched, LoadEvaluator runs alongside, result stashed in `interest["_shadow_load_evaluator"]` and captured by `_log_decision`. Smoke 7/7 PASS, including 3 synthetic accept/reject scenarios that exercise margin / risk-flag / blocklist gates.

## Spec deviation: shadow mode instead of full refactor

The agenda's stage 1a action step 3 specified replacing `try_auto_accept`'s body with `LoadEvaluator.decide()`. Two issues with that approach:

1. **Wrong signature.** Spec's "Before" code assumed `try_auto_accept(load_id, carrier_id)`; actual signature is `try_auto_accept(db, interest, load, carrier)` (verified at `auto_dispatch.py:102`). Same speculative-naming pattern as iter 139.46 stage 6a — PM Claude wrote the spec from idealized memory, not the live code.
2. **Different gate set.** Spec assumed `MAX_CONCURRENT_LOADS=50` capacity gate (not in prod); spec missed shipper vetting + onboarding_complete gates (in prod). Full body replacement would have been a behavior CHANGE, not preservation.

**Resolution: shadow mode.** Production `for ok, reason in checks` loop unchanged. LoadEvaluator runs in parallel right before that loop, stashes its decision in `interest["_shadow_load_evaluator"]`, and `_log_decision` captures it alongside the prod outcome. Try/except wraps the shadow call so any LoadEvaluator failure logs but does not affect dispatch. This is the canary-deployment / canonical refactor-safety pattern. Once N production loads have logged shadow-vs-prod agreement, iter 141.2+ can migrate prod to LoadEvaluator-only with high confidence.

Operator-authorized via Path A; documented here as required by the threshold policy.

## Files

| File | Type | Lines | Notes |
|---|---|---|---|
| `backend/fractal_evaluator.py` | NEW | ~115 | Pure abstract base class — no DB / IO at module level |
| `backend/load_evaluator.py` | NEW | ~155 | `init(db)` pattern matching throttle_system + sla_monitor |
| `backend/auto_dispatch.py` | MODIFIED | +28 | Shadow-mode insertion before prod gate-loop |
| `backend/server.py` | MODIFIED | +3 | Per-stage authorized monolith edit (import + `init(db)` call) |

## Per-action-step summary

### Step 1: fractal_evaluator.py

`FractalEvaluator(ABC, Generic[EntityType])`. Four `@abstractmethod` async methods (`get_profitability_data`, `get_reliability_data`, `get_risk_data`, `get_capacity_data`). Concrete `evaluate(entity, thresholds) -> Dict[str, bool]`, `decide(entity, thresholds) -> bool`, `get_rejection_reason(entity, thresholds) -> str`. Self-registered health check.

### Step 2: load_evaluator.py

`LoadEvaluator(FractalEvaluator)`. Mirrors `auto_dispatch.try_auto_accept` gate semantics into the four parameters:

| Parameter | Returns | Gate mapping |
|---|---|---|
| profitability | computed margin (decimal) from `rate` + `effective_carrier_pay` | prod `min_margin_floor_pct` check |
| reliability | 1.0 iff (vetted_trusted AND mc_number AND onboarding_complete AND no recent failure_alerts AND shipper vetted if required); else 0.0 | combined prod gates 3-5 + 90-day failure_alert lookup + optional shipper vetting |
| risk | blocklist=1.0 / risk_flags=0.5 / rate_above_max=0.5 / clean=0.0 | prod blocklist + respect_load_risk_flags + max_auto_rate gates |
| capacity | True iff `load.status == "available"` | prod freshness gate (only operational capacity signal in current code) |

`init(db)` sets module-level db ref. Self-registered health check.

### Step 3: auto_dispatch.py shadow-mode insertion

Insert point: just before `for ok, reason in checks:` loop (line ~197). After cfg + carrier are loaded, after `effective_carrier_pay` is computed:

```python
try:
    from load_evaluator import LoadEvaluator as _LoadEvaluator
    _le_entity = {**load, "carrier_id": carrier.get("id"), ...}
    _le_thresholds = {"margin": float(cfg["min_margin_floor_pct"])/100.0,
                      "reliability": 0.9, "risk": 0.5}
    _le_evaluator = _LoadEvaluator()
    _le_accept = await _le_evaluator.decide(_le_entity, _le_thresholds)
    interest["_shadow_load_evaluator"] = {"accept": _le_accept, "reason": ...}
except Exception as _le_err:
    interest["_shadow_load_evaluator"] = {"accept": None, "error": ...}
```

Production logic following this block is unchanged. `_log_decision` (called from the gate-loop reject path AND from the all-rules-passed accept path) writes `interest` to `db.auto_accept_log`, so the shadow result is captured both ways.

### Step 4: server.py wiring

```python
# LoadEvaluator (iter 141.1 stage 1a) — fractal evaluator framework, shadow-mode in auto_dispatch
from load_evaluator import init as _init_load_evaluator
_init_load_evaluator(db)
```

3 lines added immediately after the iter 140.1 throttle-system attach call. Standard import + side-effect. Per-stage authorized monolith edit.

## Pre-flight (10 checks)

| # | Check | Result |
|---|---|---|
| 1 | Working tree + recent commits | ✓ HEAD = `15c370d` (fractal doc) |
| 2 | fractal_evaluator.py NOT exists | ✓ |
| 3 | load_evaluator.py NOT exists | ✓ |
| 4 | auto_dispatch.try_auto_accept signature | ✓ confirmed `(db, interest, load, carrier)` — agenda spec was wrong |
| 5 | business_settings shape | ⚠️ first doc lacked `master_enabled` etc.; not blocking shadow mode (prod auto_dispatch has its own `get_config(db)` with defaults) |
| 6 | Throttle endpoint | ✓ GREEN (verified earlier) |
| 7 | Health endpoint | ✓ degraded baseline (Twilio + FMCSA missing optional creds) |
| 8 | SLA endpoint | ✓ |
| 9 | auto_accept_log baseline rows | 0 |
| 10 | Backend service | ✓ Running |

## Smoke results

| # | Test | Method | Expected | Actual | Status |
|---|---|---|---|---|---|
| 1 | Inheritance | `issubclass(LoadEvaluator, FractalEvaluator)` | True | True | ✅ PASS |
| 2 | All 4 abstract methods implemented + callable | direct attribute checks | True | True | ✅ PASS |
| 3 | ACCEPT path: 25% margin | `LoadEvaluator.decide(clean_load, threshold=0.15 margin)` | True | True; margin=25.00% | ✅ PASS |
| 4 | REJECT: 5% margin | low-margin synthetic load | False with reason | False; "margin 5.0% < threshold 15.0%" | ✅ PASS |
| 5 | REJECT: risk_flags | synthetic with `risk_flags=["double_broker_suspect"]` | False, risk≥0.5 | False; risk_score=0.5 | ✅ PASS |
| 6 | Health registration | `/api/health/system` includes both new modules | both healthy | total_count=12 (was 10), fractal_evaluator + load_evaluator both `healthy` | ✅ PASS |
| 7 | Briefing regression | GET `/api/ops/briefing?force=true` | 200, source=llm | HTTP 200 in 12303ms; source=llm | ✅ PASS |

Smoke harness `backend/.smoke_141_1_1a.py` deleted post-run.

## Verify yourself

```powershell
git log --oneline -3
# Expected:
#   c368f73 iter 141.1 stage 1a: FractalEvaluator base + LoadEvaluator (shadow mode in auto_dispatch)
#   15c370d docs: add FRACTAL_DECISION_FRAMEWORK.md to memory (architectural alignment)
#   2ed7c21 iter 140.1 close: handoff doc — Phase 7 foundation established

# Modules registered
Invoke-RestMethod -Uri http://127.0.0.1:8001/api/health/system |
  Select-Object -ExpandProperty modules | Get-Member -MemberType NoteProperty |
  Where-Object Name -in 'fractal_evaluator','load_evaluator'
# Expected: both NoteProperty members present

# Direct functional test
& "C:\CHL\.venv-local\Scripts\python.exe" -c "
import asyncio, sys
sys.path.insert(0, r'C:\CHL\backend')
from load_evaluator import LoadEvaluator
async def t():
    ev = LoadEvaluator()
    load = {'rate': 2000.0, 'effective_carrier_pay': 1500.0, 'risk_flags': [], 'status': 'available', 'cfg': {}, 'carrier_id': None}
    result = await ev.decide(load, {'margin': 0.15, 'reliability': 0.0, 'risk': 0.5})
    margin = await ev.get_profitability_data(load)
    print(f'decide={result} margin={margin:.2%}')
asyncio.run(t())
"
# Expected: decide=True margin=25.00%
```

End-to-end (production hot-path will produce shadow data after this stage):
```javascript
// On https://continentalhaul.com after a real carrier interest fires
fetch('/api/auto-accept/log', { credentials: 'include' })
  .then(r => r.json())
  .then(j => console.log(j.entries[0]?._shadow_load_evaluator))
// Expected: {accept: true|false, reason: "..."} field present in newest log entry
```

## Caveats

1. **Shadow mode only — prod still owns the decision.** LoadEvaluator does NOT short-circuit `try_auto_accept`. Any disagreement between LoadEvaluator and prod gates is logged but doesn't change dispatch. Migrate to LoadEvaluator-only after iter 141.2+ when shadow agreement is proven across N real loads.
2. **Reliability is binary in stage 1a (1.0/0.0).** Either all reliability gates pass or none. Stage 1b will add a graduated reliability score for lane-level (e.g., fraction of reliable carriers active on the lane).
3. **Capacity uses freshness as proxy** — `load.status == "available"`. Auto_dispatch has no concurrent-load gate currently. Future work could add a real capacity check (operator bandwidth, max concurrent loads, etc.).
4. **business_settings shape note.** Pre-flight check 5 surfaced that `find_one({})` on `business_settings` returned a doc lacking `master_enabled` etc. Likely there's a config doc with `_id == "auto_accept"` separately fetched by `auto_dispatch.get_config(db)`. Not blocking — LoadEvaluator reads cfg from the entity dict (caller-supplied), so the LoadEvaluator class itself doesn't depend on collection layout. Stage 1b should verify `get_config()` returns the right fields when LaneEvaluator is wired in.
5. **shadow_load_evaluator field bloats interest dict.** Each `_log_decision` writes a slightly larger row to `db.auto_accept_log`. Acceptable — at 100 dispatches/day, that's ~5 KB/day extra. Negligible.
6. **No shadow-vs-prod comparison logic this stage.** The two decisions are both logged but no automated comparison runs yet. Iter 141.2+ should add a job that scans recent logs and reports disagreement rate. For now, the operator can ad-hoc query `db.auto_accept_log` to see how shadow tracks prod.

## Open items / next stage

Stage 1a is COMPLETE. Stage 1b is next:
- New module `backend/lane_evaluator.py` (LaneEvaluator(FractalEvaluator), 90-day origin-destination aggregation)
- New endpoint `/api/lanes/{origin_city}/{origin_state}/{dest_city}/{dest_state}/score`
- Adapts the four-parameter framework to lane-level (per FRACTAL_DECISION_FRAMEWORK.md meso scale)

Pre-conditions for entering 1b:
- 1a commit landed: `c368f73` ✓
- Smoke green: 7/7 ✓
- LoadEvaluator + FractalEvaluator framework live ✓

**PAUSED for operator agentdm appointment (3 PM CT).** Stages 1b/1c/1e queued for resumption after.

Handoff: `progress/stage_completion/iter_141_1_stage_1a.md` (~9 KB)

DONE
