# Rate Calculator Scenarios -- 20260510_115327

**Run ID:** `cd17f7ab-403b-4b4a-a9d0-e7c50a914ffd`
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
- load_id: `e59d3170-33e6-40e9-a9b2-283d4ba11f1f`

### distance_100_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 245.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (245.0).
- load_id: `53412a9d-be62-47ab-901d-3219b9c7817b`

### distance_300_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 635.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (635.0).
- load_id: `fb8045e1-3b11-429f-8215-b1606774a38b`

### distance_800_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 1610.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (1610.0).
- load_id: `a3418835-4d27-4fd4-85da-2630cd5eb32b`

### distance_1500_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 2975.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (2975.0).
- load_id: `7dd2cf70-cefb-45a7-99ed-27a5f310cbd4`

### distance_2500_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 4925.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (4925.0).
- load_id: `c4865e8d-28a6-4587-90e3-98915d7ae677`

### equipment_dry_van (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=2.5 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 2.5, "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `715ef4ed-8716-4eb3-9161-ae92849d1c74`

### equipment_reefer (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=3.0 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 3.0, "cost_estimate_value": 1922.0}`
- cost_estimate matched (1922.0).
- load_id: `0ccd722b-17b4-466e-a920-aec35db2f77f`

### equipment_flatbed (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=2.8 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 2.8, "cost_estimate_value": 1805.0}`
- cost_estimate matched (1805.0).
- load_id: `ec95a7c2-93b7-4364-ad01-37b14b0f9e8a`

### equipment_step_deck (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=3.1 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 3.1, "cost_estimate_value": 1980.5}`
- cost_estimate matched (1980.5).
- load_id: `9a9c92aa-e596-4fd4-bd04-06318cf8ac44`

### equipment_tanker (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=3.25 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 3.25, "cost_estimate_value": 2078.0}`
- cost_estimate matched (2078.0).
- load_id: `84e21a72-1e56-43b0-9392-9a091fa72450`

### equipment_stepdeck (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=3.1 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 3.1, "cost_estimate_value": 1980.5}`
- cost_estimate matched (1980.5).
- load_id: `0dc72e1f-c8d7-4195-ab98-cc0ef77e8ae7`

### equipment_rgn (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=3.5 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 3.5, "cost_estimate_value": 2234.0}`
- cost_estimate matched (2234.0).
- load_id: `96300c40-866e-4b75-a96f-dbae1fbab23f`

### equipment_hotshot (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=2.85 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 2.85, "cost_estimate_value": 1824.5}`
- cost_estimate matched (1824.5).
- load_id: `14263902-8395-45c2-850c-e868ebd5a52d`

### equipment_power_only (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=1.95 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 1.95, "cost_estimate_value": 1259.0}`
- cost_estimate matched (1259.0).
- load_id: `9311c3e2-5bb4-4ae3-92f2-e25611291c91`

### commodity_general (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='Palletized general merchandise'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "Palletized general merchandise"}`
- cost_estimate matched (1610.0).
- load_id: `921329f0-0bb4-4b54-bd64-7f38afa9918b`

### commodity_hazmat_3 (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='HAZMAT class 3 (flammable liquids)'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "HAZMAT class 3 (flammable liquids)"}`
- cost_estimate matched (1610.0).
- load_id: `f0c60fe3-bbe9-47e6-9f4f-099b3fbda5ff`

### commodity_hazmat_8 (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='HAZMAT class 8 (corrosive)'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "HAZMAT class 8 (corrosive)"}`
- cost_estimate matched (1610.0).
- load_id: `8a96da6e-b78d-41ad-ba08-bd907662f582`

### commodity_oversized (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='Oversized industrial machinery'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "Oversized industrial machinery"}`
- cost_estimate matched (1610.0).
- load_id: `d10ce799-5121-430a-9206-d7b2ee524654`

### commodity_livestock (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='Livestock (live cattle)'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "Livestock (live cattle)"}`
- cost_estimate matched (1610.0).
- load_id: `d22a24b5-6bff-416b-a914-b9229358940e`

### commodity_hi_value (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='High-value electronics ($500k+)'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "High-value electronics ($500k+)"}`
- cost_estimate matched (1610.0).
- load_id: `c42c5940-d420-4ae3-ae1a-0a7f9b376cb1`

### commodity_produce (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='Fresh produce (mixed)'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "Fresh produce (mixed)"}`
- cost_estimate matched (1610.0).
- load_id: `19eaf830-e145-481f-b83a-14b79a42b8d4`

### special_team (special)  -- **PASS**

- **expectation:** special_requirements='Team driver required' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "Team driver required", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `f441dcfc-a29c-4656-85a4-7e909b6f071e`

### special_twic (special)  -- **PASS**

- **expectation:** special_requirements='TWIC card required' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "TWIC card required", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `baf212f4-de79-4494-9150-881a4e0653e2`

### special_hazmat_endorsed (special)  -- **PASS**

- **expectation:** special_requirements='HAZMAT endorsement required' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "HAZMAT endorsement required", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `81b43be3-35d9-4a1c-a838-d93e5e8736db`

### special_tarped (special)  -- **PASS**

- **expectation:** special_requirements='Tarped flatbed required' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "Tarped flatbed required", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `716bd814-6756-48d6-ad91-5ac456bfe773`

### special_live_load (special)  -- **PASS**

- **expectation:** special_requirements='Live load (no drop)' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "Live load (no drop)", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `1d89566e-381e-4598-b603-d6a2a4cc42e7`

### special_drop_hook (special)  -- **PASS**

- **expectation:** special_requirements='Drop and hook' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "Drop and hook", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `7372fc1d-7d11-4f16-ae88-965e7f0fc0a8`

### edge_zero_mile_local (edge)  -- **PASS**

- **expectation:** 0-mile local trip persists with miles_hint=1; cost_estimate calculated normally; no divide-by-zero.
- **predicted:** `{"cost_estimate_value": 245.0}`
- cost_estimate matched (245.0).
- load_id: `637a3cfd-1d06-4dd4-ab84-1e7a7e71784a`

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
- load_id: `613a9ece-9b7d-4644-9529-0de555e76dfe`

### margin_floor_soft_warn (accessorial)  -- **PASS**

- **expectation:** sell=2000, carrier=1750 -> margin=$250 (12.5%) -> meets $250 abs AND >= 5% pct (passes hard) but below 15% warn -> soft_warn.
- **predicted:** `{"margin_verdict": "soft_warn"}`
- margin verdict matched (soft_warn).
- load_id: `5a3bbd28-73f1-490c-a950-9c965f1fa1e4`

### margin_floor_clean (accessorial)  -- **PASS**

- **expectation:** sell=2000, carrier=1500 -> 25% margin -> clean.
- **predicted:** `{"margin_verdict": "clean"}`
- margin verdict matched (clean).
- load_id: `3482953d-a3c3-41f9-9dc3-bf55ec6e6ca0`

### detention_4hr_dwell (accessorial)  -- **PASS**

- **expectation:** 4hr dwell - 2hr free = 2hr billable @ $75/hr = $150 detention. Round-up 15-min increments (8 inc * $18.75 = $150).
- **predicted:** `{"detention_amount_usd": 150.0}`
- detention amount matched ($150.0).
- load_id: `65147cea-dc85-4b67-94ce-2662f8592e55`

### detention_under_free_time (accessorial)  -- **PASS**

- **expectation:** 1.5hr dwell < 2hr free -> 0 detention.
- **predicted:** `{"detention_amount_usd": 0.0}`
- detention amount matched ($0.0).
- load_id: `784c0d08-8cfc-419c-bd98-e548d3542baa`

### lumper_under_authorized (accessorial)  -- **PASS**

- **expectation:** Authorized $200, actual $175 -> -$25 variance (under_authorized).
- **predicted:** `{"has_variance": true}`
- No graded predictions; document-only.
- load_id: `8c87d9d2-c5b5-4c45-a3de-64fae843b661`

### tonu_authorized_match (accessorial)  -- **PASS**

- **expectation:** Authorized $150, actual $150 -> no variance.
- **predicted:** `{"has_variance": false}`
- No graded predictions; document-only.
- load_id: `ea865d0c-eddd-4fba-9b2f-8c0efe44f079`

---

Generated by `scripts/rate_calc_scenarios.py`. HTTP-only -- no direct imports of business logic. Cleanup: `--purge --yes`.