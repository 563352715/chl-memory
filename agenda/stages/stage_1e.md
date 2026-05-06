# Stage 1e: Operator Dashboard Endpoint

## Stage objective

Build /api/lanes/top/{N} endpoint for operator review. Returns top N lanes ranked by score (profitability × reliability / risk), with last 24h DAT opportunity count, load history metrics. Operator uses this to decide which lanes to manually book. Success = dashboard endpoint returns ranked lanes with actionable metrics, operator can review before booking.

## Pre-flight checks

Verify stages 1a-1d complete, db.dat_opportunities has entries, lane_scores populated.

## Action steps

### Step 1: Create dashboard endpoint in lane_evaluator.py

```python
@router.get("/api/lanes/top/{N}")
async def top_lanes_dashboard(N: int = 20):
    """Return top N lanes ranked by composite score."""
    from datetime import datetime, timedelta, timezone
    
    # Fetch all scored lanes
    lane_scores = await db.lane_scores.find({'score': True}).to_list(length=1000)
    
    # Rank by composite: (profitability * reliability) / max(risk, 0.01)
    for lane in lane_scores:
        profitability = lane.get('profitability', 0.0)
        reliability = lane.get('reliability', 0.0)
        risk = max(lane.get('risk', 0.01), 0.01)  # Avoid divide-by-zero
        lane['composite_score'] = (profitability * reliability) / risk
    
    # Sort descending
    lane_scores.sort(key=lambda x: x['composite_score'], reverse=True)
    top_N = lane_scores[:N]
    
    # Enrich with DAT opportunity counts (last 24h)
    twenty_four_hours_ago = datetime.now(timezone.utc) - timedelta(hours=24)
    for lane in top_N:
        opp_count = await db.dat_opportunities.count_documents({
            'origin_city': lane['origin_city'],
            'origin_state': lane['origin_state'],
            'destination_city': lane['destination_city'],
            'destination_state': lane['destination_state'],
            'fetched_at': {'$gte': twenty_four_hours_ago}
        })
        lane['opportunities_24h'] = opp_count
        
        # Load history count (90 days)
        ninety_days_ago = datetime.now(timezone.utc) - timedelta(days=90)
        load_count = await db.loads.count_documents({
            'origin_city': lane['origin_city'],
            'origin_state': lane['origin_state'],
            'destination_city': lane['destination_city'],
            'destination_state': lane['destination_state'],
            'created_at': {'$gte': ninety_days_ago}
        })
        lane['historical_loads_90d'] = load_count
    
    return {
        'top_lanes': top_N,
        'total_scored_lanes': len(lane_scores),
        'ranking_formula': '(profitability * reliability) / risk'
    }
```

### Step 2: Document endpoint for operator

Update memory/THROTTLE_PLAYBOOK.md or create memory/LANE_REVIEW_GUIDE.md:
- How to interpret composite_score
- What opportunities_24h means (DAT loads available now)
- How to manually book from dashboard

## Smoke battery

1. Dashboard endpoint exists: GET /api/lanes/top/20 → HTTP 200
2. Response has top_lanes array with ≤20 entries
3. Each lane has composite_score, opportunities_24h, historical_loads_90d
4. Lanes sorted descending by composite_score
5. Empty state (no scored lanes) returns empty array gracefully
6. Adjacent regression: /api/ops/briefing works

## Commit

```powershell
git add backend/lane_evaluator.py
git commit -m "iter 141.1 stage 1e: operator dashboard /api/lanes/top/{N} - ranked lanes with DAT opportunities"
git push origin main
```

## On completion

Iter 141.1 complete. Phase 2 foundation established:
- FractalEvaluator architecture proven
- Lane-level scoring operational
- DAT integration live
- Operator can review top opportunities

Next iter candidates:
- 141.2: Auto-bid on top-tier lanes (stage 1f execution)
- 142.1: Phase 3 - Carrier Network (multi-carrier outreach)

DONE
