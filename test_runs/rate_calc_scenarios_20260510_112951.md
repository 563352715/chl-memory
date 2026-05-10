# Rate Calculator Scenarios -- 20260510_112951

**Run ID:** `d67da7c4-d25f-4213-b81b-29b826371d94`
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
- load_id: `f051aa10-b5f0-48c3-86b6-e64b121265c4`

### distance_100_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 245.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (245.0).
- load_id: `12b80ad3-8200-4fd2-b6b4-0eb39df8b75b`

### distance_300_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 635.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (635.0).
- load_id: `34c67d7f-a8a1-4a32-9943-aeb02327436c`

### distance_800_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 1610.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (1610.0).
- load_id: `c2bd46fe-e7be-4e03-bc3d-0d930d243ae4`

### distance_1500_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 2975.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (2975.0).
- load_id: `27f13688-5e6e-40fd-bf5a-cb713de24495`

### distance_2500_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 4925.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (4925.0).
- load_id: `35550e32-0676-42a7-909e-818e6d8e26d6`

### equipment_dry_van (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=2.5 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 2.5, "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `1d01538b-8042-4234-96d7-200519c01bd6`

### equipment_reefer (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=3.0 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 3.0, "cost_estimate_value": 1922.0}`
- cost_estimate matched (1922.0).
- load_id: `89f7b75d-1108-4d83-aa02-bfdec213c114`

### equipment_flatbed (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=2.8 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 2.8, "cost_estimate_value": 1805.0}`
- cost_estimate matched (1805.0).
- load_id: `779d5103-19b5-4c53-8fd6-507fd933cdea`

### equipment_step_deck (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=3.1 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 3.1, "cost_estimate_value": 1980.5}`
- cost_estimate matched (1980.5).
- load_id: `d868d788-ce5d-4aa4-b540-eebe5f8e2046`

### equipment_tanker (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=3.25 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 3.25, "cost_estimate_value": 2078.0}`
- cost_estimate matched (2078.0).
- load_id: `5147a9b3-df66-48f2-be7e-eca01ddadb0b`

### equipment_stepdeck (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=3.1 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 3.1, "cost_estimate_value": 1980.5}`
- cost_estimate matched (1980.5).
- load_id: `46d4d358-9233-4ea6-b0ea-77f591790522`

### equipment_rgn (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=3.5 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 3.5, "cost_estimate_value": 2234.0}`
- cost_estimate matched (2234.0).
- load_id: `7a99de4d-808f-441d-aa9b-85e212704579`

### equipment_hotshot (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=2.85 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 2.85, "cost_estimate_value": 1824.5}`
- cost_estimate matched (1824.5).
- load_id: `86c231c9-d1a5-40c9-bb1b-e8cc03fb09b6`

### equipment_power_only (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=1.95 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 1.95, "cost_estimate_value": 1259.0}`
- cost_estimate matched (1259.0).
- load_id: `b77677eb-9b35-41f6-99b1-afd0d53655bf`

### commodity_general (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='Palletized general merchandise'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "Palletized general merchandise"}`
- cost_estimate matched (1610.0).
- load_id: `8fb1d675-7997-4924-ab8c-550b6206d9fd`

### commodity_hazmat_3 (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='HAZMAT class 3 (flammable liquids)'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "HAZMAT class 3 (flammable liquids)"}`
- cost_estimate matched (1610.0).
- load_id: `a8a68b62-2ac5-476e-a71c-e8f39ffb5cf9`

### commodity_hazmat_8 (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='HAZMAT class 8 (corrosive)'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "HAZMAT class 8 (corrosive)"}`
- cost_estimate matched (1610.0).
- load_id: `ef603c9d-11f2-4528-98e8-602c81554926`

### commodity_oversized (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='Oversized industrial machinery'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "Oversized industrial machinery"}`
- cost_estimate matched (1610.0).
- load_id: `a4d3a13d-9c56-4bc1-9327-01aa7847dacc`

### commodity_livestock (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='Livestock (live cattle)'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "Livestock (live cattle)"}`
- cost_estimate matched (1610.0).
- load_id: `9b007872-6b96-48c9-921f-a50c13ef39eb`

### commodity_hi_value (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='High-value electronics ($500k+)'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "High-value electronics ($500k+)"}`
- cost_estimate matched (1610.0).
- load_id: `a91220f2-3b68-4ef5-9127-daf3994392f7`

### commodity_produce (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='Fresh produce (mixed)'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "Fresh produce (mixed)"}`
- cost_estimate matched (1610.0).
- load_id: `6f8aedbc-612e-4939-8b77-a7e65e6b6dfe`

### special_team (special)  -- **PASS**

- **expectation:** special_requirements='Team driver required' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "Team driver required", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `5fad65f2-16ed-4d1a-abb4-644c5ad85641`

### special_twic (special)  -- **PASS**

- **expectation:** special_requirements='TWIC card required' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "TWIC card required", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `236c85ed-23aa-4b3f-946e-3da3f21dd9ce`

### special_hazmat_endorsed (special)  -- **PASS**

- **expectation:** special_requirements='HAZMAT endorsement required' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "HAZMAT endorsement required", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `552a5993-8315-45a4-8446-e9f1551943c9`

### special_tarped (special)  -- **PASS**

- **expectation:** special_requirements='Tarped flatbed required' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "Tarped flatbed required", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `ce651226-db60-43f4-97d6-b121adb9723f`

### special_live_load (special)  -- **PASS**

- **expectation:** special_requirements='Live load (no drop)' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "Live load (no drop)", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `848b93a0-ac05-4cdf-8dbf-8e6a86c8ad90`

### special_drop_hook (special)  -- **PASS**

- **expectation:** special_requirements='Drop and hook' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "Drop and hook", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `b5d36f80-a912-4e29-957d-e6b84a409969`

### edge_zero_mile_local (edge)  -- **PASS**

- **expectation:** 0-mile local trip persists with miles_hint=1; cost_estimate calculated normally; no divide-by-zero.
- **predicted:** `{"cost_estimate_value": 245.0}`
- cost_estimate matched (245.0).
- load_id: `d7c8beba-e531-4f58-b0cc-b40066caee22`

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
- load_id: `c3782e43-ea9b-4a61-8d0f-ece777597495`

### margin_floor_soft_warn (accessorial)  -- **PASS**

- **expectation:** sell=2000, carrier=1750 -> margin=$250 (12.5%) -> meets $250 abs AND >= 5% pct (passes hard) but below 15% warn -> soft_warn.
- **predicted:** `{"margin_verdict": "soft_warn"}`
- margin verdict matched (soft_warn).
- load_id: `f9400b3c-4348-40f5-a8f0-5500d3d0109e`

### margin_floor_clean (accessorial)  -- **PASS**

- **expectation:** sell=2000, carrier=1500 -> 25% margin -> clean.
- **predicted:** `{"margin_verdict": "clean"}`
- margin verdict matched (clean).
- load_id: `ff131fbc-4735-4334-96aa-b848673e56dc`

### detention_4hr_dwell (accessorial)  -- **PASS**

- **expectation:** 4hr dwell - 2hr free = 2hr billable @ $75/hr = $150 detention. Round-up 15-min increments (8 inc * $18.75 = $150).
- **predicted:** `{"detention_amount_usd": 150.0}`
- detention amount matched ($150.0).
- load_id: `c85973f6-7b54-44c5-9dd5-6b68c09e5aff`

### detention_under_free_time (accessorial)  -- **PASS**

- **expectation:** 1.5hr dwell < 2hr free -> 0 detention.
- **predicted:** `{"detention_amount_usd": 0.0}`
- detention amount matched ($0.0).
- load_id: `9d81f6a6-a0a0-4b0e-9a8f-b91b1751b7bd`

### lumper_under_authorized (accessorial)  -- **PASS**

- **expectation:** Authorized $200, actual $175 -> -$25 variance (under_authorized).
- **predicted:** `{"has_variance": true}`
- No graded predictions; document-only.
- load_id: `d6b552d2-a14b-4030-9a85-033f5155f7fd`

### tonu_authorized_match (accessorial)  -- **PASS**

- **expectation:** Authorized $150, actual $150 -> no variance.
- **predicted:** `{"has_variance": false}`
- No graded predictions; document-only.
- load_id: `514c46de-e086-4fd4-91ac-86efce93dfb7`

---

Generated by `scripts/rate_calc_scenarios.py`. HTTP-only -- no direct imports of business logic. Cleanup: `--purge --yes`.