# Stage 1c: Lane Scoring Background Loop

## Stage objective

Build background cron task that updates lane scores every 6 hours for active lanes. "Active lane" = any origin-destination pair with load activity in last 30 days. Cron queries db.loads for active lanes, calls LaneEvaluator for each, persists to db.lane_scores. Integrates with SLA monitor (@track_sla on scoring loop) and throttle (pause if RED/ORANGE). Success = cron runs every 6h, updates lane_scores, respects throttle states.

## Pre-flight checks

Verify stage 1b complete, /api/lanes/.../score working, db.lane_scores has entries.

## Action steps

### Step 1: Create lane_scoring_cron.py module

```python
import asyncio
import logging
from datetime import datetime, timedelta, timezone
from motor.motor_asyncio import AsyncIOMotorClient
from lane_evaluator import LaneEvaluator
from sla_monitor import track_sla
from throttle_integration import check_throttle_before_accept

logger = logging.getLogger("lane_scoring_cron")

async def update_active_lane_scores():
    """
    Update scores for all active lanes (loads in last 30 days).
    Runs every 6 hours via cron.
    """
    from server import db
    
    # Throttle check - pause scoring if system degraded
    throttle_ok, reason = await check_throttle_before_accept("lane_scoring_cron")
    if not throttle_ok:
        logger.warning(f"Lane scoring paused: {reason}")
        return
    
    # Find active lanes (loads in last 30 days)
    thirty_days_ago = datetime.now(timezone.utc) - timedelta(days=30)
    pipeline = [
        {'$match': {'created_at': {'$gte': thirty_days_ago}}},
        {'$group': {
            '_id': {
                'origin_city': '$origin_city',
                'origin_state': '$origin_state',
                'destination_city': '$destination_city',
                'destination_state': '$destination_state'
            }
        }}
    ]
    
    active_lanes = await db.loads.aggregate(pipeline).to_list(length=1000)
    logger.info(f"Updating scores for {len(active_lanes)} active lanes")
    
    evaluator = LaneEvaluator()
    settings = await db.business_settings.find_one({})
    thresholds = settings.get('lane_thresholds', {
        'margin': 0.18, 'reliability': 0.85, 'risk': 0.08, 'capacity': 20
    })
    
    updated_count = 0
    for lane_doc in active_lanes:
        lane = lane_doc['_id']
        
        try:
            decision = await evaluator.decide(lane, thresholds)
            profitability = await evaluator.get_profitability_data(lane)
            reliability = await evaluator.get_reliability_data(lane)
            risk = await evaluator.get_risk_data(lane)
            capacity = await evaluator.get_capacity_data(lane)
            
            await db.lane_scores.update_one(
                lane,
                {'$set': {
                    'score': decision,
                    'profitability': profitability,
                    'reliability': reliability,
                    'risk': risk,
                    'capacity': capacity,
                    'updated_at': datetime.now(timezone.utc)
                }},
                upsert=True
            )
            updated_count += 1
        except Exception as e:
            logger.error(f"Failed to score lane {lane}: {e}")
    
    logger.info(f"Updated {updated_count}/{len(active_lanes)} lane scores")

# Wrap in SLA tracking
update_active_lane_scores = track_sla("lane_eval", 60000)(update_active_lane_scores)

# Health check
def _health_check_lane_scoring_cron():
    return {"status": "healthy", "details": {"cron": "lane_scoring_cron defined"}}

try:
    from health_monitor import register_health_check
    register_health_check("lane_scoring_cron", _health_check_lane_scoring_cron)
except ImportError:
    pass
```

### Step 2: Register cron in server.py

Add to server.py startup:
```python
from lane_scoring_cron import update_active_lane_scores
import asyncio

@app.on_event("startup")
async def start_lane_scoring_cron():
    async def lane_scoring_loop():
        while True:
            await asyncio.sleep(6 * 60 * 60)  # 6 hours
            try:
                await update_active_lane_scores()
            except Exception as e:
                logger.error(f"Lane scoring cron failed: {e}")
    
    asyncio.create_task(lane_scoring_loop())
```

## Smoke battery

1. Cron function callable: python -c "from lane_scoring_cron import update_active_lane_scores; print('OK')"
2. Manual trigger: run update_active_lane_scores() via python, verify db.lane_scores updates
3. SLA tracking: db.sla_metrics has "lane_eval" entries after run
4. Throttle respect: set throttle to RED, verify cron skips (check logs)
5. Health check registered: /api/health/modules shows lane_scoring_cron
6. Adjacent regression: /api/ops/briefing works

## Commit

```powershell
git add backend/lane_scoring_cron.py backend/server.py
git commit -m "iter 141.1 stage 1c: lane scoring background loop (6h cron)"
git push origin main
```

DONE
