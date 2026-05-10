# Autonomous Synthetic Test Run -- Final Summary
**Run window:** 20260510_010951 UTC
**Runners:** sop_flow_orchestrator + multi_actor_synthesizer + rate_calc_scenarios + synthetic_state_watcher + self_heal_bridge

## SOP Flow Orchestrator
- PASS=54  FAIL=36  total=90

### Top failures / opens for SOP Flow Orchestrator (first 25)
| Scenario | Detail |
|---|---|
| `fi_mc_revoked` |  |
| `fi_insurance_expired_carrier` |  |
| `fi_missing_rate_con_sign` |  |
| `fi_cancel_load_tonu` | server_error |
| `fi_wrong_consignee_pin` | conflict |
| `fi_pod_missing_on_deliver` | failure-inject expected 'consignee_confirmation_required', got actual='None', ok=False, final_stage=expected_pod_missing |
| `fi_bad_bol_upload` | pod_required |
| `fi_geofence_delivery` | invalid_transition |
| `fi_geofence_pickup` | failure-inject expected 'geofence_violation', got actual='None', ok=False, final_stage=expected_geofence_pickup |

## Multi-Actor Synthesizer
- pass=78  open=22  fail=11  total=111

### Top failures / opens for Multi-Actor Synthesizer (first 25)
| Scenario | Detail |
|---|---|
| `driver_only_happy` | 400 |
| `full_lifecycle` |  |
| `factor_dispute_resolve_no_auth` | 404 |
| `factor_send_payment` | factor-pay path uses Stripe webhook same as shipper-pay. 401 = correct security posture. Real factor ACH would land via ... |
| `shipper_dispute_chargeback` | no public shipper-dispute endpoint exists. Factor-side dispute resolver is operator-only (POST /api/_factor_disputes/{id... |
| `shipper_pay_invoice` | Stripe webhook is signature-locked (correct security posture). Shipper-pay path not reachable from synthesizer without t... |
| `shipper_consignee_confirm_pin` | prereq state not met (offer not accepted or load not in_transit/booked) |
| `driver_pod_upload_only` | 400 |
| `shipper_consignee_confirm_signature` | prereq state not met (offer/load status) |

## Rate Calc Scenarios
- PASS=44  FAIL=1  total=45

### Top failures / opens for Rate Calc Scenarios (first 25)
| Scenario | Detail |
|---|---|
| `ratecalc_weight_factor_45001` | ['weight_factor: predicted 1.15, got 1.1', 'adjusted_rate_per_mile: predicted 2.88, got 2.75'] |

## Self-heal queue items written this run
- urgent_alerts (critical): **0**
- dev_review_queue (recurring): **1**
- operator_push_queue (action-required): **27**

### Sample dev_review_queue items (first 10)
| Scenario | Severity | Recur 60min | Actual failure |
|---|---|---|---|
| `driver_only_happy` | warning | 3 |  |

## Auto-fixes applied during this run
See `chl-memory/test_runs/auto_fix_log.md` for the full chronological log.

## Key findings (carried forward from sub-agent build phase)
### Logic bugs (auto-fixable)
- **LL_LOGIC_BUG_001** — `backend/server.py` weight-bracket reachability bug: `if w > 40000 ... elif w > 45000` makes the second branch unreachable. Fix: reorder the if/elif so the higher threshold checks first.

### Platform gaps (architectural — operator review)
- **LL_PLATFORM_GAP_001** — No equipment-type rate premium calculator. Dry van baseline; reefer/flatbed/RGN/etc. need premium %s.
- **LL_PLATFORM_GAP_002** — No commodity surcharge / blocking calculator (HAZMAT class, hi-value, oversize, livestock).
- **LL_PLATFORM_GAP_003** — No special-condition uplift (team driver, TWIC, tarped, etc.).
- **LL_PLATFORM_GAP_004** — Limited accessorial calculator. Only detention + TONU exist; lumper/layover/stop-off/liftgate/inside-delivery/reweigh are freeform line items with no native calculation.
- **LL_PLATFORM_GAP_005** — Missing reject validators: negative weight/rate, HAZMAT class 1 without endorsement, oversize without permit, reefer without temp set-point.
- **LL_PLATFORM_GAP_006** — No shipper-side dispute endpoint (`POST /api/public/shipper-claim` proposed).

### Auth / security findings
- **LL_AUTH_GAP_001** — Stripe webhook signature lock blocks shipper-pay simulation (correct security; needs `/api/_test/stripe-webhook-replay` for testing).

### May-14 readiness risks (from failsafe coverage matrix)
7 untested compliance gates: D&A Clearinghouse, HOS, HAZMAT, CARB, state UCR/IRP/IFTA, cash valve, auto_credit_check. Some may be intentional scope cuts (PWA-primary tracking memory).

## Recommended next actions
1. Operator triage of 7 May-14 untested gates -- mark intentional vs real.
2. Fix LL_LOGIC_BUG_001 (reorder weight bracket) -- 5-min fix, safe.
3. Decide: build rate-calc gaps (LL_PLATFORM_GAP_001..005) before May-14 OR after launch.
4. Decide: build /api/public/shipper-claim endpoint OR continue routing shipper claims via inbound-email triage.
5. Decide: enable self-heal Phase 1-4 (currently feature-flag-gated OFF in `cron_scheduler.py:3427-3605`).
6. Decide: build `/api/_test/stripe-webhook-replay` to enable end-to-end shipper-payment + factor-payment synthetic testing.

_Generated 20260510_010951 UTC by `scripts/final_test_report.py`._