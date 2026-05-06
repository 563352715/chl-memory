# Stage Completion — Iter 140.1 Stage 1c

**Stage:** 1c — SLA monitor
**Status:** ✅ COMPLETE
**Commit:** [`f8c36b6`](https://github.com/563352715/Continental-Haul-Logistics/commit/f8c36b6) on CHL `main`
**Completed:** 2026-05-06

## Outcome

Built per-operation SLA tracking infrastructure that feeds the throttle state machine. `@track_sla(operation)` decorator wraps async/sync functions, records duration into `db.sla_metrics`, and forwards into throttle's rolling op buffer. Background loop computes p50/p95/p99 + miss_rate per operation every 5 minutes. Two critical operations (`rfq_parse` and `pod_ocr`) instrumented at this stage; remaining 4 targets defined and ready for stage 1d wiring (`carrier_assign`, `dispatch_packet`, `load_eval`, `invoice_gen`). Throttle now consumes SLA report via `get_sla=build_report` callable. Smoke 7/7 PASS.

## Files

| File | Type | Lines | Source |
|---|---|---|---|
| `backend/sla_monitor.py` | NEW | ~390 | adapted from emergent scaffolding (322 lines) + attach_to_app + self-health |
| `backend/rfq_inbound.py` | MODIFIED | +2 lines | `from sla_monitor import track_sla` + `@track_sla("rfq_parse")` |
| `backend/load_completion_workflow.py` | MODIFIED | +2 lines | `from sla_monitor import track_sla` + `@track_sla("pod_ocr")` |
| `backend/server.py` | MODIFIED | +3 lines | per-stage authorized monolith edit (SLA import + attach + get_sla parameter) |

## SLA targets

| Operation | Target (ms) | Description | Instrumented? |
|---|---|---|---|
| `rfq_parse` | 4,000 | gpt-4o vision RFQ extraction (`rfq_inbound._parse_with_ai`) | ✅ this stage |
| `pod_ocr` | 10,000 | POD OCR via gpt-4o vision (`load_completion_workflow.run_pod_ocr_via_gemini`) | ✅ this stage |
| `load_eval` | 60,000 | quote draft generation | ⏳ stage 1d |
| `carrier_assign` | 300,000 | auto_dispatch carrier match | ⏳ stage 1d |
| `dispatch_packet` | 600,000 | rate confirmation generation | ⏳ stage 1d |
| `invoice_gen` | 3,600,000 | broker invoice + NOA build | ⏳ stage 1d |

## Tunables (env)

- `SLA_WINDOW_MIN` (default 60) — rolling window for summarize/build_report
- `SLA_LOOP_INTERVAL_SEC` (default 300) — background loop cadence
- `SLA_DEGRADE_MISS_RATE` (default 0.20) — threshold to nudge throttle toward degrade
- `SLA_RECOVER_MISS_RATE` (default 0.05) — threshold for "overall_healthy"

## Per-action-step summary

### Step 1: sla_monitor.py module

Adopted emergent scaffolding (~322 lines) with these additions:
- `attach_to_app(app, db, require_broker)` — single-call wiring for FastAPI
- `_health_check_sla_monitor()` — self-registered with health_monitor
- (otherwise unchanged from scaffolding)

### Step 2: Decorator instrumentation

```python
# rfq_inbound.py
from sla_monitor import track_sla
@track_sla("rfq_parse")
async def _parse_with_ai(*, body_text, attachments) -> _RFQExtract: ...

# load_completion_workflow.py
from sla_monitor import track_sla
@track_sla("pod_ocr")
async def run_pod_ocr_via_gemini(file_bytes, content_type) -> Dict[str, Any]: ...
```

### Step 3: server.py wiring

```python
# Stage 1c: SLA monitor (must be before throttle so build_report is available as get_sla)
from sla_monitor import attach_to_app as _attach_sla, build_report as _sla_build_report
_attach_sla(fastapi_app, db=db, require_broker=require_broker)

# Stage 1b: throttle (now passes get_sla)
from throttle_system import attach_to_app as _attach_throttle
_attach_throttle(fastapi_app, db=db, get_health=_get_health_dict, get_sla=_sla_build_report, require_broker=require_broker)
```

### Step 4: Background loop nudges throttle

`sla_monitor._loop()` runs every 5min; calls `throttle_system.trigger_evaluation()` when `worst_miss_rate >= 20%` (degrade) or `<= 5%` (recover). This is loose coupling: the loop just nudges; throttle's state machine decides what to do based on the SLA report it pulls.

## Pre-flight (per stage_1c.md spec)

| # | Check | Expected | Actual |
|---|---|---|---|
| 1 | Stages 1a+1b complete | both green | ✓ HEAD = `2ab45ab` |
| 2 | sla_monitor.py NOT exists | False | ✓ |

## Smoke results

| # | Test | Method | Expected | Actual | Status |
|---|---|---|---|---|---|
| 1 | Direct timing capture | record 10 timings (5 over target) + summarize | sample=10, miss=5, miss_rate=50% | sample_count=10, miss_count=5, miss_rate=50%, p50=3500ms, p95=6000ms | ✅ PASS |
| 2 | build_report aggregation | call build_report, check all targets | 6 ops, worst=rfq_parse | worst_op=rfq_parse, worst_miss_rate=50%, all 6 ops present | ✅ PASS |
| 3 | `/api/sla/summary` endpoint | GET broker auth | 200, includes worst_op | HTTP 200; worst_miss_rate=50%; worst_op=rfq_parse | ✅ PASS |
| 4 | `/api/sla/operation/rfq_parse` | GET broker auth | 200, sample_count≥10 | HTTP 200; sample_count=10; miss_rate=50% | ✅ PASS |
| 5 | RFQ regression (decorator must not break parsing) | POST `/api/rfq-inbound/parse-text` | 200, parser_model=openai/gpt-4o | HTTP 200 in 3200ms; parser_model=openai/gpt-4o | ✅ PASS |
| 6 | Briefing regression | GET `/api/ops/briefing?force=true` | 200, source=llm | HTTP 200 in 12176ms; source=llm, len=1267 | ✅ PASS |
| 7 | Self-health | `/api/health/system` includes sla_monitor | present, healthy | sla_monitor: status=healthy, db_initialized=True, 6 operations | ✅ PASS |

Smoke harness `backend/.smoke_140_1_1c.py` deleted post-run. Smoke artifacts (10 sla_metrics rows tagged `context.smoke=true`) cleaned up at end of harness.

## Caveats

1. **Only 2 of 6 operations instrumented at this stage.** Remaining 4 (`load_eval`, `carrier_assign`, `dispatch_packet`, `invoice_gen`) are defined as targets but no `@track_sla` decorators applied yet. Stage 1d will instrument these as part of broader auto_dispatch / billing integration. They report `sample_count=0` until decorated.
2. **Background loop is currently disabled in stage 1b smoke env override** — production will have `SLA_LOOP_INTERVAL_SEC=300` (5min). Verify operator hasn't set conflicting env var on production restart.
3. **db.sla_metrics will accumulate** — no automatic pruning. At 100 ops/day across 6 targets, ~600 rows/day = ~18k/month. Expect to add a TTL index or pruning loop in a future hygiene iter.
4. **`@track_sla` decorator forwards to throttle.record_operation lazily** — uses module-level import inside the function to avoid circular dependencies. If throttle_system unavailable at runtime (e.g., import order issue), the timing is still recorded to db.sla_metrics; only the throttle feed is skipped.

## Verify yourself

```powershell
git log --oneline -3
# Expected:
#   f8c36b6 iter 140.1 stage 1c: SLA monitor...
#   2ab45ab iter 140.1 stage 1b: throttle state machine...
#   a646681 iter 140.1 stage 1a: health check framework...

# Live SLA summary (broker auth)
$auth = Invoke-RestMethod -Uri "http://127.0.0.1:8001/api/auth/login" -Method Post `
    -Body (@{email=$env:ADMIN_EMAIL; password=$env:ADMIN_PASSWORD} | ConvertTo-Json) `
    -ContentType "application/json" -SessionVariable sess
Invoke-RestMethod -Uri "http://127.0.0.1:8001/api/sla/summary" -WebSession $sess | ConvertTo-Json -Depth 4

# Self-health
Invoke-RestMethod -Uri "http://127.0.0.1:8001/api/health/system" |
  Select-Object -ExpandProperty modules | Select-Object -ExpandProperty sla_monitor
# Expected: status=healthy, db_initialized=True, targets_count=6
```

## Open items / next stage

Stage 1c is COMPLETE. Stage 1d is next:
- Wire throttle into `auto_dispatch.try_auto_accept` (gate on `should_accept_load()`)
- Apply remaining `@track_sla` decorators (`load_eval`, `carrier_assign`, `dispatch_packet`, `invoice_gen` where applicable)
- Build failure simulation harness at `backend/tests/throttle_failure_sim.py` (emergent scaffolding ~270 lines available)
- Document recovery patterns
- Final iter smoke + close handoff

Pre-conditions for entering 1d:
- 1c commit landed: `f8c36b6` ✓
- Smoke green: 7/7 ✓
- SLA endpoints reachable ✓
- Throttle has SLA feed ✓

Proceeding to stage 1d autonomously per Path A delegation.

Handoff: `progress/stage_completion/iter_140_1_stage_1c.md` (~7 KB)

DONE
