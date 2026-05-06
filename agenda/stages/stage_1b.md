# Stage 1b: Throttle State Machine

## Stage objective

Implement the autonomous throttle system - a GREEN/YELLOW/ORANGE/RED state machine that monitors system health and automatically adjusts load intake speed. Consumes health data from stage 1a's `/api/health/system` endpoint. When problems detected (elevated error rates, API latency spikes, unhealthy modules), system transitions to degraded states and reduces load acceptance. Auto-recovers when metrics stabilize for 15+ minutes. Success = state machine operational, transitions trigger correctly on simulated failures, intake throttling prevents operator overload.

## Pre-flight checks

```powershell
# Check 1: stage 1a complete, health system working
$health = Invoke-RestMethod -Uri "http://localhost:8000/api/health/system" -Method Get
if ($health.overall_status -ne "healthy") {
    Write-Host "ERROR: Health system not healthy - stage 1a incomplete"
    exit 1
}
Write-Host "✓ Health system operational"

# Check 2: verify throttle_system.py does NOT exist yet
Test-Path C:\CHL\backend\throttle_system.py
# Expected: False

# Check 3: identify load acceptance entry points to throttle
# From discovery: auto_dispatch.try_auto_accept, auto_load_pipeline
Select-String -Path C:\CHL\backend\auto_dispatch.py -Pattern "def try_auto_accept"
Select-String -Path C:\CHL\backend\auto_load_pipeline.py -Pattern "def.*accept|def.*book"
# Document locations for integration

# Check 4: verify Twilio SMS credentials for alerts
$env:TWILIO_ACCOUNT_SID -and $env:TWILIO_AUTH_TOKEN
# Expected: True (SMS alerts on RED state)
```

## Action steps

### Step 1: Create throttle_system.py module

Key components:
- `ThrottleState` enum (GREEN, YELLOW, ORANGE, RED)
- `ThrottleManager` class with state transitions
- Metrics tracking (error rates, latencies, health check results)
- Auto-transition logic (degrade on problems, recover on stability)
- SMS/email alerts on state changes
- Intake percentage per state (GREEN=100%, YELLOW=50%, ORANGE=10%, RED=0%)

### Step 2: Wire throttle into auto_dispatch

Modify `auto_dispatch.py:try_auto_accept()`:
- Check throttle state before hard gates
- If RED: auto-reject with reason "system in RED state"
- If ORANGE/YELLOW: apply random sampling to reduce intake percentage
- Log throttle-based rejections to `auto_accept_log`

### Step 3: Add throttle state endpoint

Create `/api/throttle/status` endpoint returning:
```json
{
  "current_state": "GREEN",
  "intake_percentage": 100,
  "state_since": "ISO timestamp",
  "recent_transitions": [...],
  "metrics": {
    "error_rate": 0.02,
    "avg_latency_ms": 450,
    "unhealthy_module_count": 0
  }
}
```

### Step 4: Background monitoring loop

Start async task that:
- Polls `/api/health/system` every 60s
- Tracks error rates from recent failures
- Triggers state transitions when thresholds crossed
- Sends SMS alert on ORANGE→RED, email on YELLOW

## Restart and smoke

Smoke tests:
1. Normal operation: throttle stays GREEN
2. Simulate high error rate: throttle transitions YELLOW→ORANGE
3. Simulate API key missing: throttle transitions RED
4. Recovery: restore health, verify throttle steps back up GREEN
5. Intake throttling: verify auto_dispatch respects state percentages
6. Adjacent regression: boot_briefing still works

## Commit protocol

```powershell
git add backend/throttle_system.py backend/auto_dispatch.py backend/server.py
git commit -m "iter 140.1 stage 1b: throttle state machine - GREEN/YELLOW/ORANGE/RED with auto-transitions"
git push origin main
```

## Completion doc

Document: state transition logic, intake percentages, SMS alert triggers, recovery timing.

DONE
