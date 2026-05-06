# Stage 1a: Health Check Framework

## Stage objective

Build the foundation for autonomous monitoring: a per-module health check system where every backend module exposes its operational status via a `/health` endpoint. Create a unified `health_monitor.py` module that aggregates these health checks, exposes a system-wide health summary at `/api/health/system`, and provides the data feed that the throttle state machine (stage 1b) will consume. Success = all critical modules reporting health, aggregate endpoint returns comprehensive status, smoke tests confirm health checks accurately reflect module state.

## Pre-flight checks (read-only)

```powershell
# Check 1: confirm we're on the right commit and tree is clean
cd C:\CHL
git status
git log --oneline -5
# Expected: clean working tree, recent commits include iter 139.46 close (604762a)
# On mismatch: STOP. Do not proceed with uncommitted unrelated changes in tree.

# Check 2: verify health_monitor.py does NOT exist yet
Test-Path C:\CHL\backend\health_monitor.py
# Expected: False (net-new module this stage)
# On True: STOP. File already exists, stage may be partially complete.

# Check 3: scan for existing /health endpoints
Select-String -Path C:\CHL\backend\*.py -Pattern "\/health" -Context 1,1
# Expected: zero or minimal matches (maybe old TODO comments)
# Document any existing health endpoints for integration planning.

# Check 4: identify critical modules to instrument
# From discovery docs, these modules must report health:
$criticalModules = @(
    "rfq_inbound.py",
    "load_completion_workflow.py", 
    "automations.py",
    "auto_dispatch.py",
    "boot_briefing.py",
    "failure_detectors.py",
    "bmc84_watcher.py",
    "auto_repost_scheduler.py"
)
foreach ($mod in $criticalModules) {
    if (!(Test-Path "C:\CHL\backend\$mod")) {
        Write-Host "ERROR: Critical module $mod not found"
        exit 1
    }
}
Write-Host "✓ All critical modules present"

# Check 5: verify AsyncOpenAI and AsyncAnthropic are importable
& "C:\CHL\.venv-local\Scripts\python.exe" -c "from openai import AsyncOpenAI; from anthropic import AsyncAnthropic; print('SDK imports OK')"
# Expected: "SDK imports OK"
# On error: STOP. Environment broken.

# Check 6: verify backend service is running
Get-Service CHL-Backend
# Expected: Status = Running
# On mismatch: Start-Service CHL-Backend and wait for bind.

# Check 7: verify MongoDB connection
& "C:\CHL\.venv-local\Scripts\python.exe" -c "from motor.motor_asyncio import AsyncIOMotorClient; import asyncio; asyncio.run(AsyncIOMotorClient().__getitem__('test').command('ping')); print('MongoDB OK')"
# Expected: "MongoDB OK"
# On error: STOP. Database connection required.

# Check 8: check for any existing health check patterns
Select-String -Path C:\CHL\backend\server.py -Pattern "health|status|ping" -Context 2,2
# Document existing patterns (e.g., /api/system-state exists per discovery doc)
# /api/system-state is operator-facing aggregate (30s cache); our /health endpoints are machine-facing real-time.
```

## Action steps

### Step 1: Create health_monitor.py module

Create `C:\CHL\backend\health_monitor.py` with the following structure:

```python
"""
Health monitoring framework for autonomous throttle system.

Every critical backend module registers a health check function that returns:
{
    "status": "healthy" | "degraded" | "unhealthy",
    "latency_ms": <optional: recent avg latency>,
    "error_rate": <optional: recent error %>,
    "last_success": <optional: ISO timestamp>,
    "details": <optional: dict with module-specific metrics>
}

The health_monitor aggregates all checks and exposes /api/health/system.
"""

import asyncio
import logging
import time
from typing import Callable, Dict, Any
from datetime import datetime, timezone
from fastapi import APIRouter

logger = logging.getLogger("health_monitor")

# Registry of health check functions
_health_checks: Dict[str, Callable[[], Dict[str, Any]]] = {}

def register_health_check(module_name: str, check_fn: Callable[[], Dict[str, Any]]):
    """Register a health check function for a module."""
    _health_checks[module_name] = check_fn
    logger.info(f"Registered health check for {module_name}")

async def get_system_health() -> Dict[str, Any]:
    """
    Aggregate all module health checks.
    
    Returns:
    {
        "overall_status": "healthy" | "degraded" | "unhealthy",
        "modules": {
            "rfq_inbound": {...},
            "automations": {...},
            ...
        },
        "summary": {
            "healthy_count": N,
            "degraded_count": N,
            "unhealthy_count": N
        },
        "timestamp": "ISO datetime"
    }
    """
    module_results = {}
    healthy_count = 0
    degraded_count = 0
    unhealthy_count = 0
    
    for module_name, check_fn in _health_checks.items():
        try:
            start = time.time()
            result = await asyncio.wait_for(
                asyncio.to_thread(check_fn) if not asyncio.iscoroutinefunction(check_fn) else check_fn(),
                timeout=5.0  # 5s timeout per check
            )
            elapsed_ms = (time.time() - start) * 1000
            
            # Ensure result has required fields
            if "status" not in result:
                result["status"] = "unhealthy"
                result["details"] = {"error": "Health check missing status field"}
            
            result["check_duration_ms"] = round(elapsed_ms, 2)
            module_results[module_name] = result
            
            # Count statuses
            if result["status"] == "healthy":
                healthy_count += 1
            elif result["status"] == "degraded":
                degraded_count += 1
            else:
                unhealthy_count += 1
                
        except asyncio.TimeoutError:
            module_results[module_name] = {
                "status": "unhealthy",
                "details": {"error": "Health check timed out (>5s)"}
            }
            unhealthy_count += 1
        except Exception as e:
            logger.exception(f"Health check failed for {module_name}")
            module_results[module_name] = {
                "status": "unhealthy",
                "details": {"error": str(e)}
            }
            unhealthy_count += 1
    
    # Determine overall status
    if unhealthy_count > 0:
        overall_status = "unhealthy"
    elif degraded_count > 0:
        overall_status = "degraded"
    else:
        overall_status = "healthy"
    
    return {
        "overall_status": overall_status,
        "modules": module_results,
        "summary": {
            "healthy_count": healthy_count,
            "degraded_count": degraded_count,
            "unhealthy_count": unhealthy_count,
            "total_count": len(_health_checks)
        },
        "timestamp": datetime.now(timezone.utc).isoformat()
    }

# FastAPI router
router = APIRouter()

@router.get("/api/health/system")
async def health_system_endpoint():
    """System-wide health check aggregation (machine-facing, real-time)."""
    return await get_system_health()

# Export for use by throttle system
__all__ = ["register_health_check", "get_system_health", "router"]
```

### Step 2: Add health checks to critical modules

For each critical module, add a health check function at the END of the file (before any `if __name__ == "__main__"` block):

**Pattern to add to each module:**

```python
# Health check for <MODULE_NAME> (iter 140.1 stage 1a)
def _health_check_<module_name>() -> dict:
    """
    Report health status for this module.
    
    Returns:
    {
        "status": "healthy" | "degraded" | "unhealthy",
        "details": {...}
    }
    """
    # Module-specific health logic here
    # Example for rfq_inbound.py:
    import os
    api_key = os.environ.get("OPENAI_API_KEY")
    if not api_key:
        return {
            "status": "unhealthy",
            "details": {"error": "OPENAI_API_KEY not configured"}
        }
    
    # Could add: recent parse success rate, avg latency, etc.
    # For now, just check if dependencies are available
    return {
        "status": "healthy",
        "details": {"openai_key_present": True}
    }

# Register with health monitor
try:
    from health_monitor import register_health_check
    register_health_check("<module_name>", _health_check_<module_name>)
except ImportError:
    pass  # health_monitor not available yet (during initial setup)
```

**Modules to instrument (minimum 8):**
1. `rfq_inbound.py` - check OPENAI_API_KEY present
2. `load_completion_workflow.py` - check OPENAI_API_KEY present
3. `automations.py` - check OPENAI_API_KEY + Twilio creds
4. `auto_dispatch.py` - check auto_accept_master_enabled flag
5. `boot_briefing.py` - check ANTHROPIC_API_KEY present
6. `failure_detectors.py` - check if loops are running (placeholder for now)
7. `bmc84_watcher.py` - check FMCSA_WEBKEY present (optional)
8. `auto_repost_scheduler.py` - check business_settings accessible

### Step 3: Register health_monitor router in server.py

Add to `server.py` (near other router includes):

```python
# Health monitoring framework (iter 140.1 stage 1a)
from health_monitor import router as health_router
app.include_router(health_router)
```

Constraint: `server.py` is monolith-protected - this is a minimal import+registration, explicitly authorized.

### Step 4: Verify all health checks registered

After restart, create a smoke test script to verify:

```powershell
# Smoke: GET /api/health/system should return all 8 modules
$response = Invoke-RestMethod -Uri "http://localhost:8000/api/health/system" -Method Get
$response | ConvertTo-Json -Depth 5
# Expected: overall_status = "healthy", modules count = 8, all statuses = "healthy"
```

## Restart and smoke

```powershell
# Restart backend to load new code
Stop-Service CHL-Backend
Start-Service CHL-Backend
Get-Service CHL-Backend
# Wait for FastAPI to bind: ~3-5 seconds

# Confirm boot didn't crash
Select-String -Path C:\CHL\logs\nssm\backend.err.log -Pattern "ERROR|Traceback" -Context 0,3 | Select-Object -Last 5
# Expected: nothing recent post-restart
```

### Smoke battery

**Test 1: Health system endpoint exists**
```powershell
Invoke-RestMethod -Uri "http://localhost:8000/api/health/system" -Method Get
```
Expected:
- HTTP 200
- JSON response with keys: `overall_status`, `modules`, `summary`, `timestamp`
- `summary.total_count` >= 8 (all critical modules registered)

**Test 2: All modules report healthy (baseline)**
```powershell
$health = Invoke-RestMethod -Uri "http://localhost:8000/api/health/system" -Method Get
if ($health.overall_status -ne "healthy") {
    Write-Host "ERROR: Overall status not healthy: $($health.overall_status)"
    $health.modules | ConvertTo-Json -Depth 3
    exit 1
}
Write-Host "✓ Overall status: healthy"
Write-Host "✓ Healthy modules: $($health.summary.healthy_count)/$($health.summary.total_count)"
```
Expected: `overall_status = "healthy"`, all modules `status = "healthy"`

**Test 3: Simulate unhealthy module (remove OPENAI_API_KEY temporarily)**
```powershell
# Temporarily rename .env key to simulate failure
$envPath = "C:\CHL\backend\.env"
$content = Get-Content $envPath -Raw
$content = $content -replace "OPENAI_API_KEY=", "OPENAI_API_KEY_DISABLED="
Set-Content $envPath $content

Stop-Service CHL-Backend
Start-Service CHL-Backend
Start-Sleep -Seconds 5

$health = Invoke-RestMethod -Uri "http://localhost:8000/api/health/system" -Method Get
if ($health.overall_status -ne "unhealthy") {
    Write-Host "ERROR: Expected unhealthy status when OPENAI_API_KEY missing"
    exit 1
}
Write-Host "✓ System correctly detected unhealthy state"

# Restore .env
$content = $content -replace "OPENAI_API_KEY_DISABLED=", "OPENAI_API_KEY="
Set-Content $envPath $content
Stop-Service CHL-Backend
Start-Service CHL-Backend
Start-Sleep -Seconds 5
Write-Host "✓ .env restored, service restarted"
```

**Test 4: Health check performance (should be fast)**
```powershell
$stopwatch = [System.Diagnostics.Stopwatch]::StartNew()
$health = Invoke-RestMethod -Uri "http://localhost:8000/api/health/system" -Method Get
$stopwatch.Stop()
$elapsed = $stopwatch.ElapsedMilliseconds
if ($elapsed -gt 1000) {
    Write-Host "WARNING: Health check took ${elapsed}ms (>1s threshold)"
}
Write-Host "✓ Health check completed in ${elapsed}ms"
```
Expected: <1000ms total (all 8 checks + aggregation)

**Test 5: Adjacent regression - boot_briefing**
```powershell
Invoke-RestMethod -Uri "http://localhost:8000/api/ops/briefing?force=true" -Method Get
```
Expected: HTTP 200, `source: llm`, content length > 100 chars

**Test 6: Git diff is surgical**
```powershell
git diff
```
Expected:
- NEW FILE: `backend/health_monitor.py`
- MODIFIED: 8 module files (each with ~15 line health check addition)
- MODIFIED: `server.py` (2 lines: import + router registration)
- No other changes

## Commit protocol

```powershell
cd C:\CHL
git status
# Expect: health_monitor.py (new), 8 module files (modified), server.py (modified)

git add backend/health_monitor.py
git add backend/rfq_inbound.py backend/load_completion_workflow.py backend/automations.py backend/auto_dispatch.py backend/boot_briefing.py backend/failure_detectors.py backend/bmc84_watcher.py backend/auto_repost_scheduler.py
git add backend/server.py
git commit -m "iter 140.1 stage 1a: health check framework - 8 modules instrumented, /api/health/system endpoint live"
git push origin main
```

If `git status` shows unexpected modifications, STOP.

## Completion doc specification

Write `progress/stage_completion/iter_140_1_stage_1a.md` (in chl-memory clone) per completion template. Include:

- Per-action-step summary:
  - Step 1: health_monitor.py created (~150 lines)
  - Step 2: 8 modules instrumented (list which ones, line counts)
  - Step 3: server.py router registration (lines modified)
  - Step 4: Verification results
- Smoke results (6 tests) - include actual module counts, response times
- Post-action state:
  - `Invoke-RestMethod /api/health/system | ConvertTo-Json` output
  - Module health statuses
- Git diff summary: 1 new file, 9 modified files
- Verify-yourself block: commit SHA, endpoint curl command
- Caveats:
  - Health checks are currently basic (key-presence only)
  - Future enhancement: track recent error rates, latencies per module
  - No frontend dashboard yet (API-only)
- Handoff to stage 1b: throttle state machine will consume this health data

## STOP CONDITIONS specific to this stage

- Pre-flight check 2 shows health_monitor.py already exists (partial prior work)
- Pre-flight check 4 shows critical module missing
- Pre-flight check 5 shows SDK import failures
- Pre-flight check 7 shows MongoDB unreachable
- After step 1, health_monitor.py fails to import (syntax errors)
- After step 2, any module health check crashes on execution
- After step 3, /api/health/system returns 404 (router not registered)
- After step 3, smoke test 1 fails (endpoint doesn't exist)
- After step 3, smoke test 2 shows modules not registered (<8 count)
- After step 3, smoke test 3 fails to detect simulated failure (health checks not working)
- After step 3, smoke test 5 fails (adjacent regression)
- Git diff shows changes beyond expected files

## On completion

1. Update `progress/current_status.md`: mark stage 1a complete, active stage → 1b
2. Stage 1b is next: throttle state machine will consume health data from `/api/health/system`
3. Do NOT proceed to stage 1b without operator review

Handoff: progress/stage_completion/iter_140_1_stage_1a.md

DONE
