# Stage 1d: Integration + Failure Simulation

## Stage objective

End-to-end integration testing and failure simulation. Verify throttle system responds correctly to real-world failure scenarios. Simulate: API outages, database slowdowns, elevated error rates, module crashes. Confirm system transitions states, reduces intake, sends alerts, and auto-recovers. Document recovery patterns for operator playbook. Success = 5+ failure scenarios handled gracefully, throttle proven to prevent operator overload, completion doc provides operator guidance.

## Pre-flight checks

```powershell
# Check 1: all prior stages complete
$health = Invoke-RestMethod "http://localhost:8000/api/health/system"
$throttle = Invoke-RestMethod "http://localhost:8000/api/throttle/status"  
$sla = Invoke-RestMethod "http://localhost:8000/api/sla/summary"

if ($health.overall_status -ne "healthy") { exit 1 }
if ($throttle.current_state -ne "GREEN") { exit 1 }
# Verify SLA endpoint exists and returns data

# Check 2: create failure simulation harness
# Verify we can safely inject failures without breaking production
```

## Action steps

### Step 1: Create failure simulation harness

`tests/throttle_failure_sim.py`:
- Scenario 1: OpenAI API timeout (mock slow responses)
- Scenario 2: Database connection drop
- Scenario 3: Elevated 422 error rate (bad RFQ inputs)
- Scenario 4: Module crash (failure_detector loop fails)
- Scenario 5: Sustained high latency (p95 >10s)

### Step 2: Run all failure scenarios

For each scenario:
1. Inject failure
2. Observe throttle state transition (GREEN→YELLOW→ORANGE→RED as appropriate)
3. Verify intake reduces (check auto_dispatch rejection rate)
4. Verify alerts sent (SMS on RED, email on YELLOW)
5. Clear failure
6. Observe auto-recovery (wait 15+ min, verify step-up to GREEN)

### Step 3: Document recovery patterns

Write `C:\CHL\memory\THROTTLE_PLAYBOOK.md`:
- What each throttle state means for operator
- Expected SMS/email alerts
- How to manually override throttle (emergency bypass)
- How to diagnose which module triggered degradation
- Recovery SOP (wait for auto-recovery vs manual intervention)

### Step 4: Wire into failure_detectors

Modify existing failure detectors to feed throttle:
- `failure_detectors.py` → report elevated failure count to throttle
- `circuit_breaker.py` → when tripped, trigger throttle RED
- `bmc84_watcher.py` → authority loss → throttle ORANGE

## Smoke tests

1. All 5 failure scenarios pass (state transitions correct)
2. Intake throttling verified (auto_dispatch logs show rejections)
3. Alerts delivered (SMS/email received)
4. Auto-recovery proven (GREEN after 15min stability)
5. Manual override works (operator can force GREEN if needed)
6. Adjacent regression: all prior features still work

## Commit

```powershell
git add backend/throttle_system.py backend/failure_detectors.py backend/circuit_breaker.py tests/throttle_failure_sim.py memory/THROTTLE_PLAYBOOK.md
git commit -m "iter 140.1 stage 1d: throttle integration + failure simulation - 5 scenarios proven, auto-recovery confirmed"
git push origin main
```

## Completion doc

Document:
- All 5 failure scenarios (setup, expected behavior, actual results)
- Alert delivery logs (SMS screenshots, email samples)
- Recovery timing (how long GREEN→YELLOW→ORANGE→RED, how long back to GREEN)
- Operator guidance (when to intervene, when to trust auto-recovery)
- Performance impact (throttle overhead on auto_dispatch latency)

## On completion

Iter 140.1 complete. Throttle system proven. Phase 7 foundation established.

Next iter candidates:
- 141.1: Phase 2 (load board integration) - NOW SAFE to build autonomous intake
- 140.2: Phase 1 cleanup batch (Stripe, comments, R2) - deferred hygiene

Operator decides. Recommend 141.1 (autonomous intake on safe foundation).

DONE
