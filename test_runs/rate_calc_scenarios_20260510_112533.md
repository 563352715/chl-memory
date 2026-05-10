# Rate Calculator Scenarios -- 20260510_112533

**Run ID:** `62f10406-02d7-4e17-b42c-1e9ebed8cfb6`
**Total scenarios:** 45

## Grade tally
- **PASS**: 45
- **FAIL**: 0
- **OPEN**: 0
- **ERROR**: 0

## By category
- **accessorial**: PASS=7
- **commodity**: PASS=7
- **distance**: PASS=6
- **edge**: PASS=5
- **equipment**: PASS=9
- **rate_calc_only**: PASS=5
- **special**: PASS=6

## Per-scenario detail

### distance_0_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 245.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (245.0).
- load_id: `56f9b65a-c020-4a3d-81e2-13070b99d95c`

### distance_100_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 245.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (245.0).
- load_id: `ad5bc6b3-918e-403a-beda-ebd03de5f2b8`

### distance_300_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 635.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (635.0).
- load_id: `bd36f798-2583-4796-beb9-182847146f47`

### distance_800_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 1610.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (1610.0).
- load_id: `62e20272-53bc-4193-bba6-c13db161d166`

### distance_1500_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 2975.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (2975.0).
- load_id: `9cb3e4ed-ee72-4de9-82c7-6e4d1c94ae3d`

### distance_2500_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 4925.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (4925.0).
- load_id: `f3a4b96b-1a0f-474d-aedf-ed8191318626`

### equipment_dry_van (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=2.5 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 2.5, "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `a9bc3832-358a-447e-a9df-336808a4ea1b`

### equipment_reefer (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=3.0 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 3.0, "cost_estimate_value": 1922.0}`
- cost_estimate matched (1922.0).
- load_id: `5202b8a3-bd8d-4cb6-97c0-8fc553bd257b`

### equipment_flatbed (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=2.8 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 2.8, "cost_estimate_value": 1805.0}`
- cost_estimate matched (1805.0).
- load_id: `d97dd0a7-1abc-41ab-8e7e-d1f12fb6baf1`

### equipment_step_deck (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=3.1 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 3.1, "cost_estimate_value": 1980.5}`
- cost_estimate matched (1980.5).
- load_id: `12b2e735-5c55-4ee8-be17-462c178db582`

### equipment_tanker (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=3.25 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 3.25, "cost_estimate_value": 2078.0}`
- cost_estimate matched (2078.0).
- load_id: `15099c11-a5f8-4e5f-a41c-0b90ec377594`

### equipment_stepdeck (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=3.1 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 3.1, "cost_estimate_value": 1980.5}`
- cost_estimate matched (1980.5).
- load_id: `33fce190-867f-4322-89af-6b21a293a561`

### equipment_rgn (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=3.5 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 3.5, "cost_estimate_value": 2234.0}`
- cost_estimate matched (2234.0).
- load_id: `95df43dc-b501-4226-8917-f83b94269fde`

### equipment_hotshot (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=2.85 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 2.85, "cost_estimate_value": 1824.5}`
- cost_estimate matched (1824.5).
- load_id: `24a51ef0-b2ea-4164-bd55-acf40a539433`

### equipment_power_only (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=1.95 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 1.95, "cost_estimate_value": 1259.0}`
- cost_estimate matched (1259.0).
- load_id: `51af3520-dd69-4709-a247-977a43917631`

### commodity_general (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='Palletized general merchandise'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "Palletized general merchandise"}`
- cost_estimate matched (1610.0).
- load_id: `6960b50f-a6f2-4c28-9636-7b252f986936`

### commodity_hazmat_3 (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='HAZMAT class 3 (flammable liquids)'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "HAZMAT class 3 (flammable liquids)"}`
- cost_estimate matched (1610.0).
- load_id: `b86399bd-0abc-4e54-b5a5-2e6f5c31649f`

### commodity_hazmat_8 (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='HAZMAT class 8 (corrosive)'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "HAZMAT class 8 (corrosive)"}`
- cost_estimate matched (1610.0).
- load_id: `1fcca7aa-5caf-4c3c-b88d-52707751019f`

### commodity_oversized (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='Oversized industrial machinery'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "Oversized industrial machinery"}`
- cost_estimate matched (1610.0).
- load_id: `10d23401-fdeb-4ef9-9982-d82d86df7fa0`

### commodity_livestock (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='Livestock (live cattle)'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "Livestock (live cattle)"}`
- cost_estimate matched (1610.0).
- load_id: `4883d08c-5f8b-4356-800b-4ca97ecde370`

### commodity_hi_value (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='High-value electronics ($500k+)'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "High-value electronics ($500k+)"}`
- cost_estimate matched (1610.0).
- load_id: `df003e26-c0f4-44fa-9638-096fdadec45a`

### commodity_produce (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='Fresh produce (mixed)'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "Fresh produce (mixed)"}`
- cost_estimate matched (1610.0).
- load_id: `babf2d76-f0af-49dd-871b-74024f3fa37d`

### special_team (special)  -- **PASS**

- **expectation:** special_requirements='Team driver required' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "Team driver required", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `299d57fe-23fe-4f07-b8ee-3f6b23332dac`

### special_twic (special)  -- **PASS**

- **expectation:** special_requirements='TWIC card required' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "TWIC card required", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `a4a9abf9-deae-446c-8abe-3b9bb04c795d`

### special_hazmat_endorsed (special)  -- **PASS**

- **expectation:** special_requirements='HAZMAT endorsement required' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "HAZMAT endorsement required", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `4af28d02-d9e0-4189-b117-6e33871f74ba`

### special_tarped (special)  -- **PASS**

- **expectation:** special_requirements='Tarped flatbed required' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "Tarped flatbed required", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `c17fcf5b-9f54-46bf-92ba-4becc91a5610`

### special_live_load (special)  -- **PASS**

- **expectation:** special_requirements='Live load (no drop)' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "Live load (no drop)", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `35a0ee40-05c0-4a1a-8a66-156323da231a`

### special_drop_hook (special)  -- **PASS**

- **expectation:** special_requirements='Drop and hook' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "Drop and hook", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `2861506e-26ea-4538-9890-e5f6d45557e9`

### edge_zero_mile_local (edge)  -- **PASS**

- **expectation:** 0-mile local trip persists with miles_hint=1; cost_estimate calculated normally; no divide-by-zero.
- **predicted:** `{"cost_estimate_value": 245.0}`
- cost_estimate matched (245.0).
- load_id: `59e338ba-c45f-421f-bfc4-d19b46f3fe98`

### edge_negative_weight (edge)  -- **PASS**

- **expectation:** Negative weight rejected by LoadCreate model_validator (weight must be > 0).
- **predicted:** `{"should_reject": true, "expected_status": 422}`
- Predicted reject status 422 matched.
- **finding:** `{"kind": "load_create_http_error", "status": 422, "body": "{\"detail\":[{\"type\":\"value_error\",\"loc\":[\"body\"],\"msg\":\"Value error, weight must be > 0 (got -5000)\",\"input\":{\"origin_city\":\"Dallas\",\"origin_state\":\"TX\",\"origin_lat\":32.7767,\"origin_lng\":-96.797,\"destination_city\`

### edge_negative_rate (edge)  -- **PASS**

- **expectation:** Negative rate rejected by LoadCreate model_validator (rate must be > 0; sovereign_override required to bypass).
- **predicted:** `{"should_reject": true, "expected_status": 422}`
- Predicted reject status 422 matched.
- **finding:** `{"kind": "load_create_http_error", "status": 422, "body": "{\"detail\":[{\"type\":\"value_error\",\"loc\":[\"body\"],\"msg\":\"Value error, rate must be > 0 (got -500.0). Set sovereign_override=true for $0 or negative-rate loads.\",\"input\":{\"origin_city\":\"Dallas\",\"origin_state\":\"TX\",\"orig`

### edge_missing_weight (edge)  -- **PASS**

- **expectation:** Missing weight field -> Pydantic 422 (weight: int required).
- **predicted:** `{"should_reject": true, "expected_status": 422}`
- Predicted reject status 422 matched.
- **finding:** `{"kind": "load_create_http_error", "status": 422, "body": "{\"detail\":[{\"type\":\"missing\",\"loc\":[\"body\",\"weight\"],\"msg\":\"Field required\",\"input\":{\"origin_city\":\"Dallas\",\"origin_state\":\"TX\",\"origin_lat\":32.7767,\"origin_lng\":-96.797,\"destination_city\":\"Springfield\",\"de`

### edge_missing_rate (edge)  -- **PASS**

- **expectation:** Missing rate -> Pydantic 422.
- **predicted:** `{"should_reject": true, "expected_status": 422}`
- Predicted reject status 422 matched.
- **finding:** `{"kind": "load_create_http_error", "status": 422, "body": "{\"detail\":[{\"type\":\"missing\",\"loc\":[\"body\",\"rate\"],\"msg\":\"Field required\",\"input\":{\"origin_city\":\"Dallas\",\"origin_state\":\"TX\",\"origin_lat\":32.7767,\"origin_lng\":-96.797,\"destination_city\":\"Springfield\",\"dest`

### ratecalc_known_lane_TX_CA (rate_calc_only)  -- **PASS**

- **expectation:** Hard-coded TX-CA = 1400mi; dry_van base RPM 2.50; weight 25000 -> factor 1.0; total = 1400*2.50 = 3500; market = total*1.12 (Model C default) = 3920.
- **predicted:** `{"estimated_distance_miles": 1400, "base_rate_per_mile": 2.5, "weight_factor": 1.0, "total_rate": 3500.0, "market_rate": 3920.0}`

### ratecalc_unknown_lane_default (rate_calc_only)  -- **PASS**

- **expectation:** Unknown lane -> 500mi default; reefer base 3.00.
- **predicted:** `{"estimated_distance_miles": 500, "base_rate_per_mile": 3.0, "weight_factor": 1.0, "total_rate": 1500.0}`

### ratecalc_weight_factor_40000 (rate_calc_only)  -- **PASS**

- **expectation:** weight=40000 -> factor=1.0. Note: tiers use strict-greater so 40000 stays at 1.0 and 45000 stays at 1.10. Possible bug: 45001 falls through to 1.10 because elif checked > 45000 AFTER 40000 branch already returned 1.10.
- **predicted:** `{"estimated_distance_miles": 800, "weight_factor": 1.0, "adjusted_rate_per_mile": 2.5}`

### ratecalc_weight_factor_40001 (rate_calc_only)  -- **PASS**

- **expectation:** weight=40001 -> factor=1.1. Note: tiers use strict-greater so 40000 stays at 1.0 and 45000 stays at 1.10. Possible bug: 45001 falls through to 1.10 because elif checked > 45000 AFTER 40000 branch already returned 1.10.
- **predicted:** `{"estimated_distance_miles": 800, "weight_factor": 1.1, "adjusted_rate_per_mile": 2.75}`

### ratecalc_weight_factor_45001 (rate_calc_only)  -- **PASS**

- **expectation:** weight=45001 -> factor=1.15. Note: tiers use strict-greater so 40000 stays at 1.0 and 45000 stays at 1.10. Possible bug: 45001 falls through to 1.10 because elif checked > 45000 AFTER 40000 branch already returned 1.10.
- **predicted:** `{"estimated_distance_miles": 800, "weight_factor": 1.15, "adjusted_rate_per_mile": 2.88}`

### margin_floor_hard_block (accessorial)  -- **PASS**

- **expectation:** sell=2000, carrier=1980 -> margin=$20 (1%) -> below $250 floor AND below 5% pct -> hard_block.
- **predicted:** `{"margin_verdict": "hard_block"}`
- margin verdict matched (hard_block).
- load_id: `64d96674-394e-4afd-9aaa-be8ca7873836`

### margin_floor_soft_warn (accessorial)  -- **PASS**

- **expectation:** sell=2000, carrier=1750 -> margin=$250 (12.5%) -> meets $250 abs AND >= 5% pct (passes hard) but below 15% warn -> soft_warn.
- **predicted:** `{"margin_verdict": "soft_warn"}`
- margin verdict matched (soft_warn).
- load_id: `5937d516-2dfd-4cca-a93c-147b7a3d24db`

### margin_floor_clean (accessorial)  -- **PASS**

- **expectation:** sell=2000, carrier=1500 -> 25% margin -> clean.
- **predicted:** `{"margin_verdict": "clean"}`
- margin verdict matched (clean).
- load_id: `e7b0e8b6-6ce4-4e61-90dd-b26cadafc23f`

### detention_4hr_dwell (accessorial)  -- **PASS**

- **expectation:** 4hr dwell - 2hr free = 2hr billable @ $75/hr = $150 detention. Round-up 15-min increments (8 inc * $18.75 = $150).
- **predicted:** `{"detention_amount_usd": 150.0}`
- detention amount matched ($150.0).
- load_id: `12a0d856-da2e-483a-b0c4-913be0871bcc`

### detention_under_free_time (accessorial)  -- **PASS**

- **expectation:** 1.5hr dwell < 2hr free -> 0 detention.
- **predicted:** `{"detention_amount_usd": 0.0}`
- detention amount matched ($0.0).
- load_id: `99a130ad-3eae-4d2e-8951-6fee858a0662`

### lumper_under_authorized (accessorial)  -- **PASS**

- **expectation:** Authorized $200, actual $175 -> -$25 variance (under_authorized).
- **predicted:** `{"has_variance": true}`
- No graded predictions; document-only.
- load_id: `86d85101-6840-45e1-872c-1cf40e3504d9`

### tonu_authorized_match (accessorial)  -- **PASS**

- **expectation:** Authorized $150, actual $150 -> no variance.
- **predicted:** `{"has_variance": false}`
- No graded predictions; document-only.
- load_id: `81497660-4611-40c6-96b7-7ec36b5a809b`

---

Generated by `scripts/rate_calc_scenarios.py`. HTTP-only -- no direct imports of business logic. Cleanup: `--purge --yes`.