# Rate Calculator Scenarios -- 20260510_115536

**Run ID:** `e4068536-88c7-4a6f-ba56-7b8f19d6d527`
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
- load_id: `b98d558a-6a68-4630-9742-df830a737baa`

### distance_100_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 245.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (245.0).
- load_id: `a3b96a17-3522-4c6d-bb1c-d5a00a147c40`

### distance_300_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 635.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (635.0).
- load_id: `5fe6a14e-f18e-4b84-8801-691caccf4740`

### distance_800_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 1610.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (1610.0).
- load_id: `90c0b4bc-22dc-443b-a8ce-4a66d54b5724`

### distance_1500_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 2975.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (2975.0).
- load_id: `ecc57407-80d1-48c8-ab05-4f814653cff2`

### distance_2500_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 4925.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (4925.0).
- load_id: `c3673d11-aaba-4c11-9cfc-6fe5be1527da`

### equipment_dry_van (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=2.5 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 2.5, "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `3eff1aed-84b0-4e33-8567-2c0db282a4f5`

### equipment_reefer (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=3.0 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 3.0, "cost_estimate_value": 1922.0}`
- cost_estimate matched (1922.0).
- load_id: `cd5b9e15-a888-4f36-8754-ad4115f7a431`

### equipment_flatbed (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=2.8 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 2.8, "cost_estimate_value": 1805.0}`
- cost_estimate matched (1805.0).
- load_id: `bc3d83ee-7a75-4e70-adab-61282d7778ca`

### equipment_step_deck (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=3.1 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 3.1, "cost_estimate_value": 1980.5}`
- cost_estimate matched (1980.5).
- load_id: `cb99ff3e-75dd-4389-a5e1-df4b4ea7c667`

### equipment_tanker (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=3.25 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 3.25, "cost_estimate_value": 2078.0}`
- cost_estimate matched (2078.0).
- load_id: `aa8c3d00-5d84-461d-86af-b2fcc6b165cd`

### equipment_stepdeck (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=3.1 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 3.1, "cost_estimate_value": 1980.5}`
- cost_estimate matched (1980.5).
- load_id: `50b7f4de-38c7-47a2-ae59-0ab204a04a6a`

### equipment_rgn (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=3.5 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 3.5, "cost_estimate_value": 2234.0}`
- cost_estimate matched (2234.0).
- load_id: `2af56305-d442-4b32-a780-43e9844bbc38`

### equipment_hotshot (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=2.85 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 2.85, "cost_estimate_value": 1824.5}`
- cost_estimate matched (1824.5).
- load_id: `470151c8-9c72-436e-8053-acabdac787ea`

### equipment_power_only (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=1.95 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 1.95, "cost_estimate_value": 1259.0}`
- cost_estimate matched (1259.0).
- load_id: `bcf0aa10-fc1d-4a0b-918a-499f407392d5`

### commodity_general (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='Palletized general merchandise'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "Palletized general merchandise"}`
- cost_estimate matched (1610.0).
- load_id: `5cbbf066-6bbc-49df-b717-5b40e3dd8950`

### commodity_hazmat_3 (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='HAZMAT class 3 (flammable liquids)'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "HAZMAT class 3 (flammable liquids)"}`
- cost_estimate matched (1610.0).
- load_id: `44c8b2c0-6ec3-4d30-87f0-783b082ac117`

### commodity_hazmat_8 (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='HAZMAT class 8 (corrosive)'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "HAZMAT class 8 (corrosive)"}`
- cost_estimate matched (1610.0).
- load_id: `1351da32-12c7-4b84-a661-a0f7181360cf`

### commodity_oversized (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='Oversized industrial machinery'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "Oversized industrial machinery"}`
- cost_estimate matched (1610.0).
- load_id: `32e222b0-f140-48ff-a7e8-08b8351d814b`

### commodity_livestock (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='Livestock (live cattle)'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "Livestock (live cattle)"}`
- cost_estimate matched (1610.0).
- load_id: `20766d7d-04df-4199-8cde-6da5a1df69be`

### commodity_hi_value (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='High-value electronics ($500k+)'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "High-value electronics ($500k+)"}`
- cost_estimate matched (1610.0).
- load_id: `8798d2d1-8a07-42c0-8fda-370af2b92bbf`

### commodity_produce (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='Fresh produce (mixed)'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "Fresh produce (mixed)"}`
- cost_estimate matched (1610.0).
- load_id: `6335b2c3-fc7d-4ba4-85c7-768044fa5eb4`

### special_team (special)  -- **PASS**

- **expectation:** special_requirements='Team driver required' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "Team driver required", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `93936171-9119-44f1-b152-288a02b92925`

### special_twic (special)  -- **PASS**

- **expectation:** special_requirements='TWIC card required' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "TWIC card required", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `77c43d29-9067-4cf5-b2bd-fd3da5b81f71`

### special_hazmat_endorsed (special)  -- **PASS**

- **expectation:** special_requirements='HAZMAT endorsement required' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "HAZMAT endorsement required", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `f392b62a-3bcd-4c09-886f-90a8b26c58d7`

### special_tarped (special)  -- **PASS**

- **expectation:** special_requirements='Tarped flatbed required' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "Tarped flatbed required", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `02393570-695d-4855-808f-cff10b815a6b`

### special_live_load (special)  -- **PASS**

- **expectation:** special_requirements='Live load (no drop)' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "Live load (no drop)", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `bef5c403-7271-4487-a1a6-d6b3088d9e65`

### special_drop_hook (special)  -- **PASS**

- **expectation:** special_requirements='Drop and hook' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "Drop and hook", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `63d361c2-4d21-4aae-8921-7ec3f2adf02b`

### edge_zero_mile_local (edge)  -- **PASS**

- **expectation:** 0-mile local trip persists with miles_hint=1; cost_estimate calculated normally; no divide-by-zero.
- **predicted:** `{"cost_estimate_value": 245.0}`
- cost_estimate matched (245.0).
- load_id: `1a891d51-add6-4540-b3ab-6714a9c01b3d`

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
- load_id: `b40eb1ef-bb50-47c0-bf9c-ea7d5c5a1001`

### margin_floor_soft_warn (accessorial)  -- **PASS**

- **expectation:** sell=2000, carrier=1750 -> margin=$250 (12.5%) -> meets $250 abs AND >= 5% pct (passes hard) but below 15% warn -> soft_warn.
- **predicted:** `{"margin_verdict": "soft_warn"}`
- margin verdict matched (soft_warn).
- load_id: `f3161c0e-57fb-4543-8a7b-32aa40a61dd7`

### margin_floor_clean (accessorial)  -- **PASS**

- **expectation:** sell=2000, carrier=1500 -> 25% margin -> clean.
- **predicted:** `{"margin_verdict": "clean"}`
- margin verdict matched (clean).
- load_id: `2b17b30b-d5e7-406f-9ddb-1fff7c9eced7`

### detention_4hr_dwell (accessorial)  -- **PASS**

- **expectation:** 4hr dwell - 2hr free = 2hr billable @ $75/hr = $150 detention. Round-up 15-min increments (8 inc * $18.75 = $150).
- **predicted:** `{"detention_amount_usd": 150.0}`
- detention amount matched ($150.0).
- load_id: `2a09add3-9e01-4d3e-bbc5-606fb9a19325`

### detention_under_free_time (accessorial)  -- **PASS**

- **expectation:** 1.5hr dwell < 2hr free -> 0 detention.
- **predicted:** `{"detention_amount_usd": 0.0}`
- detention amount matched ($0.0).
- load_id: `c255a1ad-4a3a-4604-831e-cf7c0b729e40`

### lumper_under_authorized (accessorial)  -- **PASS**

- **expectation:** Authorized $200, actual $175 -> -$25 variance (under_authorized).
- **predicted:** `{"has_variance": true}`
- No graded predictions; document-only.
- load_id: `2dccab65-7605-4bb2-b6f8-d496f670b3dc`

### tonu_authorized_match (accessorial)  -- **PASS**

- **expectation:** Authorized $150, actual $150 -> no variance.
- **predicted:** `{"has_variance": false}`
- No graded predictions; document-only.
- load_id: `71c4b296-30bf-49b4-a265-c7a0d0640cb4`

---

Generated by `scripts/rate_calc_scenarios.py`. HTTP-only -- no direct imports of business logic. Cleanup: `--purge --yes`.