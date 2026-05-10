# Rate Calculator Scenario Battery -- 20260510_001040

Total: 66  |  PASS: 30  |  FAIL: 0  |  OPEN: 36

OPEN = expected calculator does not exist in the backend yet.

## Summary table

| # | Verdict | Category | Scenario | Note |
|---|---------|----------|----------|------|
| 1 | PASS | base_rpm | `rpm_sub_100mi_ltl` |  |
| 2 | PASS | base_rpm | `rpm_regional_200mi` |  |
| 3 | PASS | base_rpm | `rpm_mid_haul_500mi` |  |
| 4 | PASS | base_rpm | `rpm_long_haul_1200mi` |  |
| 5 | PASS | base_rpm | `rpm_coast_to_coast_2200mi` |  |
| 6 | PASS | base_rpm | `rpm_extreme_3000mi` |  |
| 7 | PASS | edge | `rpm_zero_mile_local_trip` |  |
| 8 | PASS | edge | `rpm_one_mile_micro` |  |
| 9 | OPEN | equipment | `equipment_dry_van` | No equipment-premium calculator exists in the backend. Equipment is captured on  |
| 10 | OPEN | equipment | `equipment_reefer` | No equipment-premium calculator exists in the backend. Equipment is captured on  |
| 11 | OPEN | equipment | `equipment_flatbed` | No equipment-premium calculator exists in the backend. Equipment is captured on  |
| 12 | OPEN | equipment | `equipment_step_deck` | No equipment-premium calculator exists in the backend. Equipment is captured on  |
| 13 | OPEN | equipment | `equipment_rgn_oversize` | No equipment-premium calculator exists in the backend. Equipment is captured on  |
| 14 | OPEN | equipment | `equipment_hotshot` | No equipment-premium calculator exists in the backend. Equipment is captured on  |
| 15 | OPEN | equipment | `equipment_power_only` | No equipment-premium calculator exists in the backend. Equipment is captured on  |
| 16 | OPEN | equipment | `equipment_container_chassis` | No equipment-premium calculator exists in the backend. Equipment is captured on  |
| 17 | OPEN | commodity | `commodity_general_freight` | No commodity-premium calculator exists. `cargo_type` is logged on the load doc b |
| 18 | OPEN | commodity | `commodity_food_fsma` | No commodity-premium calculator exists. `cargo_type` is logged on the load doc b |
| 19 | OPEN | commodity | `commodity_hazmat_class_1_explosive` | No commodity-premium calculator exists. `cargo_type` is logged on the load doc b |
| 20 | OPEN | commodity | `commodity_hazmat_class_4_flammable_solid` | No commodity-premium calculator exists. `cargo_type` is logged on the load doc b |
| 21 | OPEN | commodity | `commodity_hazmat_class_8_corrosive` | No commodity-premium calculator exists. `cargo_type` is logged on the load doc b |
| 22 | OPEN | commodity | `commodity_refrigerated_cold_chain` | No commodity-premium calculator exists. `cargo_type` is logged on the load doc b |
| 23 | OPEN | commodity | `commodity_oversize_overweight_permit` | No commodity-premium calculator exists. `cargo_type` is logged on the load doc b |
| 24 | OPEN | commodity | `commodity_hi_value_over_100k` | No commodity-premium calculator exists. `cargo_type` is logged on the load doc b |
| 25 | OPEN | commodity | `commodity_livestock` | No commodity-premium calculator exists. `cargo_type` is logged on the load doc b |
| 26 | OPEN | special | `special_team_driver_24_7` | No special-condition uplift calculator exists. Team / TWIC / HAZMAT-endorsed / t |
| 27 | OPEN | special | `special_twic_card_port` | No special-condition uplift calculator exists. Team / TWIC / HAZMAT-endorsed / t |
| 28 | OPEN | special | `special_hazmat_endorsement` | No special-condition uplift calculator exists. Team / TWIC / HAZMAT-endorsed / t |
| 29 | OPEN | special | `special_tarped_flatbed` | No special-condition uplift calculator exists. Team / TWIC / HAZMAT-endorsed / t |
| 30 | OPEN | special | `special_live_load_unload` | No special-condition uplift calculator exists. Team / TWIC / HAZMAT-endorsed / t |
| 31 | OPEN | special | `special_drop_and_hook` | No special-condition uplift calculator exists. Team / TWIC / HAZMAT-endorsed / t |
| 32 | OPEN | special | `special_trailer_53_vs_48` | No special-condition uplift calculator exists. Team / TWIC / HAZMAT-endorsed / t |
| 33 | PASS | accessorial | `detention_pickup_2h_within_free` |  |
| 34 | PASS | accessorial | `detention_pickup_2h47m_rounds_down` |  |
| 35 | PASS | accessorial | `detention_pickup_4h_charge_at_50_per_hr` |  |
| 36 | PASS | accessorial | `detention_capped_at_8hr_max_per_stop` |  |
| 37 | PASS | edge | `detention_negative_wait_clamped_to_zero` |  |
| 38 | OPEN | accessorial | `fee_lumper_fee_shipper_side_200` | No native fee-calculator for this accessorial.  Operator enters the $ amount man |
| 39 | OPEN | accessorial | `fee_layover_24h_plus` | No native fee-calculator for this accessorial.  Operator enters the $ amount man |
| 40 | OPEN | accessorial | `fee_stop_off_per_extra_stop` | No native fee-calculator for this accessorial.  Operator enters the $ amount man |
| 41 | OPEN | accessorial | `fee_driver_assist_fee` | No native fee-calculator for this accessorial.  Operator enters the $ amount man |
| 42 | OPEN | accessorial | `fee_liftgate_required` | No native fee-calculator for this accessorial.  Operator enters the $ amount man |
| 43 | OPEN | accessorial | `fee_inside_delivery` | No native fee-calculator for this accessorial.  Operator enters the $ amount man |
| 44 | OPEN | accessorial | `fee_reweigh_fee_at_dot_scale` | No native fee-calculator for this accessorial.  Operator enters the $ amount man |
| 45 | PASS | margin | `margin_clean_dispatch_18pct` |  |
| 46 | PASS | margin | `margin_soft_warn_below_15pct` |  |
| 47 | PASS | margin | `margin_hard_block_below_floor_usd` |  |
| 48 | PASS | margin | `margin_hard_block_buy_geq_sell` |  |
| 49 | PASS | edge | `margin_hard_block_negative_sell` |  |
| 50 | PASS | edge | `margin_hard_block_zero_carrier` |  |
| 51 | PASS | margin | `quote_below_market_warn` |  |
| 52 | PASS | margin | `quote_within_market_clean` |  |
| 53 | PASS | margin | `tonu_tier_none_lead` |  |
| 54 | PASS | margin | `tonu_tier_pre_dispatch_150` |  |
| 55 | PASS | margin | `tonu_tier_post_dispatch_250` |  |
| 56 | PASS | margin | `tonu_tier_post_arrival_350_plus_deadhead` |  |
| 57 | PASS | margin | `tonu_tier_mid_transit_500` |  |
| 58 | PASS | margin | `tonu_carrier_fault_zero` |  |
| 59 | PASS | margin | `tonu_post_arrival_with_deadhead` |  |
| 60 | PASS | margin | `factor_chl_sovereign_captures_3pct` |  |
| 61 | PASS | margin | `factor_3rd_party_pays_3pct` |  |
| 62 | OPEN | edge | `reject_negative_weight_data_corruption` | No reject/block logic for these data-integrity violations.  Loads can be persist |
| 63 | OPEN | edge | `reject_negative_rate_data_corruption` | No reject/block logic for these data-integrity violations.  Loads can be persist |
| 64 | OPEN | edge | `reject_hazmat_class_1_without_driver_endorsement` | No reject/block logic for these data-integrity violations.  Loads can be persist |
| 65 | OPEN | edge | `reject_oversized_without_state_permit` | No reject/block logic for these data-integrity violations.  Loads can be persist |
| 66 | OPEN | edge | `reject_reefer_no_temp_setpoint` | No reject/block logic for these data-integrity violations.  Loads can be persist |

## Per-category breakdown

| Category | PASS | FAIL | OPEN |
|----------|------|------|------|
| accessorial | 4 | 0 | 7 |
| base_rpm | 6 | 0 | 0 |
| commodity | 0 | 0 | 9 |
| edge | 5 | 0 | 5 |
| equipment | 0 | 0 | 8 |
| margin | 15 | 0 | 0 |
| special | 0 | 0 | 7 |

## OPEN findings (calculator missing)

### No equipment-premium calculator exists in the backend. Equipment is captured on the load doc as `equipment_type` / `trailer_type` but has no lookup-table or formula multiplying the base RPM. Operator sets the rate manually per equipment type.

- `equipment_dry_van`
- `equipment_reefer`
- `equipment_flatbed`
- `equipment_step_deck`
- `equipment_rgn_oversize`
- `equipment_hotshot`
- `equipment_power_only`
- `equipment_container_chassis`

### No commodity-premium calculator exists. `cargo_type` is logged on the load doc but no formula adjusts rate or blocks based on commodity class. HAZMAT class / hi-value / livestock / oversize are not gated.

- `commodity_general_freight`
- `commodity_food_fsma`
- `commodity_hazmat_class_1_explosive`
- `commodity_hazmat_class_4_flammable_solid`
- `commodity_hazmat_class_8_corrosive`
- `commodity_refrigerated_cold_chain`
- `commodity_oversize_overweight_permit`
- `commodity_hi_value_over_100k`
- `commodity_livestock`

### No special-condition uplift calculator exists. Team / TWIC / HAZMAT-endorsed / tarped / 53' / drop-and-hook are recorded as boolean flags on loads but no rate formula adds an uplift % or $ amount.

- `special_team_driver_24_7`
- `special_twic_card_port`
- `special_hazmat_endorsement`
- `special_tarped_flatbed`
- `special_live_load_unload`
- `special_drop_and_hook`
- `special_trailer_53_vs_48`

### No native fee-calculator for this accessorial.  Operator enters the $ amount manually as a line item on the load.accessorials array. delivered_load_autofire.py:149 then includes them on the invoice.

- `fee_lumper_fee_shipper_side_200`
- `fee_layover_24h_plus`
- `fee_stop_off_per_extra_stop`
- `fee_driver_assist_fee`
- `fee_liftgate_required`
- `fee_inside_delivery`
- `fee_reweigh_fee_at_dot_scale`

### No reject/block logic for these data-integrity violations.  Loads can be persisted with negative weight, negative rate, missing reefer set-point, HAZMAT without driver endorsement check.  Recommend a load_validator module gated on dispatch.

- `reject_negative_weight_data_corruption`
- `reject_negative_rate_data_corruption`
- `reject_hazmat_class_1_without_driver_endorsement`
- `reject_oversized_without_state_permit`
- `reject_reefer_no_temp_setpoint`
