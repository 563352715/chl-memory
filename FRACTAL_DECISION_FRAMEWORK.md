# Fractal Decision Framework

**Design Principle:** CHL operations use self-similar decision logic at multiple scales (load → lane → market). Same four-parameter framework evaluates profitability, reliability, risk, and capacity regardless of scope. Simple rules at micro level create emergent intelligence at macro level.

**Status:** Architectural foundation established iter 140.1 (throttle system demonstrates fractal pattern). Full implementation across Phases 2-8.

---

## Core Principle

**Fractal pattern in nature:** Same structure repeats at different scales (coastlines, trees, river networks, neural pathways).

**Fractal pattern in CHL:** Same decision logic evaluates individual loads, route patterns, and regional strategy. Parameters defined once, applied recursively.

**Why this works:**
- Reduces code complexity (one framework, multiple uses)
- Parameters learned at one scale inform others (load data → lane insights → market strategy)
- Aligns with how freight markets actually operate (micro decisions aggregate to macro patterns)
- Easier to reason about, debug, and improve (same logic everywhere)

---

## The Four-Parameter Framework

Every decision in CHL—regardless of scale—evaluates these four dimensions:

### 1. **Profitability**
Margin exceeds threshold for sustainable operation
- **Metric:** Profit margin (revenue - costs) / revenue
- **Thresholds scale-dependent:** Load 15%, Lane 18%, Market 20%

### 2. **Reliability**  
Counterparty performs as expected
- **Metric:** Historical performance score, failure rate, payment history
- **Thresholds scale-dependent:** Carrier 90% on-time, Lane 85% success rate, Market 80% stability

### 3. **Risk**
Probability of failure within acceptable bounds
- **Metric:** Combined risk score (payment default, cargo claim, carrier no-show, regulatory)
- **Thresholds scale-dependent:** Load <5% risk, Lane <8% risk, Market <10% risk

### 4. **Capacity**
Operational bandwidth available to handle volume
- **Metric:** Current utilization vs. max capacity (loads/day, operator hours, system throughput)
- **Thresholds scale-dependent:** Load-level immediate, Lane-level weekly, Market-level monthly

---

## Three Scales of Application

### **Micro Scale: Individual Load Decision**

**Question:** Should we accept this specific load?

**Evaluation:**
```python
def evaluate_load(load):
    return {
        'profitability': load.margin >= MARGIN_FLOOR_PCT,
        'reliability': carrier.vetted_trusted == True and carrier.mc_verified == True,
        'risk': load.risk_score < RISK_THRESHOLD,
        'capacity': current_active_loads < MAX_CONCURRENT_LOADS
    }
    
decision = all(evaluate_load(load).values())  # Accept if all four pass
```

**Data source:** Individual load record, carrier profile, real-time system state

**Decision frequency:** Per load (hundreds/day at scale)

**Current implementation:** `auto_dispatch.py:try_auto_accept()` (iter 139.46)

---

### **Meso Scale: Lane/Route Pattern Decision**

**Question:** Should we actively pursue this origin-destination lane?

**Evaluation:**
```python
def evaluate_lane(origin, destination):
    lane_loads = db.loads.find({
        'origin_city': origin,
        'destination_city': destination,
        'created_at': {'$gte': datetime.now() - timedelta(days=90)}
    })
    
    return {
        'profitability': mean([l.margin for l in lane_loads]) >= LANE_MARGIN_FLOOR,
        'reliability': len([c for c in lane_carriers if c.reliable]) >= MIN_CARRIER_DENSITY,
        'risk': lane_failure_rate(lane_loads) < LANE_RISK_THRESHOLD,
        'capacity': lane_volume_sustainable(lane_loads)
    }
    
decision = all(evaluate_lane(origin, destination).values())
```

**Data source:** Aggregated load history (90-day window), carrier network analysis

**Decision frequency:** Weekly lane review, triggered by pattern changes

**Planned implementation:** Phase 2 (iter 141.x) - Load board integration uses lane evaluation to prioritize which routes to monitor

---

### **Macro Scale: Market/Regional Strategy Decision**

**Question:** Should we operate in this geographic market?

**Evaluation:**
```python
def evaluate_market(region):
    regional_loads = db.loads.find({
        'region': region,
        'created_at': {'$gte': datetime.now() - timedelta(days=180)}
    })
    
    return {
        'profitability': regional_margin(regional_loads) >= REGIONAL_MARGIN_TARGET,
        'reliability': regional_shipper_quality(region) >= MIN_SHIPPER_SCORE,
        'risk': regional_market_stability(region) < MARKET_RISK_THRESHOLD,
        'capacity': can_scale_to_regional_volume(region)
    }
    
decision = all(evaluate_market(region).values())
```

**Data source:** Regional aggregates (6-month window), market research, competitive analysis

**Decision frequency:** Quarterly strategic review

**Planned implementation:** Phase 8 (iter 146.x) - Market optimization and expansion planning

---

## Implementation Roadmap

### **Phase 0-1: Foundation** ✅ COMPLETE (iter 139.46)
- Load-level decision framework exists (`auto_dispatch.try_auto_accept`)
- Hard-coded thresholds (margin_floor, vetted_trusted gates)
- No lane or market-level evaluation yet

### **Phase 2: Load Discovery & Evaluation** 🔄 NEXT (iter 141.x)
**Build lane-level fractal evaluation:**
- Create `lane_evaluator.py` module
- Aggregate load history per origin-destination pair
- Apply four-parameter framework to lanes
- Use lane scores to prioritize load board monitoring
- **Deliverable:** `/api/lanes/{origin}/{destination}/score` endpoint

**Why lane-level matters for Phase 2:**
- Load boards show hundreds of opportunities - can't evaluate all
- Lane-level scoring identifies which routes are worth pursuing
- Same decision logic as load-level, just aggregated data

### **Phase 3: Carrier Network & Dispatch** (iter 142.x)
**Extend fractal framework to carrier network:**
- Evaluate carrier reliability at lane level (not just individual load)
- "Do we have sufficient carrier density for this route?"
- Multi-carrier outreach based on lane-level capacity gaps
- **Deliverable:** Carrier network sufficiency score per lane

### **Phase 7: Monitoring & Self-Healing** ✅ IN PROGRESS (iter 140.1)
**Throttle system demonstrates fractal pattern:**
- Module health (micro) → System health (meso) → Throttle state (macro)
- Same health-check logic at three levels
- Proves fractal architecture is executable in CHL

### **Phase 8: Scale & Optimization** (iter 146.x)
**Build market-level fractal evaluation:**
- Create `market_evaluator.py` module
- Regional profitability, risk, capacity analysis
- Market entry/exit decisions using four-parameter framework
- **Deliverable:** Strategic planning dashboard with regional scores

---

## Code Pattern: Fractal Evaluator Base Class

**Shared logic across all scales:**

```python
from abc import ABC, abstractmethod
from typing import Dict, Any

class FractalEvaluator(ABC):
    """
    Base class for fractal decision-making.
    Subclasses implement scale-specific data aggregation.
    Decision logic (four parameters) remains constant.
    """
    
    @abstractmethod
    def get_profitability_data(self, entity) -> float:
        """Return margin/profitability metric for this entity at this scale"""
        pass
    
    @abstractmethod
    def get_reliability_data(self, entity) -> float:
        """Return counterparty reliability metric"""
        pass
    
    @abstractmethod
    def get_risk_data(self, entity) -> float:
        """Return risk score"""
        pass
    
    @abstractmethod
    def get_capacity_data(self, entity) -> bool:
        """Return capacity availability boolean"""
        pass
    
    def evaluate(self, entity, thresholds: Dict[str, Any]) -> Dict[str, bool]:
        """
        Four-parameter evaluation - same for all scales.
        Only data aggregation differs per subclass.
        """
        return {
            'profitability': self.get_profitability_data(entity) >= thresholds['margin'],
            'reliability': self.get_reliability_data(entity) >= thresholds['reliability'],
            'risk': self.get_risk_data(entity) < thresholds['risk'],
            'capacity': self.get_capacity_data(entity)
        }
    
    def decide(self, entity, thresholds: Dict[str, Any]) -> bool:
        """Accept if all four parameters pass"""
        evaluation = self.evaluate(entity, thresholds)
        return all(evaluation.values())


class LoadEvaluator(FractalEvaluator):
    """Micro scale: individual load decisions"""
    def get_profitability_data(self, load):
        return load.margin
    
    def get_reliability_data(self, load):
        return 1.0 if load.carrier.vetted_trusted else 0.0
    
    def get_risk_data(self, load):
        return load.risk_score
    
    def get_capacity_data(self, load):
        return db.loads.count({'status': 'active'}) < MAX_CONCURRENT_LOADS


class LaneEvaluator(FractalEvaluator):
    """Meso scale: route pattern decisions"""
    def get_profitability_data(self, lane):
        loads = db.loads.find({'origin': lane.origin, 'destination': lane.destination})
        return mean([l.margin for l in loads])
    
    def get_reliability_data(self, lane):
        # Carrier density on this lane
        carriers = get_lane_carriers(lane.origin, lane.destination)
        return len([c for c in carriers if c.reliable]) / max(len(carriers), 1)
    
    def get_risk_data(self, lane):
        loads = db.loads.find({'origin': lane.origin, 'destination': lane.destination})
        failures = len([l for l in loads if l.status == 'failed'])
        return failures / max(len(loads), 1)
    
    def get_capacity_data(self, lane):
        # Can we handle daily volume on this lane?
        daily_avg = get_lane_daily_volume(lane)
        return daily_avg < LANE_CAPACITY_THRESHOLD


class MarketEvaluator(FractalEvaluator):
    """Macro scale: regional strategy decisions"""
    def get_profitability_data(self, market):
        loads = db.loads.find({'region': market.region})
        return mean([l.margin for l in loads])
    
    def get_reliability_data(self, market):
        # Shipper payment reliability in region
        shippers = get_regional_shippers(market.region)
        return mean([s.payment_score for s in shippers])
    
    def get_risk_data(self, market):
        # Market volatility, regulatory risk, competitive intensity
        return calculate_market_risk_score(market.region)
    
    def get_capacity_data(self, market):
        # Can we scale operations to regional volume?
        return regional_volume_projectable(market.region)
```

**Usage:**
```python
# Same interface, different scales
load_eval = LoadEvaluator()
lane_eval = LaneEvaluator()
market_eval = MarketEvaluator()

# Load decision (micro)
accept_load = load_eval.decide(
    load, 
    thresholds={'margin': 0.15, 'reliability': 0.9, 'risk': 0.05}
)

# Lane decision (meso)
pursue_lane = lane_eval.decide(
    lane,
    thresholds={'margin': 0.18, 'reliability': 0.85, 'risk': 0.08}
)

# Market decision (macro)
enter_market = market_eval.decide(
    market,
    thresholds={'margin': 0.20, 'reliability': 0.80, 'risk': 0.10}
)
```

---

## Threshold Calibration

Thresholds are **parameters to be discovered** through empirical testing, not arbitrary choices.

### **Initial Values (Hypothesis)**

| Scale | Profitability | Reliability | Risk | Capacity |
|-------|--------------|-------------|------|----------|
| Load | 15% margin | 90% score | 5% risk | <50 active loads |
| Lane | 18% margin | 85% score | 8% risk | <20 loads/day |
| Market | 20% margin | 80% score | 10% risk | Can scale 2x |

### **Refinement Process**

1. **Run with initial thresholds** (100 loads, 10 lanes, 1 market)
2. **Measure outcomes** (actual margin, failure rate, intervention rate)
3. **Adjust thresholds** based on results:
   - Too many rejections → lower threshold
   - Too many failures → raise threshold
   - Operator overload → raise risk/lower capacity threshold
4. **Iterate** until performance targets met

**Performance targets from playbook:**
- Margin: sustain profitability (actual avg margin ≥ target)
- Reliability: <1% operator intervention rate
- Risk: <2% load failure rate
- Capacity: operator bandwidth <80% utilization

### **Threshold Storage**

Store thresholds in `business_settings` collection for runtime adjustment:

```python
{
    'load_thresholds': {
        'margin': 0.15,
        'reliability': 0.90,
        'risk': 0.05,
        'capacity_max': 50
    },
    'lane_thresholds': {
        'margin': 0.18,
        'reliability': 0.85,
        'risk': 0.08,
        'capacity_daily': 20
    },
    'market_thresholds': {
        'margin': 0.20,
        'reliability': 0.80,
        'risk': 0.10,
        'scale_factor': 2.0
    }
}
```

Operator can adjust via `/api/thresholds/update` without code changes.

---

## Benefits of Fractal Architecture

### **1. Code Reuse**
- Four-parameter framework defined once in `FractalEvaluator` base class
- Load/Lane/Market evaluators inherit logic, only override data aggregation
- Changes to decision logic propagate automatically to all scales

### **2. Parameter Learning Transfers**
- Margin learned from load acceptance informs lane threshold
- Lane success rate informs market entry decisions
- Single source of truth for what "acceptable" means

### **3. Consistent Mental Model**
- Operator asks same questions at every scale: "Is it profitable? Reliable? Low-risk? Do we have capacity?"
- Easier to train, document, explain to stakeholders
- Debugging simplified: "Which of the four parameters failed?"

### **4. Natural Emergence**
- Simple load-level rules create lane-level patterns
- Lane-level patterns create market-level strategy
- System behavior emerges from fractal repetition (like cellular automata)

### **5. Alignment with Reality**
- Freight markets ARE fractal: shipper decisions aggregate to lane demand, lane demand aggregates to regional capacity
- System architecture mirrors actual market structure
- More intuitive, less artificial

---

## Connection to Philosophical Foundation

**From operator discussion (May 6, 2026):**

> "What you might consider autonomous, I would simply say is an operation with defined parameters... This is how a human thinks too. They are not autonomous. They operate within a set of parameters."

**Fractal decision framework embodies this principle:**

- Not building "autonomous AI" that makes novel decisions
- Building **parameter-driven automation** that executes defined logic consistently
- **Same parameters at all scales** - reflects how reality operates (physical laws, market forces, biological imperatives)
- **Iterative refinement** - discover parameters through testing, not invention

**Fractal universe parallel:**

> "If we are a fractal universe, then the same patterns repeat at different scales. Our decision-making reflects this - same logic at load/lane/market levels."

- CHL's architecture mirrors natural structure
- Not imposed metaphor - actual operational reality
- Freight operations ARE fractal (individual loads → lane patterns → market dynamics)

---

## Testing & Validation

### **Unit Tests (Per Scale)**

Each evaluator subclass needs tests confirming:
- Data aggregation correct (mean, count, filtering)
- Threshold comparison works (>=, <)
- Edge cases handled (empty datasets, missing data)

### **Integration Tests (Cross-Scale)**

Verify parameter consistency:
- Load decisions aggregate to match lane patterns
- Lane evaluations inform market strategy
- Threshold changes propagate correctly

### **Empirical Validation**

Real-world testing proves framework:
- Run 100 loads with load-level thresholds
- Aggregate to lane-level, verify lane evaluations match outcomes
- Measure: Do high-scoring lanes actually perform better?

**Success metrics:**
- Load-level: <1% operator intervention on auto-accepted loads
- Lane-level: Predicted profitability within 5% of actual
- Market-level: Regional expansion decisions lead to sustained profitability

---

## Future Enhancements

### **Phase 9: Edge Cases & Compliance**

Fractal evaluation extends to specialized loads:
- HAZMAT: same four parameters + regulatory compliance dimension
- Oversize: same four parameters + route feasibility dimension
- Refrigerated: same four parameters + equipment availability dimension

### **Phase 10: Continuous Improvement**

ML-based threshold optimization:
- Historical outcomes → recommended threshold adjustments
- A/B testing different thresholds at lane level
- Automated parameter tuning (within human-defined bounds)

### **Temporal Fractal Dimension**

Same framework at different time scales:
- Real-time: "Accept this load now?"
- Daily: "Pursue this lane today?"
- Weekly: "Adjust lane priorities?"
- Monthly: "Enter/exit markets?"

---

## Summary

**Fractal Decision Framework** = Self-similar logic at load/lane/market scales

**Four parameters:** Profitability, Reliability, Risk, Capacity

**Three scales:** Micro (load), Meso (lane), Macro (market)

**One codebase:** `FractalEvaluator` base class + scale-specific subclasses

**Rollout:** Phase 2 (lane-level), Phase 8 (market-level)

**Foundation:** Phase 7 throttle system (iter 140.1) proves fractal pattern executable

**Validation:** Empirical threshold refinement based on observed outcomes

**Philosophy:** Parameters discovered through testing, not invented. Architecture mirrors reality.

---

**Document Status:** Approved for implementation (May 6, 2026)
**Next Action:** Lane-level evaluator scaffolding in Phase 2 planning (post iter 140.1)
**Owner:** Jason Aaron Meyer / Continental Haul Logistics
**Architectural Principle:** Fractal self-similarity in decision-making

DONE
