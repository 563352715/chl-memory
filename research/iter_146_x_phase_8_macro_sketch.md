# Iter 146.x — Phase 8 Macro-Market Layer Preliminary Scope Sketch

> **Source:** PM Claude (`@pm-lead`) via AgentDM messages `dc4781f1` (Part 1) + `62b76e31` (Part 2) + `3a3e2951` (Part 3 final) on 2026-05-06.
> **Status:** ✅ COMPLETE — all 3 chunks reconstructed. Operator can review whenever ready; PM Claude formal agenda for iter 146.x is drafted when operator authorizes (post-Phase-3 / post-Phase-4 / post-Phase-5).

---

## Goal

Build `MarketEvaluator` (macro-scale `FractalEvaluator`) for strategic market entry/exit decisions. **Third tier of the fractal pattern:** Load (micro) → Lane (meso) → Market (macro).

## Entity Definition: "Market"

A market = **geographic region + season + equipment type slice**
- **Example:** "Midwest Van Q4" aggregates all Chicago/Detroit/Minneapolis Van lanes during Oct–Dec
- **Regions:** Midwest, Southeast, Northeast, Southwest, West Coast (5 total)
- **Equipment:** Van, Reefer, Flatbed, Stepdeck (4 total)
- **Seasons:** Q1, Q2, Q3, Q4 (4 total)
- **Total markets:** 5 regions × 4 equipment × 4 seasons = **80 distinct markets**

## MarketEvaluator(FractalEvaluator) — 4 Parameters at Macro Scale

### 1. Profitability (market-level margin)
- **Metric:** Mean `composite_score` across all lanes in the market
- **Formula:** `avg(lane.composite_score)` for lanes matching `(region, equipment, season)`
- **Threshold:** `profitability_min >= 0.20` — markets must sustain 20%+ margins
- **Data source:** `db.lane_scores` filtered by market criteria

### 2. Reliability (capacity availability)
- **Metric:** % of weeks with surplus carrier capacity in the market
- **Formula:** `weeks_with_available_carriers / total_weeks_in_quarter`
- **Threshold:** `reliability_min >= 0.80` — 80% of weeks need carrier surplus
- **Data source:** `db.carriers.lanes_served` + `db.carrier_offers` aggregated weekly

### 3. Risk (market volatility)
- **Metric:** Rate volatility (coefficient of variation)
- **Formula:** `stdev(lane_rates) / mean(lane_rates)` over rolling 90 days
- **Threshold:** `risk_max <= 0.15` — 15% rate swing acceptable
- **Data source:** `db.loads.broker_margin_pct` + `db.lane_scores.profitability` aggregated

### 4. Capacity (broker bandwidth ceiling)
- **Metric:** Market load volume vs operator's max bandwidth
- **Formula:** `monthly_load_count / operator_max_loads_per_month`
- **Threshold:** `capacity_max <= 0.70` — don't exceed 70% of bandwidth on a single market (concentration risk)
- **Data source:** `db.loads.count()` filtered by market + `business_settings.operator_capacity`

## Market Scoring Logic
```python
class MarketEvaluator(FractalEvaluator):
    def evaluate(self, market, thresholds):
        return {
            'profitability': market.avg_composite >= thresholds['margin'],
            'reliability':   market.carrier_weeks >= thresholds['reliability'],
            'risk':          market.rate_volatility < thresholds['risk'],
            'capacity':      market.load_density <= thresholds['capacity'],
        }
```

## Use Cases

1. **Strategic dashboard** — operator views "Which markets should we prioritize this quarter?"
2. **Entry/exit decisions** — market passes all 4 gates → expand presence; fails → reduce/exit
3. **Resource allocation** — high-scoring markets get more aggressive auto-bid thresholds
4. **Forecasting** — market trends signal when to prepare carrier-network expansion

## Stage Breakdown (3 stages estimated)

### Stage 8a: Market schema + aggregation
- New `db.market_signals` collection: `{region, equipment, season, profitability, reliability, risk, capacity, score_timestamp}`
- New `backend/market_aggregator.py` cron: runs daily, rolls up `lane_scores` → `market_signals`
- `MarketEvaluator` class + unit tests

### Stage 8b: Market scoring dashboard
- `/api/markets/scores` endpoint — returns all 80 markets with composite scores
- `/api/markets/{region}/{equipment}/{season}/detail` — deep-dive on a specific market
- Frontend heatmap: 5 regions × 4 equipment types, color by composite score (Emergent task likely)

### Stage 8c: Market-aware auto-bid tuning
- `business_settings.market_bid_multipliers`: `{market_id: multiplier}`
- High-scoring markets get `bid_multiplier=1.2` (20% more aggressive)
- Low-scoring markets get `bid_multiplier=0.8` (20% more conservative)
- `auto_bid` module (from iter 141.2) reads multiplier: `bid_amount *= market_multiplier`
- Operator can override per-market multipliers via `/api/markets/{id}/tune`

## Open Questions (operator decision points)

1. **DAT MarketWatch integration** (paid data source, ~$500/month) for external market signals — yes/no/defer?
2. **Should market scoring trigger YELLOW throttle state** — reduce intake on volatile markets?
3. **Market-level carrier density tracking** — does this belong in Phase 3 (carrier network) or Phase 8?
4. **Seasonal adjustments** — Q4 peak vs Q1 slow season — hard-code thresholds per season, or ML-learn from historical data?

## Dependencies on prior phases

- **Phase 2 (iter 141.1):** LaneEvaluator pattern proven, `db.lane_scores` collection exists ✅ already done
- **Phase 3 (iter 142.x):** Carrier density metrics feed into market reliability score — `db.carriers.lanes_served` + `db.carrier_offers` need to exist before Phase 8 can run reliability formula
- **Phase 7 (iter 140.1):** Throttle integration if market volatility triggers YELLOW state ✅ already done

## Out of Scope (deferred)

- ML forecasting (Phase 9+)
- Multi-broker comparison (Phase 9+)
- Real-time rate prediction (future enhancement)
- External data feeds beyond DAT (trucking indices, fuel prices, weather, etc.)

## Timeline

Iter 146.x+ — opens **post-Phase-3 carrier network, post-Phase-4 tracking, post-Phase-5 completion**. Estimated ~400 lines total across 3 stages.

## Architectural Win

Phase 8 completes the **fractal trifecta** — `LoadEvaluator` (micro) + `LaneEvaluator` (meso) + `MarketEvaluator` (macro) all inherit from the same `FractalEvaluator` base class. **Same 4 parameters, same decision logic, three scales.** Strategic decisions at any zoom level use the same architectural pattern.

---

**Source:** AgentDM messages `dc4781f1` (Part 1) + `62b76e31` (Part 2) + `3a3e2951` (Part 3 final). Reconstructed via `@dev-engineer` ↔ `@pm-lead` async loop on 2026-05-06.
