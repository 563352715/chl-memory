# Stage 1c: SLA Monitoring

## Stage objective

Build performance target tracking system that feeds into throttle state machine. Monitor critical operations against playbook SLA targets (RFQ parse <4s, load eval <60s, dispatch <10min, etc.). Alert when targets missed repeatedly. Elevated SLA miss rates trigger throttle degradation (GREEN→YELLOW). Success = SLA metrics tracked, alerts fire on violations, throttle responds to sustained SLA misses.

## Pre-flight checks

```powershell
# Check 1: stages 1a+1b complete
$health = Invoke-RestMethod "http://localhost:8000/api/health/system"
$throttle = Invoke-RestMethod "http://localhost:8000/api/throttle/status"
if ($health.overall_status -ne "healthy" -or $throttle.current_state -ne "GREEN") {
    Write-Host "ERROR: Prerequisites not met"
    exit 1
}

# Check 2: verify sla_monitor.py does NOT exist
Test-Path C:\CHL\backend\sla_monitor.py
# Expected: False
```

## Action steps

### Step 1: Create sla_monitor.py module

Track SLAs from playbook:
- RFQ parse: <4s (gpt-4o vision call in rfq_inbound.py)
- Load eval: <60s (quote draft generation)
- Carrier assignment: <5min (auto_dispatch)
- Dispatch packet: <10min (rate-con generation)
- POD OCR: <10s (gpt-4o vision in load_completion_workflow)

### Step 2: Instrument critical operations

Add timing decorators to:
- `rfq_inbound.py:_parse_with_ai` (track duration)
- `auto_quote_draft.py:_suggest_rate` (track duration)
- `auto_dispatch.py:try_auto_accept` (track duration)
- `automations.py:rate-con generation` (track duration)
- `load_completion_workflow.py:run_pod_ocr_via_gemini` (track duration)

Write timings to `db.sla_metrics` collection.

### Step 3: SLA violation detection

Background loop:
- Every 5 min, query `sla_metrics` for last 1 hour
- Calculate SLA miss rates per operation
- If RFQ parse miss rate >20%: trigger throttle YELLOW
- If multiple operations missing SLAs: trigger throttle ORANGE

### Step 4: SLA dashboard endpoint

`/api/sla/summary` returns:
```json
{
  "rfq_parse": {
    "target_ms": 4000,
    "p50_ms": 2100,
    "p95_ms": 3800,
    "p99_ms": 4500,
    "miss_rate_1h": 0.02
  },
  ...
}
```

## Smoke tests

1. Normal operation: all SLAs green
2. Simulate slow API: p95 exceeds target, miss rate climbs
3. Throttle integration: sustained misses trigger YELLOW state
4. Recovery: fix simulation, miss rate drops, throttle recovers

DONE
