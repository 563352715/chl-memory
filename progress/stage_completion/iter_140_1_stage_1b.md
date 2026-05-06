# Stage Completion — Iter 140.1 Stage 1b

**Stage:** 1b — Throttle state machine
**Status:** ✅ COMPLETE
**Commit:** [`2ab45ab`](https://github.com/563352715/Continental-Haul-Logistics/commit/2ab45ab) on CHL `main`
**Completed:** 2026-05-06

## Outcome

Built the GREEN/YELLOW/ORANGE/RED throttle state machine that consumes stage 1a's `/api/health/system` data feed and gates load intake based on system health. Adopted emergent scaffolding (`throttle_models.py` 245 lines as-is, `throttle_system.py` ~527 lines refined to ~440 lines). Direction-aware state transitions: snap-to-worse, gradual one-step recovery after `RECOVERY_WINDOW_SEC` (default 15min). Persistence via `db.throttle_state_log`. Smoke 7/7 PASS. Stage 1c SLA monitor will hook into `record_operation` + `_get_sla` callable for SLA-driven transitions.

## Files

| File | Type | Lines | Source |
|---|---|---|---|
| `backend/throttle_models.py` | NEW | 245 | adopted as-is from emergent scaffolding |
| `backend/throttle_system.py` | NEW | ~440 | adapted from emergent scaffolding (~527 lines) |
| `backend/server.py` | MODIFIED | +4 lines | per-stage authorized monolith edit (import + attach_to_app call) |

## Per-action-step summary

### Step 1: throttle_models.py

Adopted emergent scaffolding as-is. Pydantic v2 models:
- `ThrottleState` enum (GREEN, YELLOW, ORANGE, RED)
- `STATE_INTAKE_PCT` const dict (100, 50, 10, 0)
- `state_severity()` helper for direction comparison
- `HealthStatus`, `HealthCheck`, `HealthSummary`, `SystemHealth` (cross-stage health typing)
- `ThrottleMetrics`, `ThrottleTransition`, `ThrottleStatus`, `ThrottleOverrideRequest`
- `SLATarget`, `SLAMetric`, `SLASummary`, `SLAReport` (stage 1c will use)
- `FailureScenarioConfig` (stage 1d will use)

### Step 2: throttle_system.py

Adapted from emergent scaffolding with these refinements:

| Change | Why |
|---|---|
| `_normalize_health()` adapter accepts dict OR `SystemHealth` | `health_monitor.get_system_health()` returns dict; throttle's call sites can pass it directly without conversion |
| Removed hardcoded `ALERT_EMAIL_TO`/`SMS_TO` defaults | env-only; if unset, alerts skip silently. No accidental SMS to a stale operator number |
| `CRITICAL_MODULES` empty by default (env-overridable) | scaffolding listed `database`/`auth` but we don't have those registered yet; future stages can add |
| `attach_to_app(app, ...)` helper | encapsulates `init_manager` + `@on_event("startup")` registration + `include_router` so server.py stays minimal (single function call, no new top-level @on_event handler in the monolith) |
| Added `_health_check_throttle_system` self-registration | throttle reports its own health back to the framework — closes the meta-loop |

### Step 3: server.py wiring (4 lines added)

```python
# Throttle system (iter 140.1 stage 1b) — autonomous intake state machine
from throttle_system import attach_to_app as _attach_throttle
_attach_throttle(fastapi_app, db=db, get_health=_get_health_dict, require_broker=require_broker)
```

(Plus the existing health_monitor line was upgraded to also import `get_system_health as _get_health_dict`.)

## State machine logic

### Transition triggers

```
GREEN→YELLOW   error_rate ≥ 5%   OR  unhealthy_count > 0  OR  p95 ≥ 5s   OR  sla_max ≥ 20%
YELLOW→ORANGE  error_rate ≥ 10%  OR  unhealthy_count > 2  OR  p95 ≥ 10s
ORANGE→RED    error_rate ≥ 20%  OR  unhealthy_count > 4  OR  any CRITICAL_MODULE unhealthy
```

### Direction rules

- **Worsening** (sev_target > sev_current): snap to target immediately, alert if applicable
- **Recovering** (sev_target < sev_current): require `RECOVERY_WINDOW_SEC` of stable better metrics, then step ONE level toward GREEN. Never skip levels.
- **No change**: clear any active recovery window

This means: a healthy system that suddenly goes very wrong jumps RED in one tick (good — fast protection). A degraded system that recovers walks back to GREEN one step at a time over 15+min (good — operator sees the climb, no flapping).

### Tunables (env)

- `THROTTLE_POLL_INTERVAL_SEC` (default 60)
- `THROTTLE_RECOVERY_WINDOW_SEC` (default 900 = 15min)
- `THROTTLE_ERROR_WINDOW_SEC` (default 3600 = 1h rolling)
- `THROTTLE_YELLOW_ERR/ORANGE_ERR/RED_ERR` (default 0.05/0.10/0.20)
- `THROTTLE_YELLOW_P95_MS/ORANGE_P95_MS` (default 5000/10000)
- `THROTTLE_CRITICAL_MODULES` (default empty, comma-separated)
- `THROTTLE_ALERT_EMAIL`, `THROTTLE_ALERT_SMS` (default empty → alerts skip)
- `THROTTLE_OP_BUFFER_MAXLEN` (default 5000)

## Pre-flight (per stage_1b.md spec)

| # | Check | Expected | Actual |
|---|---|---|---|
| 1 | `/api/health/system` healthy | overall=healthy | overall=`degraded` (Twilio + FMCSA optional creds missing — accurate baseline, **not** a stop condition since unhealthy_count=0 maps to GREEN) |
| 2 | throttle_system.py NOT exists | False | ✓ |
| 3 | `auto_dispatch.try_auto_accept` location | found | (deferred to stage 1d for actual integration) |
| 4 | Twilio creds for SMS alerts | True | False — alerts skip silently per env-only design (no accidental SMS) |

## Smoke results

| # | Test | Method | Expected | Actual | Status |
|---|---|---|---|---|---|
| 1 | Initial state | `evaluate()` | GREEN with current metrics | state=GREEN, intake=100%, unhealthy=0, degraded=0 (note: in-process new manager, fresh metrics) | ✅ PASS |
| 2 | 100% error rate snap to RED | 20 failed ops + evaluate | RED, intake=0% | state=RED, intake=0%, err_rate=100%, total_ops=20 | ✅ PASS |
| 3 | DB persistence | query `db.throttle_state_log` | ≥1 transition row | 3 transitions logged (GREEN→RED ×3) | ✅ PASS |
| 4 | Recovery | clear errors + wait `RECOVERY_WINDOW_SEC=2` + evaluate | step-down toward GREEN | RED → ORANGE (one step, correct per design) | ✅ PASS |
| 5 | Manual override | `transition_to(GREEN, ...)` direct call | GREEN | state=GREEN | ✅ PASS |
| 6 | Briefing regression | `GET /api/ops/briefing?force=true` | 200 source=llm | HTTP 200 in 13866ms; source=`llm`, len=1406 | ✅ PASS |
| 7 | Self-health | `/api/health/system` includes `throttle_system` | present | `throttle_system: {status: healthy, manager_initialized: True, current_state: GREEN, intake_pct: 100}` | ✅ PASS |

Smoke harness `backend/.smoke_140_1_1b.py` deleted post-run.

## Notable design decisions

1. **dict vs SystemHealth at the boundary.** `health_monitor.get_system_health()` returns a `Dict[str, Any]` (kept simple — JSON-serializable, no Pydantic dependency in health_monitor). `throttle_system._normalize_health()` accepts either dict or model and converts. Single boundary point; rest of throttle code is type-safe.

2. **`attach_to_app` pattern instead of explicit `@on_event` in server.py.** Per `agenda/protocol.md` monolith rules, no new top-level functions in server.py. By having `attach_to_app` register the startup event internally, server.py only gets a single function call. Same outcome, cleaner monolith profile.

3. **In-process manager for smoke vs server-side manager.** The backend service runs as a separate NSSM process — its module-level `_manager` is initialized via `attach_to_app` at startup. The smoke harness creates its own in-process manager (calling `init_manager` directly) to test transitions without affecting the live server's state. Both work correctly; smoke validates the implementation; live server validates the wiring (test 7 confirms via `/api/health/system`).

4. **Empty `CRITICAL_MODULES` by default.** Scaffolding had `["database", "auth"]` hardcoded. Neither is registered as a health check yet. Stage 1d will register them (or define a `database` module via a quick check in `health_monitor`'s init). For now, RED only triggers on error/count thresholds, not critical-module-down. Configurable via env.

5. **Auto-recovery is gradual, not instant.** Even with smoke's 2-second recovery window (vs default 15min), each `evaluate()` cycle steps ONE level. The smoke proved this: clean ops + wait → RED stepped to ORANGE (not direct to GREEN). To fully recover from RED would take 3 evaluate cycles (RED→ORANGE→YELLOW→GREEN), each separated by a recovery window. This is by design — operator sees the climb, no flapping.

## Caveats

1. **Auto_dispatch integration deferred to stage 1d** — the throttle exposes `should_accept_load()` and `get_intake_percentage()` but no module yet calls them. Stage 1d wires `auto_dispatch.try_auto_accept` to gate on throttle state.
2. **No SMS/email alerts emit during smoke** — alert recipients (`THROTTLE_ALERT_EMAIL`, `THROTTLE_ALERT_SMS`) are env-only and unset by default. Operator can populate to enable. Code path tested implicitly via the worsening-transition logic (just no actual messages sent).
3. **`SLA_max` always 0.0 until stage 1c** — `_get_sla` callable is None; sla_max contributes 0 to YELLOW evaluation. Not a regression — just future surface area.
4. **Self-test run-time event: 3 transitions visible in `db.throttle_state_log`** — that's smoke artifacts. Production runs should not see GREEN→RED unless actual failures occur. Operator may want to clear the smoke rows: `db.throttle_state_log.deleteMany({reason: /^smoke/})` if desired.
5. **Recovery window for production should remain 15min.** Smoke uses 2s via env override. Don't lower in production — it'd cause flapping.

## Verify yourself

```powershell
git log --oneline -2
# Expected:
#   2ab45ab iter 140.1 stage 1b: throttle state machine...
#   a646681 iter 140.1 stage 1a: health check framework...

# Live throttle status (broker auth required)
$auth = (Invoke-RestMethod -Uri "http://127.0.0.1:8001/api/auth/login" -Method Post -Body (@{email=$env:ADMIN_EMAIL; password=$env:ADMIN_PASSWORD} | ConvertTo-Json) -ContentType "application/json" -SessionVariable sess)
Invoke-RestMethod -Uri "http://127.0.0.1:8001/api/throttle/status" -WebSession $sess | ConvertTo-Json -Depth 5
# Expected: current_state=GREEN, intake_percentage=100, metrics object, recent_transitions array

# Self-health
Invoke-RestMethod -Uri "http://127.0.0.1:8001/api/health/system" |
  Select-Object -ExpandProperty modules | Select-Object -ExpandProperty throttle_system
# Expected: status=healthy, current_state=GREEN, intake_pct=100
```

## Open items / next stage

Stage 1b is COMPLETE. Stage 1c is next:
- New module: `backend/sla_monitor.py` (emergent scaffolding ~322 lines available at `C:\CHL\staging\iter_140_1_emergent\iter_140_1_scaffolding\sla_monitor.py`)
- Define SLA targets (RFQ parse <4s, load eval <60s, etc.)
- `@track_sla(operation, target_ms)` decorator for measuring durations
- `db.sla_metrics` collection writes
- Background loop computes per-operation miss_rate, p50/p95/p99
- Hook into throttle: pass `get_sla` callable to `init_manager` so `metrics.sla_miss_rate_max` is populated

Pre-conditions for entering 1c:
- 1b commit landed: `2ab45ab` ✓
- Smoke green: 7/7 ✓
- Throttle endpoints reachable ✓
- DB persistence proven ✓

Proceeding to stage 1c autonomously per Path A delegation.

Handoff: `progress/stage_completion/iter_140_1_stage_1b.md` (~9 KB)

DONE
