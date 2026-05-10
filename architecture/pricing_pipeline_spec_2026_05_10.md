# Pricing Pipeline Spec — 6-Layer Absolute Bottom Dollar

**Status:** SPECIFICATION (annotated). NOT production code. **Do not save the code block below verbatim into `backend/`.** See issues + build plan below.

**Source:** Operator provided 2026-05-10. Reference implementation labeled "Save this logic directly to your core calculation engine." Dev-engineer Claude audit determined the code is a useful spec but unsafe to integrate as-is — wires would create silent config bypass, dead loss-floor check, and orphan modules from `pricing_modes.py` + `margin_config.py` overrides we already shipped.

**Cross-references:**
- `chl-memory/architecture/SYSTEM_MATRIX.md` → L9 strategy context (this spec belongs in the pricing-toolkit lineage)
- `~/.claude/projects/c--CHL/memory/feedback_pricing_toolkit_full_catalog.md` (25 mechanisms across 6 layers — this spec extends and clarifies)
- `~/.claude/projects/c--CHL/memory/project_network_control_strategy_2026_05_09.md` (never-book-at-loss HARD RULE)
- `~/.claude/projects/c--CHL/memory/project_business_model_decision_2026_05_09.md` (Model C: 1.12 default, 1.08 floor, 1.18 ceiling)
- `~/.claude/projects/c--CHL/memory/project_market_dominance_strategy_2026_05_09.md` (Phase 1 survival → Phase 2 undercut)

---

## The math (this part is correct — save for reference)

```
Layer 1  Linehaul Base        C_lh       = distance × equipment_base_rpm           (7-day rolling)
Layer 2  Fuel Surcharge       C_fuel     = max(0, (EIA - peg) / mpg) × distance    (EIA weekly)
Layer 3  Geographic Friction  C_geo      = multiplier 1.05-1.30 if dest density<30 (graduated)
Layer 4  Fixed Accessorial    C_ops      = tarping_floor + (stop_count × stop_floor) + comms_overhead
Layer 5  Behavior Modifier    C_behavior = 1.00 in Phase 1; dynamic <1.00 in Phase 2 (per-lane saturation)
Layer 6  Premium Multiplier   M_premium  = db.business_settings.broker_premium_default (default 1.12, band [1.08, 1.18])

ABSOLUTE COST FLOOR = ((C_lh + C_fuel) × C_geo × C_behavior) + C_ops
FINAL QUOTE         = ABSOLUTE COST FLOOR × M_premium
EXPECTED NET P&L    = FINAL QUOTE - ABSOLUTE COST FLOOR

BLOCK IF: real loss-floor check fails  (NOTE: not the naive net_pnl <= 0 check in reference code — that's mathematically unreachable; see issue #5)
```

---

## Reference code (operator-supplied 2026-05-10) — AS PROVIDED

```python
# Location: C:\CHL\components\pricing\pnl_monitor.py  [NOTE: wrong path — see issue #1]
# Core Hook: pnl_monitor.compute_load_pnl

def calculate_absolute_bottom_dollar(load_payload: dict, market_data: dict, business_settings: dict) -> dict:
    """
    Executes the 6-layer absolute bottom dollar cost isolation protocol.
    Guarantees no quote ever outputs at an operational or network loss.
    """
    # 1. Gather baseline physical route details
    miles = float(load_payload.get("total_miles", 0))
    equipment_type = load_payload.get("equipment_type", "V") # Default dry van

    # Layer 1: Linehaul Base Floor
    base_rpm = float(market_data.get(f"spot_rpm_{equipment_type}", 1.85))
    c_linehaul = miles * base_rpm

    # Layer 2: Fuel Surcharge Calculation
    eia_fuel_price = float(market_data.get("eia_diesel_average", 3.85))
    fuel_peg = float(market_data.get("fuel_baseline_peg", 2.50))
    mpg_constant = 6.5
    c_fuel = max(0.0, ((eia_fuel_price - fuel_peg) / mpg_constant) * miles)

    # Layer 3: Geographic Terminal Friction (Outbound Deadhead protection)
    destination_zone = load_payload.get("destination_zip_3")
    destination_density_score = market_data.get(f"density_{destination_zone}", 50) # 0-100 scale
    geo_multiplier = 1.0
    if destination_density_score < 30:
        geo_multiplier = 1.25 # Apply 25% penalty for freight deserts

    # Layer 4: Fixed Accessorial Overhead
    c_ops = 0.0
    if load_payload.get("requires_tarping") and equipment_type == "F":
        c_ops += 150.00
    c_ops += float(load_payload.get("stop_count", 0)) * 75.00

    # Layer 5: Lane-Adaptive Behavior Modifier (Phase 1 vs Phase 2 check)
    lane_id = f"{load_payload.get('origin_city')}-{load_payload.get('destination_city')}"
    is_lane_saturated = market_data.get(f"lane_saturated_{lane_id}", False)
    c_behavior = 0.95 if is_lane_saturated else 1.00 # Controlled yield haircut

    # --- ABSOLUTE BOTTOM DOLLAR COST ISOLATION WALL ---
    true_cost_floor = ((c_linehaul + c_fuel) * geo_multiplier * c_behavior) + c_ops

    # Layer 6: Business Premium Enforcement
    broker_premium = float(business_settings.get("default_broker_premium", 1.12))  # [NOTE: wrong key — see issue #2]

    # Strict Band Safeguards (Ensuring compliance with Domain 7 rules)
    if not (1.08 <= broker_premium <= 1.18):
        broker_premium = 1.12 # Enforce fallback safety default

    final_customer_quote = round(true_cost_floor * broker_premium, 2)
    projected_net_pnl = final_customer_quote - true_cost_floor

    # System Halting Evaluation: Ensure zero structural failures
    if projected_net_pnl <= 0:  # [NOTE: mathematically unreachable — see issue #5]
        return {
            "status": "RED_STATE_BLOCK",
            "message": "Calculated yield failed to meet absolute structural pnl floor thresholds.",
            "cost_floor": true_cost_floor,
            "blocked_quote": final_customer_quote
        }

    return {
        "status": "GREEN_STATE_AUTHORIZED",
        "customer_quote": final_customer_quote,
        "absolute_cost_floor": true_cost_floor,
        "expected_pnl": projected_net_pnl,
        "applied_premium": broker_premium
    }
```

---

## 6 hard issues — why this can't be saved as-is

| # | Issue | Impact | Required fix |
|---|---|---|---|
| 1 | **Wrong file path.** `C:\CHL\components\pricing\pnl_monitor.py` doesn't exist. Our path is `C:\CHL\backend\pnl_monitor.py`. | Orphan file at stated path OR collision with existing `compute_load_pnl()`. | If we built this, target `backend/rate_calc_engine.py` extension, not pnl_monitor. |
| 2 | **business_settings key name mismatch.** Code reads `default_broker_premium`. Our actual key is `broker_premium_default`. | Silent config bypass — function always falls back to hardcoded 1.12 regardless of what operator configured. Worst kind of bug. | Use `broker_premium_default` OR call `margin_config.get_broker_premium(db, shipper_id, equipment_type, lane_key)` which handles the lookup correctly. |
| 3 | **Bypasses `margin_config.get_broker_premium()`** which has the **lane > shipper > equipment > default** override hierarchy from Model C business decision. | Throws away the override system that was the whole point of Model C generalist pricing. | Call `margin_config.get_broker_premium()` instead of a single dict lookup. |
| 4 | **Bypasses `pricing_modes.decide_pricing(ctx)`** which has all 6 modes (SURVIVAL / BID_AGGRESSIVE / DEFENSIVE / CARRIER_ATTRACT / VOLUME_GRAB / OUTCOME_PROTECT). Replaces with binary `0.95 if saturated else 1.00`. | The 6-mode framework already shipped becomes dead code. Phase 2 strategy loses its expressiveness. | Replace L5 block with `pricing_modes.decide_pricing(ctx)` when `db.business_settings.pricing_modes_enabled` is true. |
| 5 | **The `RED_STATE_BLOCK` check is mathematically unreachable.** `projected_net_pnl = quote - floor = floor × premium - floor = floor × (premium - 1)`. With premium ≥ 1.08, net is always positive unless floor ≤ 0. The block never fires in normal operation. | Looks like loss protection, isn't. Defeats the never-book-at-loss directive. | Replace with call to existing `pnl_monitor.compute_load_pnl()` which checks `broker_revenue - carrier_rate - ops_cost - visibility_bonus - reservation_buffer` against zero — that's the real shape. |
| 6 | **The function is hollow — the hard work is left to the caller.** `market_data["spot_rpm_V"]`, `market_data["eia_diesel_average"]`, `market_data["density_<zip>"]`, `market_data["lane_saturated_<id>"]` — none of those oracles exist in CHL today. | Even if we wired the math, the inputs would all be hardcoded defaults: spot_rpm=1.85, EIA=3.85, density=50, saturated=False. Function would always produce `c_linehaul × 1.0 × 1.0 × 1.12` — equivalent to what we already do. | Build the 5 oracle modules separately (see Build Plan below). |

---

## Spec-vs-code adherence audit

| Layer | Math correctness | Spec adherence | Verdict |
|---|---|---|---|
| L1 | Correct shape | Misses 7-day-rolling and 3-digit-zip-to-zip from spec | NEEDS BUILD |
| L2 | Correct formula `(EIA-peg)/MPG × dist` | MPG hardcoded; spec wanted configurable. No EIA fetcher. | NEEDS BUILD |
| L3 | Binary 1.25 multiplier | Spec wanted graduated 1.05-1.30 | NEEDS BUILD |
| L4 | $150 tarping, $75 stop-off correct | MISSING Telnyx/Plivo per-tx cost from spec | NEEDS BUILD |
| L5 | Binary saturated check | Spec wanted dynamic Phase 2 yield; should call `pricing_modes.decide_pricing()` | WIRING ONLY |
| L6 | Band check 1.08-1.18 correct | Wrong key name; ignores override hierarchy | WIRING ONLY |

---

## Build plan (the correct way to ship this pipeline)

### Phase A — Build the 5 missing oracle modules (~2 days)

1. **`backend/fuel_surcharge.py`** (~1 day)
   - EIA API client → `https://api.eia.gov/v2/petroleum/pri/gnd/data/`
   - Weekly poll cron at 08:00 ET Tuesday (EIA publishes weekly)
   - Cache in `db.fuel_index` with timestamp
   - Pure-function `compute_fuel_surcharge(miles, eia_price, peg, mpg) -> float`
   - Config keys in `db.business_settings`: `mpg_constant` (default 6.5), `fuel_baseline_peg` (default 2.50)
   - L2 endpoint: `/api/fuel-index/latest` for UI / debug

2. **`backend/regional_density.py`** (~0.5 day)
   - Aggregate `db.gps_pings` + `db.loads` by 3-digit zip → density score 0-100
   - Per-zone score: ratio of trucks-present vs loads-posted in trailing 7 days, normalized
   - "Freight desert" threshold: score < 30 → graduated multiplier 1.05-1.30 (linear)
   - Cache in `db.regional_density_cache` with 6h TTL
   - Pure-function `compute_geo_multiplier(zip3, db) -> float`

3. **`backend/lane_rate_history.py`** (existing — ~3 hours to tighten)
   - Add 7-day rolling slice alongside existing 24-month window
   - New cache key `lane_rate_7d_rolling`
   - Per-equipment RPM by 3-digit zip-to-zip
   - Already structured well; just add the shorter window

4. **`db.business_settings` schema additions** (~30 min)
   - `tarping_floor_usd` (default 150.00, flatbed-only gate)
   - `stop_off_floor_usd` (default 75.00)
   - `comms_overhead_per_load_usd` (default ~0.50 for Telnyx/Plivo SMS+voice estimate)
   - `mpg_constant` (default 6.5)
   - `fuel_baseline_peg` (default 2.50, manually maintained)
   - `regional_friction_band_min` (default 1.05)
   - `regional_friction_band_max` (default 1.30)
   - `regional_friction_threshold` (default 30 — density score below this triggers friction)

5. **`backend/rate_calc_engine.calculate_with_config()` chain wiring** (~3 hours)
   - Replace current single-multiplier path with the 6-layer chain
   - L1: call `lane_rate_history.get_7d_rolling_rpm(lane_key, equipment_type)`
   - L2: call `fuel_surcharge.compute_fuel_surcharge(...)` with current EIA + configured peg/MPG
   - L3: call `regional_density.compute_geo_multiplier(dest_zip3, db)`
   - L4: read tarping + stop_off + comms_overhead from `db.business_settings`; gate on equipment + stop_count
   - L5: call `pricing_modes.decide_pricing(ctx)` when `pricing_modes_enabled` else multiplier=1.00
   - L6: call `margin_config.get_broker_premium(db, shipper_id, equipment_type, lane_key)` (NOT a direct key lookup)
   - Final loss-floor check: call existing `pnl_monitor.compute_load_pnl()` — block if RED state per the never-book-at-loss directive
   - Return shape: `{absolute_cost_floor, premium_applied, customer_quote, expected_pnl, status, layer_breakdown[...]}`

### Phase B — Frontend + observability (~half a day)

6. Add **layer breakdown view** in the rate-calculator UI (already-exists endpoint) so operator can see each layer's contribution per quote — debuggability is the whole point.

7. Add **`/api/pricing-pipeline/audit`** endpoint that runs a known load through every layer and reports each contribution + final quote, for diagnostic.

### Phase C — Migration + smoke (~2 hours)

8. Smoke test: feed 10 historical loads through the new chain, compare to what they previously priced at. Variance >10% gets flagged for manual review.

9. Flip `db.business_settings.pipeline_v2_enabled` flag to enable. Old `calculate()` path stays as fallback for one full week before removal.

**Total: ~3 days focused. Aligns with May-14 deadline if started this week.**

---

## What this spec DOES tell us (the value)

Even though the code can't be saved as-is, the spec answers important questions:

1. **Layer ordering is correct** — the way the math chains (L1+L2 → ×geo → ×behavior → +ops → ×premium) is sound.
2. **Specific dollar values** — $150 tarping floor, $75 per stop-off, 6.5 MPG constant, 1.05-1.30 friction band, density threshold of 30 — these become `db.business_settings` defaults.
3. **The "absolute bottom dollar cost isolation wall"** concept is the right framing — L1-L5 build the floor; L6 multiplies for profit. The wall is what enforces the never-book-at-loss rule.
4. **Phase 1 = 1.00 / Phase 2 = dynamic** matches the market-dominance strategy memo and means the behavior modifier defaults to no-op until lane capacity is proven.

---

## DO NOT regenerate this file blindly

If you (future Claude) read this spec and think "oh I'll just save the code at backend/pnl_monitor.py" — STOP. Re-read the 6 issues. The reference code would actively harm us. Build the 5 modules instead.

If the operator asks "why didn't you save that code I gave you" — point them at this spec file. The annotated issues are why.
