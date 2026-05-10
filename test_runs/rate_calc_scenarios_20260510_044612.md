# Rate Calculator Scenarios -- 20260510_044612

**Run ID:** `a07cd3ad-7655-4a9a-a36b-85df5085e0b4`
**Total scenarios:** 45

## Grade tally
- **PASS**: 42
- **FAIL**: 1
- **OPEN**: 2
- **ERROR**: 0

## By category
- **accessorial**: PASS=7
- **commodity**: PASS=7
- **distance**: PASS=6
- **edge**: OPEN=2, PASS=3
- **equipment**: PASS=9
- **rate_calc_only**: FAIL=1, PASS=4
- **special**: PASS=6

## Per-scenario detail

### distance_0_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 245.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (245.0).
- load_id: `6fa1eac8-59d5-40a0-8b00-049bf4bda09b`

### distance_100_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 245.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (245.0).
- load_id: `7f52b4ea-3c40-4445-8cea-f449226a290a`

### distance_300_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 635.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (635.0).
- load_id: `d4685141-81a8-4d36-9b8e-4b06d59d9d00`

### distance_800_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 1610.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (1610.0).
- load_id: `bee8431b-34c1-4860-a36f-e99f5706e78d`

### distance_1500_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 2975.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (2975.0).
- load_id: `cbe209c5-1582-4a89-b081-ef5be66981d0`

### distance_2500_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 4925.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (4925.0).
- load_id: `ba8020e0-7245-48b6-8caf-74f25b3a1244`

### equipment_dry_van (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=2.5 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 2.5, "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `caedb981-bf92-49ad-a374-3b10821ea553`

### equipment_reefer (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=3.0 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 3.0, "cost_estimate_value": 1922.0}`
- cost_estimate matched (1922.0).
- load_id: `2554c8ed-fa89-4d94-b09c-f8227e195365`

### equipment_flatbed (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=2.8 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 2.8, "cost_estimate_value": 1805.0}`
- cost_estimate matched (1805.0).
- load_id: `a8a0a286-b64a-47c8-8ac8-59e3dbd422eb`

### equipment_step_deck (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=3.1 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 3.1, "cost_estimate_value": 1980.5}`
- cost_estimate matched (1980.5).
- load_id: `f029332c-0bd3-4219-abf5-c3fe39f377ab`

### equipment_tanker (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=3.25 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 3.25, "cost_estimate_value": 2078.0}`
- cost_estimate matched (2078.0).
- load_id: `86714e10-85cb-4d5c-bbb1-95168d1477f8`

### equipment_stepdeck (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=3.1 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 3.1, "cost_estimate_value": 1980.5}`
- cost_estimate matched (1980.5).
- load_id: `7797d79f-2929-4faa-ad92-ff4d32b4b6e3`

### equipment_rgn (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=3.5 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 3.5, "cost_estimate_value": 2234.0}`
- cost_estimate matched (2234.0).
- load_id: `43062e2d-b562-476b-807e-c5b3243d4a56`

### equipment_hotshot (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=2.85 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 2.85, "cost_estimate_value": 1824.5}`
- cost_estimate matched (1824.5).
- load_id: `6d4dd1d6-2022-4635-8a2b-cfd8d15fe1ed`

### equipment_power_only (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=1.95 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 1.95, "cost_estimate_value": 1259.0}`
- cost_estimate matched (1259.0).
- load_id: `58c552ce-11c4-4447-8c95-f4220ecfe178`

### commodity_general (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='Palletized general merchandise'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "Palletized general merchandise"}`
- cost_estimate matched (1610.0).
- load_id: `609c5ceb-6ab8-48b3-8602-bd151299ff86`

### commodity_hazmat_3 (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='HAZMAT class 3 (flammable liquids)'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "HAZMAT class 3 (flammable liquids)"}`
- cost_estimate matched (1610.0).
- load_id: `d70544a0-d402-489e-83f5-dc96055ed7bb`

### commodity_hazmat_8 (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='HAZMAT class 8 (corrosive)'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "HAZMAT class 8 (corrosive)"}`
- cost_estimate matched (1610.0).
- load_id: `9b178e86-7780-4332-b367-1ec2caab2c02`

### commodity_oversized (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='Oversized industrial machinery'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "Oversized industrial machinery"}`
- cost_estimate matched (1610.0).
- load_id: `8a41fbbc-e0a3-482f-8c1a-98a2a05e6be9`

### commodity_livestock (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='Livestock (live cattle)'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "Livestock (live cattle)"}`
- cost_estimate matched (1610.0).
- load_id: `3ba6a6da-a8f2-4311-9dc2-d243a9344ae1`

### commodity_hi_value (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='High-value electronics ($500k+)'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "High-value electronics ($500k+)"}`
- cost_estimate matched (1610.0).
- load_id: `3bf4200a-c275-45b3-bc29-b3962afe4715`

### commodity_produce (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='Fresh produce (mixed)'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "Fresh produce (mixed)"}`
- cost_estimate matched (1610.0).
- load_id: `a51d759c-d638-4056-8d72-2f04b2c97c24`

### special_team (special)  -- **PASS**

- **expectation:** special_requirements='Team driver required' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "Team driver required", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `285a9396-1896-4359-95e8-efee3949bb6f`

### special_twic (special)  -- **PASS**

- **expectation:** special_requirements='TWIC card required' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "TWIC card required", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `296be960-a29e-41db-9492-031cf0e727ee`

### special_hazmat_endorsed (special)  -- **PASS**

- **expectation:** special_requirements='HAZMAT endorsement required' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "HAZMAT endorsement required", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `c4a85891-7f57-400c-b518-89291b7fd2b6`

### special_tarped (special)  -- **PASS**

- **expectation:** special_requirements='Tarped flatbed required' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "Tarped flatbed required", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `cf5ebd4c-49a2-4c35-9a48-270a5116d82a`

### special_live_load (special)  -- **PASS**

- **expectation:** special_requirements='Live load (no drop)' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "Live load (no drop)", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `1993a277-fb5e-4ca9-b86b-070b2dea870a`

### special_drop_hook (special)  -- **PASS**

- **expectation:** special_requirements='Drop and hook' persisted on load doc; no automatic premium applied to rate or cost_estimate.
- **predicted:** `{"special_requirements_persisted": "Drop and hook", "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `9ffe2524-eecc-4215-bc20-48e19efba8aa`

### edge_zero_mile_local (edge)  -- **PASS**

- **expectation:** 0-mile local trip persists with miles_hint=1; cost_estimate calculated normally; no divide-by-zero.
- **predicted:** `{"cost_estimate_value": 245.0}`
- cost_estimate matched (245.0).
- load_id: `2f5f81e9-7435-42d0-b7f8-3f6595d053ed`

### edge_negative_weight (edge)  -- **OPEN**

- **expectation:** Negative weight: LoadCreate has weight: int (no Field(ge=0)). Likely accepted; flag as silent-accept finding.
- Load creation failed (status 422); cannot grade.
- **finding:** `{"kind": "load_create_http_error", "status": 422, "body": "{\"detail\":[{\"type\":\"value_error\",\"loc\":[\"body\"],\"msg\":\"Value error, weight must be > 0 (got -5000)\",\"input\":{\"origin_city\":\"Dallas\",\"origin_state\":\"TX\",\"origin_lat\":32.7767,\"origin_lng\":-96.797,\"destination_city\`

### edge_negative_rate (edge)  -- **OPEN**

- **expectation:** Negative rate: server-side cost_estimate guard `if float(doc['rate']) > 0` suppresses cost_estimate auto-fill. Load itself likely persists -- silent-accept finding.
- Load creation failed (status 422); cannot grade.
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

### ratecalc_known_lane_TX_CA (rate_calc_only)  -- **FAIL**

- **expectation:** Hard-coded TX-CA = 1400mi; dry_van base RPM 2.50; weight 25000 -> factor 1.0; total = 1400*2.50 = 3500; market = total*1.05 = 3675.
- **predicted:** `{"estimated_distance_miles": 1400, "base_rate_per_mile": 2.5, "weight_factor": 1.0, "total_rate": 3500.0, "market_rate": 3675.0}`
- market_rate: predicted 3675.0, got 3920.0

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
- load_id: `dd9da852-33d3-4c72-9969-00f463017c8f`

### margin_floor_soft_warn (accessorial)  -- **PASS**

- **expectation:** sell=2000, carrier=1750 -> margin=$250 (12.5%) -> meets $250 abs AND >= 5% pct (passes hard) but below 15% warn -> soft_warn.
- **predicted:** `{"margin_verdict": "soft_warn"}`
- margin verdict matched (soft_warn).
- load_id: `7201246a-8534-4f2b-912f-3129243bf8ab`

### margin_floor_clean (accessorial)  -- **PASS**

- **expectation:** sell=2000, carrier=1500 -> 25% margin -> clean.
- **predicted:** `{"margin_verdict": "clean"}`
- margin verdict matched (clean).
- load_id: `c9987da8-b9fc-4e8d-bf94-c7f6f75e0fd6`

### detention_4hr_dwell (accessorial)  -- **PASS**

- **expectation:** 4hr dwell - 2hr free = 2hr billable @ $75/hr = $150 detention. Round-up 15-min increments (8 inc * $18.75 = $150).
- **predicted:** `{"detention_amount_usd": 150.0}`
- detention amount matched ($150.0).
- load_id: `5406f0de-d0b3-49f7-9f65-928ff6a86225`

### detention_under_free_time (accessorial)  -- **PASS**

- **expectation:** 1.5hr dwell < 2hr free -> 0 detention.
- **predicted:** `{"detention_amount_usd": 0.0}`
- detention amount matched ($0.0).
- load_id: `138429e6-e9cc-4cc7-be83-78e64ea242e5`

### lumper_under_authorized (accessorial)  -- **PASS**

- **expectation:** Authorized $200, actual $175 -> -$25 variance (under_authorized).
- **predicted:** `{"has_variance": true}`
- No graded predictions; document-only.
- load_id: `0152b747-d084-4bfe-b7fa-36b32944ef1b`

### tonu_authorized_match (accessorial)  -- **PASS**

- **expectation:** Authorized $150, actual $150 -> no variance.
- **predicted:** `{"has_variance": false}`
- No graded predictions; document-only.
- load_id: `c3cc005d-9434-48bd-b1c0-60644e8c346c`

---

Generated by `scripts/rate_calc_scenarios.py`. HTTP-only -- no direct imports of business logic. Cleanup: `--purge --yes`.