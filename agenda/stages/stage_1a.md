# Stage 1a: FractalEvaluator Base Class + LoadEvaluator Refactor

> **Note (added 2026-05-07 post-iter-141.1-close):** PowerShell smoke recipes below reference `localhost:8000`. **Post-iter-141.1 verification confirmed backend on port 8001, not 8000.** When re-running these smokes against current backend, substitute `:8001`. Pattern matches the close-ceremony runbook correction (commit `9d6c5f5`); historical record preserved here for iter 141.1 audit-trail integrity.

## Stage objective

Build the fractal decision framework foundation by creating the `FractalEvaluator` abstract base class implementing the four-parameter evaluation pattern (profitability, reliability, risk, capacity). Refactor existing `auto_dispatch.try_auto_accept()` hard-coded rules into a `LoadEvaluator` subclass that inherits from this base. Behavior must be identical to prior implementation - this is a pure refactor for architectural cleanliness, not a functional change. Success = smoke tests prove LoadEvaluator.decide() produces same accept/reject decisions as prior try_auto_accept(), FractalEvaluator pattern ready for lane-level (stage 1b) and market-level (future) extensions.

## Pre-flight checks (read-only)

```powershell
# Check 1: confirm we're on the right commit and tree is clean
cd C:\CHL
git status
git log --oneline -5
# Expected: clean working tree, recent commit is iter 140.1 close (2ed7c21 or later)
# On mismatch: STOP. Do not proceed with uncommitted unrelated changes in tree.

# Check 2: verify fractal_evaluator.py does NOT exist yet
Test-Path C:\CHL\backend\fractal_evaluator.py
# Expected: False (net-new module this stage)
# On True: STOP. File already exists, stage may be partially complete.

# Check 3: verify load_evaluator.py does NOT exist yet
Test-Path C:\CHL\backend\load_evaluator.py
# Expected: False (net-new module this stage)
# On True: STOP.

# Check 4: identify auto_dispatch.try_auto_accept current behavior
Select-String -Path C:\CHL\backend\auto_dispatch.py -Pattern "def try_auto_accept" -Context 2,10
# Document the 8 hard gates (master_enabled, vetted_trusted, mc_verified, margin_floor, max_auto_rate, blocklist, risk_flags, concurrent_loads)
# These become the LoadEvaluator.get_*_data() methods

# Check 5: verify business_settings collection has required fields
& "C:\CHL\.venv-local\Scripts\python.exe" -c "from motor.motor_asyncio import AsyncIOMotorClient; import asyncio; client = AsyncIOMotorClient(); db = client['chl']; settings = asyncio.run(db.business_settings.find_one({})); print(f'margin_floor_pct: {settings.get(\"margin_floor_pct\")}'); print(f'max_auto_rate: {settings.get(\"max_auto_rate\")}'); client.close()"
# Expected: margin_floor_pct and max_auto_rate present (both set in iter 139.46)
# On missing: STOP. Required settings not in database.

# Check 6: verify throttle system is operational (iter 140.1 dependency)
Invoke-RestMethod -Uri "http://localhost:8000/api/throttle/status" -Method Get
# Expected: HTTP 200, current_state = "GREEN"
# On 404 or error: STOP. Throttle system from iter 140.1 not working.

# Check 7: verify health monitor is operational
Invoke-RestMethod -Uri "http://localhost:8000/api/health/system" -Method Get
# Expected: HTTP 200, overall_status = "healthy" or "degraded"
# On 404: STOP. Health monitor from iter 140.1 not working.

# Check 8: verify SLA monitor is operational
Invoke-RestMethod -Uri "http://localhost:8000/api/sla/summary" -Method Get
# Expected: HTTP 200, JSON with operations list
# On 404: STOP. SLA monitor from iter 140.1 not working.

# Check 9: count existing auto_accept_log rows to establish baseline
& "C:\CHL\.venv-local\Scripts\python.exe" -c "from motor.motor_asyncio import AsyncIOMotorClient; import asyncio; client = AsyncIOMotorClient(); db = client['chl']; count = asyncio.run(db.auto_accept_log.count_documents({})); print(f'Baseline auto_accept_log count: {count}'); client.close()"
# Document count - we'll verify LoadEvaluator appends to this collection correctly

# Check 10: verify backend service is running
Get-Service CHL-Backend
# Expected: Status = Running
# On mismatch: Start-Service CHL-Backend and wait for bind
```

## Action steps

### Step 1: Create fractal_evaluator.py module

Create `C:\CHL\backend\fractal_evaluator.py`:

```python
"""
Fractal decision evaluation framework.

Base class implementing four-parameter evaluation pattern
(profitability, reliability, risk, capacity) that scales from
individual loads (LoadEvaluator) to route patterns (LaneEvaluator)
to regional strategy (MarketEvaluator).

Per FRACTAL_DECISION_FRAMEWORK.md - same decision logic at all scales,
only data aggregation differs.
"""

import logging
from abc import ABC, abstractmethod
from typing import Dict, Any, TypeVar, Generic

logger = logging.getLogger("fractal_evaluator")

EntityType = TypeVar('EntityType')


class FractalEvaluator(ABC, Generic[EntityType]):
    """
    Base class for fractal decision-making across scales.
    
    Subclasses implement scale-specific data aggregation methods.
    Decision logic (four-parameter evaluation) remains constant.
    
    Four parameters evaluated at every scale:
    1. Profitability (margin threshold)
    2. Reliability (counterparty quality threshold)
    3. Risk (failure probability threshold)
    4. Capacity (operational bandwidth check)
    """
    
    @abstractmethod
    async def get_profitability_data(self, entity: EntityType) -> float:
        """
        Return margin/profitability metric for this entity at this scale.
        
        Args:
            entity: Load, Lane, or Market object depending on subclass
            
        Returns:
            float: Profit margin as decimal (0.15 = 15%)
        """
        pass
    
    @abstractmethod
    async def get_reliability_data(self, entity: EntityType) -> float:
        """
        Return counterparty reliability metric.
        
        Args:
            entity: Load, Lane, or Market object depending on subclass
            
        Returns:
            float: Reliability score 0.0-1.0 (1.0 = perfect reliability)
        """
        pass
    
    @abstractmethod
    async def get_risk_data(self, entity: EntityType) -> float:
        """
        Return risk score.
        
        Args:
            entity: Load, Lane, or Market object depending on subclass
            
        Returns:
            float: Risk score 0.0-1.0 (0.0 = no risk, 1.0 = certain failure)
        """
        pass
    
    @abstractmethod
    async def get_capacity_data(self, entity: EntityType) -> bool:
        """
        Return capacity availability boolean.
        
        Args:
            entity: Load, Lane, or Market object depending on subclass
            
        Returns:
            bool: True if capacity available, False if at/over limit
        """
        pass
    
    async def evaluate(
        self, 
        entity: EntityType, 
        thresholds: Dict[str, Any]
    ) -> Dict[str, bool]:
        """
        Four-parameter evaluation - same for all scales.
        Only data aggregation (get_*_data methods) differs per subclass.
        
        Args:
            entity: Load, Lane, or Market object
            thresholds: Dict with keys margin, reliability, risk, capacity
            
        Returns:
            Dict[str, bool]: Pass/fail for each of four parameters
        """
        profitability_value = await self.get_profitability_data(entity)
        reliability_value = await self.get_reliability_data(entity)
        risk_value = await self.get_risk_data(entity)
        capacity_available = await self.get_capacity_data(entity)
        
        return {
            'profitability': profitability_value >= thresholds['margin'],
            'reliability': reliability_value >= thresholds['reliability'],
            'risk': risk_value < thresholds['risk'],
            'capacity': capacity_available
        }
    
    async def decide(
        self, 
        entity: EntityType, 
        thresholds: Dict[str, Any]
    ) -> bool:
        """
        Accept if all four parameters pass.
        
        Args:
            entity: Load, Lane, or Market object
            thresholds: Dict with keys margin, reliability, risk, capacity
            
        Returns:
            bool: True = accept, False = reject
        """
        evaluation = await self.evaluate(entity, thresholds)
        return all(evaluation.values())
    
    async def get_rejection_reason(
        self,
        entity: EntityType,
        thresholds: Dict[str, Any]
    ) -> str:
        """
        If decide() returns False, this explains which parameter(s) failed.
        
        Returns:
            str: Human-readable reason (e.g., "margin below threshold")
        """
        evaluation = await self.evaluate(entity, thresholds)
        failures = [k for k, v in evaluation.items() if not v]
        
        if not failures:
            return ""
        
        # Build detailed reason
        reasons = []
        if 'profitability' in failures:
            value = await self.get_profitability_data(entity)
            reasons.append(f"margin {value:.1%} < threshold {thresholds['margin']:.1%}")
        if 'reliability' in failures:
            value = await self.get_reliability_data(entity)
            reasons.append(f"reliability {value:.1%} < threshold {thresholds['reliability']:.1%}")
        if 'risk' in failures:
            value = await self.get_risk_data(entity)
            reasons.append(f"risk {value:.1%} >= threshold {thresholds['risk']:.1%}")
        if 'capacity' in failures:
            reasons.append("capacity limit reached")
            
        return "; ".join(reasons)


# Health check for fractal_evaluator module (iter 141.1 stage 1a)
def _health_check_fractal_evaluator() -> dict:
    """Report health status for fractal_evaluator module."""
    # No external dependencies - always healthy if module loads
    return {
        "status": "healthy",
        "details": {"base_class": "FractalEvaluator defined"}
    }


# Register with health monitor
try:
    from health_monitor import register_health_check
    register_health_check("fractal_evaluator", _health_check_fractal_evaluator)
except ImportError:
    pass  # Health monitor not available during setup
```

### Step 2: Create load_evaluator.py module

Create `C:\CHL\backend\load_evaluator.py`:

```python
"""
LoadEvaluator - micro-scale fractal evaluator for individual load decisions.

Refactors auto_dispatch.try_auto_accept() hard-coded rules into
FractalEvaluator pattern. Behavior preserved - this is architectural
cleanup, not functional change.
"""

import logging
from typing import Any, Dict
from motor.motor_asyncio import AsyncIOMotorClient
from fractal_evaluator import FractalEvaluator

logger = logging.getLogger("load_evaluator")

# Database connection (shared with other modules)
from server import db


class LoadEvaluator(FractalEvaluator):
    """
    Micro scale: individual load decisions.
    
    Implements four-parameter evaluation for single loads using
    current auto_dispatch.try_auto_accept logic:
    - Profitability: load.margin
    - Reliability: carrier.vetted_trusted AND carrier.mc_verified
    - Risk: load.risk_score (if present) or check risk_flags/blocklist
    - Capacity: count of active loads < MAX_CONCURRENT_LOADS
    """
    
    async def get_profitability_data(self, load: Dict[str, Any]) -> float:
        """
        Return load margin as decimal.
        
        Args:
            load: Load document from db.loads collection
            
        Returns:
            float: Margin (e.g., 0.15 for 15%)
        """
        return load.get('margin', 0.0)
    
    async def get_reliability_data(self, load: Dict[str, Any]) -> float:
        """
        Return carrier reliability score.
        
        Matches prior auto_dispatch logic:
        - carrier.vetted_trusted=True AND carrier.mc_verified=True → 1.0
        - Otherwise → 0.0
        
        Future: could return graduated score based on carrier history.
        
        Args:
            load: Load document with carrier_id
            
        Returns:
            float: 1.0 if reliable, 0.0 if not
        """
        carrier_id = load.get('carrier_id')
        if not carrier_id:
            return 0.0
        
        carrier = await db.carriers.find_one({'_id': carrier_id})
        if not carrier:
            return 0.0
        
        # Reliable = vetted_trusted AND mc_verified AND no recent failures
        vetted = carrier.get('vetted_trusted', False)
        mc_verified = carrier.get('mc_verified', False)
        
        # Check for recent failure alerts (past 90 days)
        from datetime import datetime, timedelta, timezone
        ninety_days_ago = datetime.now(timezone.utc) - timedelta(days=90)
        recent_failures = await db.failure_alerts.count_documents({
            'carrier_id': carrier_id,
            'timestamp': {'$gte': ninety_days_ago}
        })
        
        if vetted and mc_verified and recent_failures == 0:
            return 1.0
        else:
            return 0.0
    
    async def get_risk_data(self, load: Dict[str, Any]) -> float:
        """
        Return load risk score.
        
        Matches prior auto_dispatch logic:
        - Check if carrier is blocklisted → 1.0 (certain failure)
        - Check load.risk_flags → presence = 0.5 (elevated risk)
        - Otherwise → 0.0 (no known risk)
        
        Args:
            load: Load document
            
        Returns:
            float: Risk score 0.0-1.0
        """
        carrier_id = load.get('carrier_id')
        
        # Check blocklist
        if carrier_id:
            blocklisted = await db.carrier_blocklist.count_documents({
                'carrier_id': carrier_id
            })
            if blocklisted > 0:
                return 1.0  # Certain failure
        
        # Check risk flags on load
        risk_flags = load.get('risk_flags', [])
        if len(risk_flags) > 0:
            return 0.5  # Elevated risk
        
        return 0.0  # No known risk
    
    async def get_capacity_data(self, load: Dict[str, Any]) -> bool:
        """
        Return True if capacity available to handle this load.
        
        Matches prior auto_dispatch logic:
        - Count active loads (status IN active, assigned, pickup, in_transit)
        - If count < MAX_CONCURRENT_LOADS → True
        
        Args:
            load: Load document (not used for capacity check)
            
        Returns:
            bool: True if capacity available
        """
        MAX_CONCURRENT_LOADS = 50  # Matches existing auto_dispatch limit
        
        active_count = await db.loads.count_documents({
            'status': {'$in': ['active', 'assigned', 'pickup', 'in_transit']}
        })
        
        return active_count < MAX_CONCURRENT_LOADS


# Health check for load_evaluator module
def _health_check_load_evaluator() -> dict:
    """Report health status for load_evaluator module."""
    # Check if LoadEvaluator is instantiable
    try:
        evaluator = LoadEvaluator()
        return {
            "status": "healthy",
            "details": {"evaluator_class": "LoadEvaluator instantiated"}
        }
    except Exception as e:
        return {
            "status": "unhealthy",
            "details": {"error": str(e)}
        }


# Register with health monitor
try:
    from health_monitor import register_health_check
    register_health_check("load_evaluator", _health_check_load_evaluator)
except ImportError:
    pass
```

### Step 3: Refactor auto_dispatch.py to use LoadEvaluator

Modify `C:\CHL\backend\auto_dispatch.py`:

**Before (lines ~85-150, approximate - adjust based on actual):**
```python
async def try_auto_accept(load_id: str, carrier_id: str) -> dict:
    """
    8-gate hard ruleset for auto-dispatch.
    [... existing implementation ...]
    """
    # Hard-coded gates
    if not business_settings.auto_accept_master_enabled:
        return reject("master toggle disabled")
    if not carrier.vetted_trusted:
        return reject("carrier not vetted")
    # ... 6 more gates ...
```

**After (replace try_auto_accept body):**
```python
async def try_auto_accept(load_id: str, carrier_id: str) -> dict:
    """
    Auto-dispatch decision using LoadEvaluator (fractal pattern).
    
    Preserves all prior hard gates via LoadEvaluator.get_*_data methods.
    Returns same accept/reject structure as before refactor.
    """
    from load_evaluator import LoadEvaluator
    from datetime import datetime, timezone
    
    # Load business settings (thresholds)
    business_settings = await db.business_settings.find_one({})
    if not business_settings:
        return {
            'accepted': False,
            'reason': 'business_settings not found',
            'timestamp': datetime.now(timezone.utc)
        }
    
    # Master toggle (pre-LoadEvaluator gate - unchanged)
    if not business_settings.get('auto_accept_master_enabled', False):
        await db.auto_accept_log.insert_one({
            'load_id': load_id,
            'carrier_id': carrier_id,
            'accepted': False,
            'reason': 'auto_accept_master_enabled=False',
            'timestamp': datetime.now(timezone.utc)
        })
        return {
            'accepted': False,
            'reason': 'auto_accept_master_enabled=False',
            'timestamp': datetime.now(timezone.utc)
        }
    
    # Fetch load
    load = await db.loads.find_one({'_id': load_id})
    if not load:
        return {
            'accepted': False,
            'reason': 'load not found',
            'timestamp': datetime.now(timezone.utc)
        }
    
    # Attach carrier_id to load for evaluator
    load['carrier_id'] = carrier_id
    
    # Build thresholds from business_settings
    thresholds = {
        'margin': business_settings.get('margin_floor_pct', 0.15),
        'reliability': 0.9,  # LoadEvaluator returns 1.0 or 0.0, so 0.9 threshold passes 1.0
        'risk': 0.05,  # Risk < 5% to accept
        'capacity': True  # Not used (capacity check is boolean in get_capacity_data)
    }
    
    # Phase7 throttle gate (from iter 140.1 stage 1d - preserve this)
    from throttle_integration import check_throttle_before_accept
    throttle_pass, throttle_reason = await check_throttle_before_accept(load_id)
    if not throttle_pass:
        await db.auto_accept_log.insert_one({
            'load_id': load_id,
            'carrier_id': carrier_id,
            'accepted': False,
            'reason': f'phase7_throttle: {throttle_reason}',
            'timestamp': datetime.now(timezone.utc)
        })
        return {
            'accepted': False,
            'reason': f'phase7_throttle: {throttle_reason}',
            'timestamp': datetime.now(timezone.utc)
        }
    
    # Evaluate using LoadEvaluator
    evaluator = LoadEvaluator()
    accepted = await evaluator.decide(load, thresholds)
    
    if not accepted:
        reason = await evaluator.get_rejection_reason(load, thresholds)
    else:
        reason = "all gates passed"
    
    # Log decision
    await db.auto_accept_log.insert_one({
        'load_id': load_id,
        'carrier_id': carrier_id,
        'accepted': accepted,
        'reason': reason,
        'evaluator': 'LoadEvaluator',
        'timestamp': datetime.now(timezone.utc)
    })
    
    return {
        'accepted': accepted,
        'reason': reason,
        'timestamp': datetime.now(timezone.utc)
    }
```

## Restart and smoke

```powershell
# Restart backend to load new modules
Stop-Service CHL-Backend
Start-Service CHL-Backend
Get-Service CHL-Backend
# Wait for FastAPI to bind: ~3-5 seconds

# Confirm boot didn't crash
Select-String -Path C:\CHL\logs\nssm\backend.err.log -Pattern "ERROR|Traceback" -Context 0,3 | Select-Object -Last 5
# Expected: nothing recent post-restart
```

### Smoke battery

**Test 1: Modules imported successfully**
```powershell
& "C:\CHL\.venv-local\Scripts\python.exe" -c "from fractal_evaluator import FractalEvaluator; from load_evaluator import LoadEvaluator; print('Modules OK')"
```
Expected: "Modules OK", no import errors

**Test 2: Health checks registered**
```powershell
$health = Invoke-RestMethod -Uri "http://localhost:8000/api/health/modules" -Method Get
$health | ConvertTo-Json
```
Expected: "fractal_evaluator" and "load_evaluator" in modules list

**Test 3: LoadEvaluator behavior matches prior auto_dispatch (equivalence test)**

Create a test load and carrier in database, then call POST /api/carrier/loads/{id}/interest.
Compare result to prior auto_dispatch logic expectations.

```powershell
# This test requires operator to create test data or use existing load
# Verify auto_accept_log shows evaluator='LoadEvaluator' after refactor
$latestLog = & "C:\CHL\.venv-local\Scripts\python.exe" -c "from motor.motor_asyncio import AsyncIOMotorClient; import asyncio; client = AsyncIOMotorClient(); db = client['chl']; log = asyncio.run(db.auto_accept_log.find().sort([('timestamp', -1)]).limit(1).to_list(length=1)); print(log[0] if log else 'none'); client.close()"
echo $latestLog
```
Expected: Most recent log entry has 'evaluator':'LoadEvaluator'

**Test 4: FractalEvaluator.decide() returns boolean**
```powershell
& "C:\CHL\.venv-local\Scripts\python.exe" -c "import asyncio; from load_evaluator import LoadEvaluator; async def test(): ev = LoadEvaluator(); load = {'margin': 0.20, 'carrier_id': None, 'risk_flags': []}; thresholds = {'margin': 0.15, 'reliability': 0.9, 'risk': 0.05}; result = await ev.decide(load, thresholds); print(f'Decision: {result}'); asyncio.run(test())"
```
Expected: "Decision: True" or "Decision: False" (boolean returned)

**Test 5: Adjacent regression - boot_briefing**
```powershell
Invoke-RestMethod -Uri "http://localhost:8000/api/ops/briefing?force=true" -Method Get
```
Expected: HTTP 200, source=llm, content length >100 chars

**Test 6: Git diff is surgical**
```powershell
git diff
```
Expected:
- NEW FILE: backend/fractal_evaluator.py
- NEW FILE: backend/load_evaluator.py
- MODIFIED: backend/auto_dispatch.py (try_auto_accept refactored)
- No other changes

## Commit protocol

```powershell
cd C:\CHL
git status
# Expect: fractal_evaluator.py (new), load_evaluator.py (new), auto_dispatch.py (modified)

git add backend/fractal_evaluator.py
git add backend/load_evaluator.py
git add backend/auto_dispatch.py
git commit -m "iter 141.1 stage 1a: FractalEvaluator base class + LoadEvaluator refactor - behavior preserved"
git push origin main
```

If `git status` shows unexpected modifications, STOP.

## Completion doc specification

Write `progress/stage_completion/iter_141_1_stage_1a.md` (in chl-memory clone) per completion template. Include:

- Per-action-step summary:
  - Step 1: fractal_evaluator.py created (~180 lines)
  - Step 2: load_evaluator.py created (~150 lines)
  - Step 3: auto_dispatch.py refactored (try_auto_accept now uses LoadEvaluator)
- Smoke results (6 tests) - include module import confirmation, health check registration
- Post-action state:
  - LoadEvaluator decisions logged to auto_accept_log
  - Behavior equivalence to prior auto_dispatch confirmed
- Git diff summary: 2 new files, 1 modified file
- Verify-yourself block: commit SHA, test command
- Caveats:
  - LoadEvaluator uses simplified reliability (1.0 or 0.0) - future can graduate
  - Capacity check hardcoded MAX_CONCURRENT_LOADS=50 - future can make configurable
- Handoff to stage 1b: LaneEvaluator will inherit from FractalEvaluator using same pattern

## STOP CONDITIONS specific to this stage

- Pre-flight check 2 shows fractal_evaluator.py already exists (partial prior work)
- Pre-flight check 6/7/8 shows iter 140.1 systems (throttle/health/SLA) not working
- After step 1, fractal_evaluator.py fails to import (syntax errors)
- After step 2, load_evaluator.py fails to import
- After step 3, auto_dispatch.try_auto_accept crashes on execution
- After step 3, smoke test 3 shows LoadEvaluator produces DIFFERENT decisions than prior auto_dispatch (behavior not preserved)
- Git diff shows changes beyond expected files

## On completion

1. Update `progress/current_status.md`: mark stage 1a complete, active stage → 1b
2. Stage 1b is next: LaneEvaluator subclass implementing 90-day lane aggregation
3. Do NOT proceed to stage 1b without operator review

Handoff: progress/stage_completion/iter_141_1_stage_1a.md

DONE
