# Stage 1d: DAT Load Board Integration

## Stage objective

Wire DAT load board adapter live. Poll DAT API every 15 minutes for new loads, filter by lane scores (only loads on lanes with score=True), persist to db.dat_opportunities collection. Integrate throttle (pause polling if RED/ORANGE) and SLA (track poll duration). Requires operator to provide DAT credentials (API key, endpoint URL) via environment variables. Success = DAT poller runs every 15min, fetches loads, filters by lane scores, throttle-gated.

## Pre-flight checks

Verify stages 1a-1c complete, lane_scores populated. Confirm operator has provided:
- DAT_API_KEY env var
- DAT_API_ENDPOINT env var (default https://api.dat.com/v1/loads)
- DAT_ACCOUNT_ID env var

## Action steps

### Step 1: Create dat_adapter.py module

Implement DAT API client using httpx:
```python
import httpx
import logging
from typing import List, Dict
from datetime import datetime, timezone

logger = logging.getLogger("dat_adapter")

class DATAdapter:
    def __init__(self, api_key: str, endpoint: str, account_id: str):
        self.api_key = api_key
        self.endpoint = endpoint
        self.account_id = account_id
    
    async def fetch_loads(self, origin_city: str = None, dest_city: str = None) -> List[Dict]:
        """Fetch loads from DAT API."""
        headers = {'Authorization': f'Bearer {self.api_key}'}
        params = {'accountId': self.account_id}
        
        if origin_city:
            params['originCity'] = origin_city
        if dest_city:
            params['destinationCity'] = dest_city
        
        async with httpx.AsyncClient(timeout=30.0) as client:
            response = await client.get(f"{self.endpoint}/search", headers=headers, params=params)
            response.raise_for_status()
            return response.json().get('loads', [])
```

### Step 2: Create dat_poller.py background loop

```python
import asyncio
import os
from dat_adapter import DATAdapter
from throttle_integration import check_throttle_before_accept
from sla_monitor import track_sla
from server import db
from datetime import datetime, timezone

async def poll_dat_loads():
    """Poll DAT for new loads, filter by lane scores."""
    # Throttle check
    throttle_ok, reason = await check_throttle_before_accept("dat_polling")
    if not throttle_ok:
        logger.warning(f"DAT polling paused: {reason}")
        return
    
    # Initialize DAT adapter
    adapter = DATAdapter(
        api_key=os.environ['DAT_API_KEY'],
        endpoint=os.environ.get('DAT_API_ENDPOINT', 'https://api.dat.com/v1/loads'),
        account_id=os.environ['DAT_ACCOUNT_ID']
    )
    
    # Fetch loads (unfiltered first pass)
    loads = await adapter.fetch_loads()
    logger.info(f"Fetched {len(loads)} loads from DAT")
    
    # Filter by lane scores
    scored_count = 0
    for load in loads:
        origin_city = load.get('originCity')
        origin_state = load.get('originState')
        dest_city = load.get('destinationCity')
        dest_state = load.get('destinationState')
        
        # Look up lane score
        lane_score = await db.lane_scores.find_one({
            'origin_city': origin_city,
            'origin_state': origin_state,
            'destination_city': dest_city,
            'destination_state': dest_state
        })
        
        if lane_score and lane_score.get('score') == True:
            # Persist opportunity
            await db.dat_opportunities.insert_one({
                'dat_load_id': load.get('loadId'),
                'origin_city': origin_city,
                'origin_state': origin_state,
                'destination_city': dest_city,
                'destination_state': dest_state,
                'rate_usd': load.get('rate'),
                'equipment_type': load.get('equipmentType'),
                'lane_score': lane_score.get('profitability'),
                'fetched_at': datetime.now(timezone.utc)
            })
            scored_count += 1
    
    logger.info(f"Persisted {scored_count}/{len(loads)} opportunities (lane score filter)")

poll_dat_loads = track_sla("dat_poll", 15000)(poll_dat_loads)  # 15s target
```

### Step 3: Register polling loop in server.py

```python
from dat_poller import poll_dat_loads

@app.on_event("startup")
async def start_dat_poller():
    async def dat_polling_loop():
        while True:
            await asyncio.sleep(15 * 60)  # 15 minutes
            try:
                await poll_dat_loads()
            except Exception as e:
                logger.error(f"DAT polling failed: {e}")
    
    asyncio.create_task(dat_polling_loop())
```

## Smoke battery

1. DAT adapter credentials present: echo $env:DAT_API_KEY (not empty)
2. Manual poll test: run poll_dat_loads() once, verify db.dat_opportunities has entries
3. Lane score filter works: count(dat_opportunities) ≤ count(dat raw loads)
4. Throttle respect: set RED, verify polling skips
5. SLA tracking: db.sla_metrics has "dat_poll" entries
6. Adjacent regression: /api/ops/briefing works

## Commit

```powershell
git add backend/dat_adapter.py backend/dat_poller.py backend/server.py
git commit -m "iter 141.1 stage 1d: DAT load board integration (15min polling, lane-score filtered)"
git push origin main
```

DONE
