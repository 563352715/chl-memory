# Rate Calculator Scenarios -- 20260509_203144

**Run ID:** `15ffbef6-4bc3-4f1d-bf88-e999b77a1482`
**Total scenarios:** 20

## Grade tally
- **PASS**: 20
- **FAIL**: 0
- **OPEN**: 0
- **ERROR**: 0

## By category
- **commodity**: PASS=5
- **distance**: PASS=6
- **equipment**: PASS=9

## Per-scenario detail

### distance_0_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 245.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (245.0).
- load_id: `29a929ab-8d87-41bf-b655-4412d7d8a679`

### distance_100_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 245.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (245.0).
- load_id: `70bb7011-ec14-40cb-bca2-134930e78a86`

### distance_300_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 635.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (635.0).
- load_id: `8df4d6a6-d594-4016-b9c3-7db7608756bc`

### distance_800_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 1610.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (1610.0).
- load_id: `c7c0c064-2b0e-4a0a-91ed-6d62623b62b0`

### distance_1500_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 2975.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (2975.0).
- load_id: `93c202a3-1bff-433b-b92d-4fbe94890ca5`

### distance_2500_dryvan (distance)  -- **PASS**

- **expectation:** Load persists with cost_estimate auto-filled; rate-calc endpoint returns base_rate_per_mile=2.50 for dry_van.
- **predicted:** `{"cost_estimate_value": 4925.0, "visibility_bonus": 50.0, "rate_calc_base_rpm": 2.5}`
- cost_estimate matched (4925.0).
- load_id: `6e689b4f-3f59-4037-9952-b9d836038eec`

### equipment_dry_van (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=2.5 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 2.5, "cost_estimate_value": 1610.0}`
- cost_estimate matched (1610.0).
- load_id: `3ba443e4-7e7a-403f-bd4b-fc07a88e302a`

### equipment_reefer (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=3.0 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 3.0, "cost_estimate_value": 1922.0}`
- cost_estimate matched (1922.0).
- load_id: `9d77a99b-0f8c-4cc2-a7f0-9ac681067fc6`

### equipment_flatbed (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=2.8 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 2.8, "cost_estimate_value": 1805.0}`
- cost_estimate matched (1805.0).
- load_id: `03252e24-4a91-4738-acfc-a9aadb3c7fdc`

### equipment_step_deck (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=3.1 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 3.1, "cost_estimate_value": 1980.5}`
- cost_estimate matched (1980.5).
- load_id: `b5a110a5-b58c-4495-b3e7-2c7d419e6741`

### equipment_tanker (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=3.25 (supported equipment); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 3.25, "cost_estimate_value": 2078.0}`
- cost_estimate matched (2078.0).
- load_id: `ce70bd7c-46b9-4fcf-b31b-6b44d57d6da5`

### equipment_stepdeck (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=2.75 (unsupported -> default 2.75); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 2.75, "cost_estimate_value": 1766.0}`
- cost_estimate matched (1766.0).
- load_id: `7f87be55-d2c0-4ba7-a189-45b87dd02c01`

### equipment_rgn (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=2.75 (unsupported -> default 2.75); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 2.75, "cost_estimate_value": 1766.0}`
- cost_estimate matched (1766.0).
- load_id: `b9d451b5-e749-47cf-b3f6-cc05b1f797b3`

### equipment_hotshot (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=2.75 (unsupported -> default 2.75); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 2.75, "cost_estimate_value": 1766.0}`
- cost_estimate matched (1766.0).
- load_id: `f3f48666-e1fe-4e0c-9866-908d5e2157cb`

### equipment_power_only (equipment)  -- **PASS**

- **expectation:** rate-calc returns base_rpm=2.75 (unsupported -> default 2.75); load persists regardless.
- **predicted:** `{"rate_calc_base_rpm": 2.75, "cost_estimate_value": 1766.0}`
- cost_estimate matched (1766.0).
- load_id: `5eb25979-2885-47a0-9548-8f8d855c0ebf`

### commodity_general (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='Palletized general merchandise'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "Palletized general merchandise"}`
- cost_estimate matched (1610.0).
- load_id: `5d549f29-f816-466d-a45c-db8e7254fdc2`

### commodity_hazmat_3 (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='HAZMAT class 3 (flammable liquids)'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "HAZMAT class 3 (flammable liquids)"}`
- cost_estimate matched (1610.0).
- load_id: `898f11b9-faf2-454d-b6f5-0b4eb20e09d7`

### commodity_hazmat_8 (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='HAZMAT class 8 (corrosive)'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "HAZMAT class 8 (corrosive)"}`
- cost_estimate matched (1610.0).
- load_id: `22e2e73b-a28b-4909-8f30-2c62137eb357`

### commodity_oversized (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='Oversized industrial machinery'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "Oversized industrial machinery"}`
- cost_estimate matched (1610.0).
- load_id: `03facbba-b072-4b9f-bd8d-61c8ca2c8c0a`

### commodity_livestock (commodity)  -- **PASS**

- **expectation:** Load persists with commodity='Livestock (live cattle)'; cost_estimate auto-filled; no commodity-specific surcharge.
- **predicted:** `{"cost_estimate_value": 1610.0, "commodity_persisted": "Livestock (live cattle)"}`
- cost_estimate matched (1610.0).
- load_id: `a631525d-4fbe-428f-a3aa-ac815e4b9a6b`

---

Generated by `scripts/rate_calc_scenarios.py`. HTTP-only -- no direct imports of business logic. Cleanup: `--purge --yes`.