# Stage 1b: LaneEvaluator + Lane Scoring Endpoint

## Stage objective

Build LaneEvaluator subclass inheriting from FractalEvaluator. Implement 90-day origin-destination aggregation for four parameters (lane profitability, carrier density, failure rate, volume capacity). Create /api/lanes/{origin_city}/{origin_state}/{dest_city}/{dest_state}/score endpoint returning lane evaluation with breakdown. Persist scores to db.lane_scores collection. Success = lane scoring endpoint returns 200 with valid four-parameter scores, scores update on repeat calls with new load data.

## Pre-flight checks

Verify stage 1a complete, FractalEvaluator working, LoadEvaluator proven equivalent. Check db.loads has sufficient history (ideally 50+ loads across multiple lanes for meaningful aggregation).

## Action steps

### Step 1: Create lane_evaluator.py module

Implement LaneEvaluator(FractalEvaluator) with async methods:
- get_profitability_data: Query db.loads for {origin_city, origin_state, destination_city, destination_state} match, last 90 days, calculate mean(margin). Handle empty list → return 0.0.
- get_reliability_data: Count carriers on lane with vetted_trusted=True AND mc_verified=True, divide by total carriers. Empty → return 0.0.
- get_risk_data: Count failed loads (status IN cancelled/disputed/cargo_claim OR has failure_alert) divide by total. Empty → return 0.0.
- get_capacity_data: Calculate daily_avg volume, return daily_avg < 20 (threshold from FRACTAL_DECISION_FRAMEWORK).

Helper functions: _get_lane_loads(origin_city, origin_state, dest_city, dest_state, days=90) returns Motor cursor.

Register health check with health_monitor.

### Step 2: Create /api/lanes/score endpoint

In lane_evaluator.py, add FastAPI router:

```python
from fastapi import APIRouter
router = APIRouter()

@router.get("/api/lanes/{origin_city}/{origin_state}/{dest_city}/{dest_state}/score")
async def lane_score_endpoint(origin_city: str, origin_state: str, dest_city: str, dest_state: str):
    """Return lane evaluation score with four-parameter breakdown."""
    evaluator = LaneEvaluator()
    lane = {'origin_city': origin_city, 'origin_state': origin_state, 
            'destination_city': dest_city, 'destination_state': dest_state}
    
    # Thresholds from business_settings.lane_thresholds (default to hypothesis values)
    settings = await db.business_settings.find_one({})
    thresholds = settings.get('lane_thresholds', {
        'margin': 0.18,
        'reliability': 0.85,
        'risk': 0.08,
        'capacity': 20
    })
    
    evaluation = await evaluator.evaluate(lane, thresholds)
    decision = await evaluator.decide(lane, thresholds)
    
    # Get raw values for breakdown
    profitability = await evaluator.get_profitability_data(lane)
    reliability = await evaluator.get_reliability_data(lane)
    risk = await evaluator.get_risk_data(lane)
    capacity_ok = await evaluator.get_capacity_data(lane)
    
    # Persist to db.lane_scores
    from datetime import datetime, timezone
    await db.lane_scores.update_one(
        {'origin_city': origin_city, 'origin_state': origin_state,
         'destination_city': dest_city, 'destination_state': dest_state},
        {'$set': {
            'score': decision,
            'profitability': profitability,
            'reliability': reliability,
            'risk': risk,
            'capacity': capacity_ok,
            'thresholds': thresholds,
            'updated_at': datetime.now(timezone.utc)
        }},
        upsert=True
    )
    
    return {
        'lane': lane,
        'score': decision,
        'evaluation': evaluation,
        'metrics': {
            'profitability': profitability,
            'reliability': reliability,
            'risk': risk,
            'capacity': capacity_ok
        },
        'thresholds': thresholds
    }
```

### Step 3: Register router in server.py

Add to server.py:
```python
from lane_evaluator import router as lane_router
app.include_router(lane_router)
```

### Step 4: Initialize lane_thresholds in business_settings

If not present, insert hypothesis defaults:
```python
await db.business_settings.update_one(
    {},
    {'$setOnInsert': {
        'lane_thresholds': {
            'margin': 0.18,
            'reliability': 0.85,
            'risk': 0.08,
            'capacity': 20
        }
    }},
    upsert=True
)
```

## Smoke battery

1. Lane scoring endpoint exists: GET /api/lanes/Chicago/IL/Dallas/TX/score → HTTP 200
2. Response has score, evaluation, metrics, thresholds keys
3. db.lane_scores collection has entry after call
4. Repeat call updates updated_at timestamp
5. Empty-history lane (no loads) returns score=False with 0.0 metrics
6. Adjacent regression: /api/ops/briefing still works

## Commit

```powershell
git add backend/lane_evaluator.py backend/server.py
git commit -m "iter 141.1 stage 1b: LaneEvaluator + /api/lanes/{o}/{s}/{d}/{s}/score endpoint"
git push origin main
```

DONE
