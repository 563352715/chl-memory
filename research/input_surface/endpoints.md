# CHL Backend API Endpoints

**Total Endpoints: 839**

| Path | Method | File | Line | Purpose |
|------|--------|------|------|---------|
| `/1099/{year}` | GET | carrier_hub.py | 426 | Get record |
| `/10dlc/refresh` | POST | notification_service.py | 598 | Execute action |
| `/10dlc/status` | GET | notification_service.py | 594 | Health/status check |
| `/_health` | GET | carrier_tier_promoter\promoter_router.py | 89 | Health/status check |
| `/_health` | GET | credit_override_revoker\revoker_router.py | 84 | Health/status check |
| `/_health` | GET | detention\detention_router.py | 103 | Health/status check |
| `/_health` | GET | inbox_triage\triage_router.py | 67 | Health/status check |
| `/_health` | GET | renewals\renewals_router.py | 202 | Health/status check |
| `/_health` | GET | self_healing\self_heal_router.py | 43 | Health/status check |
| `/accounts` | GET | mercury_client.py | 149 | Get record |
| `/activate` | POST | phase2_readiness.py | 303 | Execute action |
| `/aggregate` | GET | loadboards.py | 1613 | Get record |
| `/alert-dismiss/{load_id}` | POST | margin_floor_router.py | 209 | Execute action |
| `/alerts` | GET | anomaly_sniffer.py | 279 | Get record |
| `/alerts` | GET | margin_floor_router.py | 197 | Get record |
| `/alerts/{alert_id}/ack` | POST | anomaly_sniffer.py | 314 | Execute action |
| `/all` | GET | bulk_carrier_hunter.py | 253 | Get record |
| `/all` | GET | healthz_all_router.py | 391 | Get record |
| `/anomalies` | GET | throughput_governor.py | 409 | Get record |
| `/anomaly` | POST | throughput_governor.py | 403 | Execute action |
| `/api/_carrier_redispatch/_health` | GET | carrier_redispatch\redispatch_router.py | 131 | Health/status check |
| `/api/_carrier_redispatch/attempts` | GET | carrier_redispatch\redispatch_router.py | 66 | Get record |
| `/api/_client_errors` | POST | client_error_logger.py | 217 | Execute action |
| `/api/_compliance/expiring` | GET | carrier_docs\extraction_router.py | 107 | Get record |
| `/api/_dispatch/manual_release/{load_id}` | POST | server.py | 13334 | Execute action |
| `/api/_enrichment/carrier/{carrier_id}` | GET | enrichment\router.py | 46 | Get record |
| `/api/_enrichment/carrier/{carrier_id}/operator_set/{field}` | PUT | enrichment\router.py | 90 | Update record |
| `/api/_enrichment/carrier/{carrier_id}/refresh` | POST | enrichment\router.py | 57 | Execute action |
| `/api/_enrichment/health` | GET | enrichment\router.py | 135 | Health/status check |
| `/api/_factor_decline/_health` | GET | factor_decline_handler\decline_handler_router.py | 204 | Health/status check |
| `/api/_factor_decline/recent` | GET | factor_decline_handler\decline_handler_router.py | 71 | Get record |
| `/api/_factor_decline/{decline_id}` | GET | factor_decline_handler\decline_handler_router.py | 139 | Get record |
| `/api/_factor_decline/{decline_id}/manual_resolution` | POST | factor_decline_handler\decline_handler_router.py | 162 | Execute action |
| `/api/_monitor/carrier_changes_24h` | GET | server.py | 13404 | Get record |
| `/api/_rate_conf/sign/{token}` | GET | server.py | 13229 | Get record |
| `/api/_rate_conf/sign/{token}` | POST | server.py | 13259 | Execute action |
| `/api/_records-request/queue` | GET | records_request_router.py | 398 | Get record |
| `/api/_records-request/{req_id}` | GET | records_request_router.py | 490 | Get record |
| `/api/_records-request/{req_id}/respond` | POST | records_request_router.py | 519 | Execute action |
| `/api/_tracking/exceptions/active` | GET | tracking\exception_router.py | 150 | Get record |
| `/api/admin/fall-off-risks` | GET | graceful_exit.py | 231 | Get record |
| `/api/auth/magic-link/consume` | POST | magic_link.py | 98 | Execute action |
| `/api/auth/magic-link/request` | POST | magic_link.py | 139 | Execute action |
| `/api/broker-invoice/execute` | POST | broker_invoice.py | 655 | Execute action |
| `/api/broker-invoice/from-load/{load_id}` | POST | delivered_load_autofire.py | 594 | Execute action |
| `/api/broker-invoice/list` | GET | broker_invoice.py | 746 | List records |
| `/api/broker-invoice/pending` | GET | delivered_load_autofire.py | 369 | Get record |
| `/api/broker-invoice/{invoice_id}` | GET | broker_invoice.py | 753 | Get record |
| `/api/broker-invoice/{invoice_id}/discard` | POST | delivered_load_autofire.py | 550 | Execute action |
| `/api/broker-invoice/{invoice_id}/pdf` | GET | broker_invoice.py | 762 | Get record |
| `/api/broker-invoice/{invoice_id}/send` | POST | delivered_load_autofire.py | 387 | Execute action |
| `/api/broker/loads/{load_id}/cold-outreach` | POST | carrier_outreach.py | 279 | Execute action |
| `/api/broker/profit-ledger` | GET | payment_rails.py | 359 | Get record |
| `/api/broker/revenue-ledger` | GET | delivery_completion.py | 316 | Get record |
| `/api/broker/settlements/pending` | GET | payment_rails.py | 222 | Get record |
| `/api/broker/settlements/{payment_id}/fund` | POST | payment_rails.py | 270 | Execute action |
| `/api/capacity-hunter/config` | GET | capacity_hunter.py | 357 | Get record |
| `/api/capacity-hunter/config` | PUT | capacity_hunter.py | 361 | Update record |
| `/api/capacity-hunter/events` | GET | capacity_hunter.py | 377 | Get record |
| `/api/capacity-hunter/scan-now` | POST | capacity_hunter.py | 369 | Execute action |
| `/api/carrier-discovery/near-load/{load_id}` | GET | fmcsa_census.py | 298 | Get record |
| `/api/carrier/check-in/{check_in_id}` | GET | carrier_outreach.py | 841 | Get record |
| `/api/carrier/check-in/{check_in_id}/confirm` | POST | carrier_outreach.py | 851 | Execute action |
| `/api/carrier/financial-profile` | GET | payment_rails.py | 414 | Get record |
| `/api/carrier/financial-profile` | PUT | payment_rails.py | 426 | Update record |
| `/api/carrier/loads/{load_id}/complete-delivery` | POST | delivery_completion.py | 143 | Execute action |
| `/api/carrier/loads/{load_id}/delivery-eligibility` | GET | delivery_completion.py | 103 | Get record |
| `/api/carrier/me/payout-method` | GET | payment_switchboard.py | 359 | Get record |
| `/api/carrier/me/payout-method` | PUT | payment_switchboard.py | 368 | Update record |
| `/api/carrier/preferences` | GET | carrier_preferences.py | 77 | Get record |
| `/api/carrier/preferences` | PUT | carrier_preferences.py | 81 | Update record |
| `/api/carrier/preferences/opt-out` | POST | carrier_preferences.py | 100 | Execute action |
| `/api/carrier/preferences/reset` | POST | carrier_preferences.py | 95 | Execute action |
| `/api/carrier/truck-status` | POST | capacity_hunter.py | 394 | Health/status check |
| `/api/carrier/{carrier_id}/payout-method` | PATCH | payment_switchboard.py | 343 | Update record |
| `/api/carriers/documents/{document_id}/download` | GET | carrier_docs\router.py | 190 | Get record |
| `/api/carriers/workflow/schema` | GET | carrier_vetting_workflow.py | 430 | Get record |
| `/api/carriers/{dot_number}/documents` | GET | carrier_docs\router.py | 78 | Get record |
| `/api/carriers/{dot_number}/documents/{doc_type}/status` | POST | carrier_docs\router.py | 156 | Health/status check |
| `/api/carriers/{dot_number}/documents/{doc_type}/upload` | POST | carrier_docs\router.py | 94 | Execute action |
| `/api/carriers/{mc_number}/red_flags` | GET | carrier_vetting_workflow.py | 421 | Get record |
| `/api/carriers/{mc_number}/workflow` | GET | carrier_vetting_workflow.py | 320 | Get record |
| `/api/email-ingestion/config` | GET | email_ingestion.py | 362 | Get record |
| `/api/email-ingestion/config` | PUT | email_ingestion.py | 367 | Update record |
| `/api/email-ingestion/poll-now` | POST | email_ingestion.py | 376 | Execute action |
| `/api/email-ingestion/queue` | GET | email_ingestion.py | 395 | Get record |
| `/api/email-ingestion/queue/{qid}/approve` | POST | email_ingestion.py | 402 | Execute action |
| `/api/email-ingestion/queue/{qid}/reject` | POST | email_ingestion.py | 428 | Execute action |
| `/api/email-ingestion/status` | GET | email_ingestion.py | 380 | Health/status check |
| `/api/facility-plan` | GET | server.py | 12937 | Get record |
| `/api/facility-plan/digest` | GET | server.py | 13023 | Get record |
| `/api/facility-plan/milestone/{milestone_id}` | POST | server.py | 13143 | Execute action |
| `/api/fmcsa-census/ingest` | POST | fmcsa_census.py | 180 | Execute action |
| `/api/fmcsa-census/search` | GET | fmcsa_census.py | 258 | Get record |
| `/api/fmcsa-census/status` | GET | fmcsa_census.py | 239 | Health/status check |
| `/api/fmcsa/validate/{mc_number}` | GET | server.py | 12718 | Get record |
| `/api/hardware-key/challenge` | POST | privacy_hardware.py | 175 | Execute action |
| `/api/hardware-key/register` | POST | privacy_hardware.py | 156 | Execute action |
| `/api/hardware-key/status` | GET | privacy_hardware.py | 146 | Health/status check |
| `/api/hardware-key/verify` | POST | privacy_hardware.py | 186 | Execute action |
| `/api/lanes/top/{N}` | GET | lane_evaluator.py | 198 | Get record |
| `/api/lanes/{origin_city}/{origin_state}/{dest_city}/{dest_state}/score` | GET | lane_evaluator.py | 141 | Get record |
| `/api/live-board/loads` | GET | live_board.py | 35 | Get record |
| `/api/live-board/loads/bulk-hunt` | POST | live_board.py | 254 | Execute action |
| `/api/live-board/loads/{load_id}/fire-autobid` | POST | live_board.py | 206 | Execute action |
| `/api/live-board/loads/{load_id}/rerun-match` | POST | live_board.py | 234 | Execute action |
| `/api/live-board/stats` | GET | live_board.py | 169 | Get record |
| `/api/live-map/ghost-pins` | GET | market_radar.py | 114 | Get record |
| `/api/loads/{id}/dispatch` | POST | integrity\operational_state.py | 44 | Execute action |
| `/api/loads/{load_id}/consignee-confirmation` | GET | consignee_confirmation.py | 284 | Get record |
| `/api/loads/{load_id}/consignee-confirmation/dispatcher-override` | POST | consignee_confirmation.py | 306 | Execute action |
| `/api/loads/{load_id}/consignee-confirmation/regenerate-pin` | POST | consignee_confirmation.py | 335 | Execute action |
| `/api/loads/{load_id}/documents` | GET | payment_rails.py | 404 | Get record |
| `/api/loads/{load_id}/documents` | POST | payment_rails.py | 375 | Execute action |
| `/api/loads/{load_id}/exceptions` | GET | tracking\exception_router.py | 105 | Get record |
| `/api/loads/{load_id}/exceptions/{exc_id}/acknowledge` | POST | tracking\exception_router.py | 174 | Execute action |
| `/api/loads/{load_id}/return` | POST | graceful_exit.py | 66 | Execute action |
| `/api/loads/{load_id}/returns` | GET | graceful_exit.py | 225 | Get record |
| `/api/loads/{load_id}/tracking` | GET | tracking\router.py | 201 | Get record |
| `/api/loads/{load_id}/tracking/latest` | GET | tracking\router.py | 215 | Get record |
| `/api/margin-erosion/config` | GET | margin_erosion.py | 203 | Get record |
| `/api/margin-erosion/config` | PUT | margin_erosion.py | 207 | Update record |
| `/api/margin-erosion/run-now` | POST | margin_erosion.py | 215 | Execute action |
| `/api/margin-erosion/runs` | GET | margin_erosion.py | 219 | Get record |
| `/api/market-radar/sources` | GET | market_radar.py | 73 | Get record |
| `/api/noa/execute` | POST | broker_invoice.py | 824 | Execute action |
| `/api/noa/list` | GET | broker_invoice.py | 845 | List records |
| `/api/noa/{noa_id}` | GET | broker_invoice.py | 853 | Get record |
| `/api/noa/{noa_id}/pdf` | GET | broker_invoice.py | 860 | Get record |
| `/api/noa/{noa_id}/send` | POST | delivered_load_autofire.py | 499 | Execute action |
| `/api/ops/pnl-state` | GET | server.py | 10537 | Get record |
| `/api/outbound-autobid/config` | GET | outbound_autobid.py | 215 | Get record |
| `/api/outbound-autobid/config` | PUT | outbound_autobid.py | 219 | Update record |
| `/api/outbound-autobid/log` | GET | outbound_autobid.py | 227 | Get record |
| `/api/payouts/switchboard/log` | GET | payment_switchboard.py | 327 | Get record |
| `/api/payouts/switchboard/route/{load_id}` | POST | payment_switchboard.py | 332 | Execute action |
| `/api/payouts/switchboard/status` | GET | payment_switchboard.py | 311 | Health/status check |
| `/api/privacy/delete` | DELETE | enrichment\router.py | 120 | Delete record |
| `/api/privacy/mode` | PUT | privacy_hardware.py | 133 | Update record |
| `/api/privacy/status` | GET | privacy_hardware.py | 129 | Health/status check |
| `/api/public/carrier-offer/{token}` | GET | carrier_outreach.py | 387 | Get record |
| `/api/public/carrier-offer/{token}/accept` | POST | carrier_outreach.py | 546 | Execute action |
| `/api/public/carrier-offer/{token}/advance-status` | POST | carrier_outreach.py | 1253 | Health/status check |
| `/api/public/carrier-offer/{token}/consignee-confirm` | POST | consignee_confirmation.py | 199 | Execute action |
| `/api/public/carrier-offer/{token}/consignee-confirm-status` | GET | consignee_confirmation.py | 177 | Health/status check |
| `/api/public/carrier-offer/{token}/empty-truck-post` | POST | carrier_outreach.py | 1563 | Execute action |
| `/api/public/carrier-offer/{token}/gps-ping` | POST | carrier_outreach.py | 1108 | Health/status check |
| `/api/public/carrier-offer/{token}/pod-status` | GET | carrier_outreach.py | 1143 | Health/status check |
| `/api/public/carrier-offer/{token}/pod-upload` | POST | carrier_outreach.py | 1062 | Execute action |
| `/api/public/carrier-offer/{token}/post-delivery` | GET | carrier_outreach.py | 1400 | Get record |
| `/api/public/carrier-offer/{token}/relinquish` | POST | carrier_outreach.py | 1166 | Execute action |
| `/api/public/carrier-offer/{token}/report-incident` | POST | carrier_outreach.py | 1621 | Execute action |
| `/api/public/carrier-offer/{token}/status` | POST | carrier_outreach.py | 885 | Health/status check |
| `/api/public/carrier-offer/{token}/suggest-next-loads` | GET | carrier_outreach.py | 1382 | Get record |
| `/api/public/carrier-offer/{token}/synthetic-docs` | GET | carrier_outreach.py | 530 | Get record |
| `/api/public/download/chl-coin-hero.png` | GET | server.py | 11619 | Get record |
| `/api/public/download/chl-coin.icns` | GET | server.py | 11630 | Get record |
| `/api/public/download/chl-coin.png` | GET | server.py | 11608 | Get record |
| `/api/public/download/icon-pack.zip` | GET | server.py | 11597 | Get record |
| `/api/public/loads/{load_id}/synthetic-docs` | GET | carrier_outreach.py | 540 | Get record |
| `/api/public/rate-cons/{rc_id}/sign` | POST | rate_con.py | 495 | Execute action |
| `/api/rate-cons/{rc_id}` | GET | rate_con.py | 458 | Get record |
| `/api/rate-cons/{rc_id}/pdf` | GET | rate_con.py | 466 | Get record |
| `/api/rate-cons/{rc_id}/sign` | POST | rate_con.py | 484 | Execute action |
| `/api/records-request` | POST | records_request_router.py | 256 | Execute action |
| `/api/replies/by-log/{log_id}` | GET | resend_inbound_webhook.py | 258 | Get record |
| `/api/replies/recent` | GET | resend_inbound_webhook.py | 232 | Get record |
| `/api/replies/unmatched` | GET | resend_inbound_webhook.py | 249 | Get record |
| `/api/tracking/checkins` | GET | tracking\checkin_router.py | 85 | Get record |
| `/api/tracking/checkins/recent` | GET | tracking\checkin_router.py | 122 | Get record |
| `/api/tracking/checkins/{id}/cancel` | POST | tracking\checkin_router.py | 162 | Execute action |
| `/api/tracking/events` | POST | tracking\router.py | 152 | Execute action |
| `/api/webhook/stripe` | POST | server.py | 4039 | Webhook handler |
| `/api/webhooks/resend-inbound` | POST | resend_inbound_webhook.py | 198 | Webhook handler |
| `/append` | POST | agent_journal.py | 158 | Execute action |
| `/assign/{load_id}` | PUT | factor_api_integration.py | 555 | Update record |
| `/attempts` | GET | notification_service.py | 659 | Get record |
| `/audit` | GET | driver_preferences.py | 382 | Get record |
| `/audit` | GET | manual_coverage.py | 342 | Get record |
| `/audit` | GET | manual_override_router.py | 138 | Get record |
| `/audit` | GET | vault.py | 238 | Get record |
| `/audit-export` | GET | compliance.py | 388 | Get record |
| `/audit-loads` | GET | broker_authority.py | 321 | Get record |
| `/audit-log` | GET | system_health.py | 186 | Get record |
| `/audit/{load_id}` | GET | load_dispatch_lock.py | 380 | Get record |
| `/audit/{load_id}` | GET | prevet_audit.py | 298 | Get record |
| `/audit_trail/{collection}/{doc_id}` | GET | integrity\integrity_router.py | 142 | Get record |
| `/authority-lost/tick` | POST | failure_detectors.py | 275 | Execute action |
| `/auto-pair/email-suggestions` | POST | factor_autopair.py | 468 | Execute action |
| `/auto-pair/funnel` | GET | factor_autopair.py | 588 | Get record |
| `/auto-pair/preview` | GET | factor_autopair.py | 438 | Get record |
| `/auto-pair/settings` | GET | factor_autopair.py | 403 | Get record |
| `/auto-pair/settings` | PUT | factor_autopair.py | 407 | Update record |
| `/auto-pair/suggest` | GET | factor_autopair.py | 418 | Get record |
| `/auto-pair/track/{token}` | GET | factor_autopair.py | 554 | Get record |
| `/auto-watch` | POST | bmc84_watcher.py | 183 | Execute action |
| `/automations/1099/pdf/{carrier_user_id}` | GET | automations.py | 1031 | Get record |
| `/automations/1099/summary` | GET | automations.py | 999 | Get record |
| `/automations/bol/{load_id}` | POST | automations.py | 699 | Execute action |
| `/automations/checkin/trigger` | POST | automations.py | 887 | Execute action |
| `/automations/documents/load/{load_id}` | GET | automations.py | 739 | Get record |
| `/automations/documents/{document_id}/pdf` | GET | automations.py | 723 | Get record |
| `/automations/email-intake` | POST | automations.py | 749 | Execute action |
| `/automations/email-intake/{intake_id}/create-load` | POST | automations.py | 826 | Create record |
| `/automations/invoice/{invoice_id}/pdf` | GET | automations.py | 640 | Get record |
| `/automations/invoice/{pending_payment_id}` | POST | automations.py | 570 | Execute action |
| `/automations/pipeline-status` | GET | automations.py | 1131 | Health/status check |
| `/automations/rate-con/{load_id}` | POST | automations.py | 665 | Execute action |
| `/automations/vet-carrier-user/{carrier_user_id}` | POST | automations.py | 975 | Execute action |
| `/automations/vet/{mc_or_dot}` | GET | automations.py | 963 | Get record |
| `/backhaul/list` | GET | carrier_hub.py | 376 | List records |
| `/backhaul/post` | POST | carrier_hub.py | 341 | Execute action |
| `/backhaul/{lane_id}/book` | POST | carrier_hub.py | 396 | Execute action |
| `/backhaul/{lane_id}/cancel` | POST | carrier_hub.py | 414 | Execute action |
| `/banker-digest` | GET | night_watchman.py | 364 | Get record |
| `/bankruptcy/tick` | POST | failure_detectors_part2.py | 731 | Execute action |
| `/blacklist-mc` | POST | load_intake.py | 401 | List records |
| `/blast` | POST | bulk_outreach_blast.py | 386 | Execute action |
| `/blast/click/{token}` | GET | bulk_outreach_blast.py | 463 | Get record |
| `/blast/funnel` | GET | bulk_outreach_blast.py | 504 | Get record |
| `/blast/log` | GET | bulk_outreach_blast.py | 413 | Get record |
| `/blast/open/{token}.gif` | GET | bulk_outreach_blast.py | 430 | Get record |
| `/blast/{mc_number}` | POST | bulk_outreach_blast.py | 373 | Execute action |
| `/boc3` | POST | renewals\renewals_router.py | 648 | Execute action |
| `/boc3/calendar` | GET | renewals\renewals_router.py | 613 | Get record |
| `/boc3/jurisdiction` | POST | renewals\renewals_router.py | 706 | Execute action |
| `/boc3/jurisdiction/{state_code}` | DELETE | renewals\renewals_router.py | 742 | Delete record |
| `/boc3/{designation_id}` | DELETE | renewals\renewals_router.py | 685 | Delete record |
| `/bol-mismatch/tick` | POST | failure_detectors_part2.py | 715 | Execute action |
| `/book/preview/{load_id}` | POST | load_dispatch_lock.py | 360 | Execute action |
| `/book/{load_id}` | POST | load_dispatch_lock.py | 312 | Execute action |
| `/briefing` | GET | boot_briefing.py | 387 | Get record |
| `/browser-incoming` | POST | browser_phone.py | 192 | Execute action |
| `/browser-incoming` | POST | telnyx_webrtc.py | 564 | Execute action |
| `/browser-outgoing` | POST | browser_phone.py | 129 | Execute action |
| `/browser-outgoing` | POST | telnyx_webrtc.py | 497 | Execute action |
| `/browser-phone-config` | GET | browser_phone.py | 253 | Get record |
| `/browser-phone-config` | GET | telnyx_webrtc.py | 645 | Get record |
| `/browser-status` | POST | browser_phone.py | 238 | Health/status check |
| `/browser-status` | POST | telnyx_webrtc.py | 627 | Health/status check |
| `/browser-token` | POST | browser_phone.py | 79 | Execute action |
| `/browser-token` | POST | telnyx_webrtc.py | 449 | Execute action |
| `/buckets` | GET | aged_ar.py | 77 | Get record |
| `/budgets` | GET | integrity\integrity_router.py | 126 | Get record |
| `/bulk-apply-defaults` | POST | load_schema_v2.py | 572 | Execute action |
| `/bulk-apply-defaults/preview` | GET | load_schema_v2.py | 536 | Get record |
| `/bulk-promote` | POST | carrier_prevet.py | 371 | Execute action |
| `/bulk-run` | POST | carrier_prevet.py | 306 | Execute action |
| `/calendar/business-day-add` | GET | system_clock.py | 303 | Get record |
| `/calendar/events` | GET | system_clock.py | 370 | Get record |
| `/calendar/events` | POST | system_clock.py | 391 | Execute action |
| `/calendar/events/{event_id}` | DELETE | system_clock.py | 417 | Delete record |
| `/calendar/holidays` | GET | system_clock.py | 293 | Get record |
| `/calendar/month` | GET | system_clock.py | 344 | Get record |
| `/call-history/by-load/{load_id}` | GET | browser_phone.py | 264 | Get record |
| `/cap` | POST | load_throttle.py | 104 | Execute action |
| `/capabilities` | GET | broker_authority.py | 285 | Get record |
| `/capabilities` | PUT | broker_authority.py | 290 | Update record |
| `/captured-fee` | GET | liquidity_provider.py | 577 | Get record |
| `/captured-margin` | GET | finance_strategy.py | 324 | Get record |
| `/cargo-claim/open` | POST | failure_detectors_part2.py | 770 | Execute action |
| `/cargo-claim/{claim_id}/packet/generate` | POST | collections_packets.py | 312 | Execute action |
| `/cargo-claim/{claim_id}/packet/send` | POST | collections_packets.py | 324 | Execute action |
| `/cargo-claims/list` | GET | failure_detectors_part2.py | 791 | List records |
| `/cargo-claims/{claim_id}/resolve` | POST | failure_detectors_part2.py | 799 | Execute action |
| `/carrier-leaderboard` | GET | performance.py | 221 | Get record |
| `/carrier/stripe/onboard` | POST | stripe_connect.py | 237 | Execute action |
| `/carrier/stripe/status` | GET | stripe_connect.py | 260 | Health/status check |
| `/carrier/{carrier_id}` | GET | visibility_bonus.py | 320 | Get record |
| `/carrier/{carrier_id}/scorecard` | GET | scorecard.py | 22 | Get record |
| `/carrier/{mc_number}` | GET | carrier_hub.py | 240 | Get record |
| `/carriers` | GET | auto_dispatch.py | 328 | Get record |
| `/carriers/{carrier_id}` | POST | auto_dispatch.py | 344 | Execute action |
| `/categories` | GET | hauler_types\hauler_lists_router.py | 210 | Get record |
| `/charges` | GET | detention\detention_router.py | 46 | Get record |
| `/check` | GET | factor_credit_lookup.py | 99 | Get record |
| `/check-can-book` | GET | load_throttle.py | 93 | Get record |
| `/check-load` | POST | shipper_credit_headroom.py | 230 | Execute action |
| `/checklist` | GET | phase2_readiness.py | 245 | List records |
| `/checklist/{item_key}` | PUT | phase2_readiness.py | 267 | List records |
| `/circuit_breakers` | GET | integrity\integrity_router.py | 62 | Get record |
| `/circuit_breakers/{vendor}/reset` | POST | integrity\integrity_router.py | 74 | Execute action |
| `/claim/{load_id}` | POST | load_dispatch_lock.py | 240 | Execute action |
| `/classify` | POST | broker_authority.py | 312 | Execute action |
| `/cleanup` | POST | stress_test.py | 189 | Execute action |
| `/cleanup` | POST | synthetic_load_replay.py | 487 | Execute action |
| `/collections-packets/recent` | GET | collections_packets.py | 360 | Get record |
| `/complete` | POST | day1_walkthrough.py | 305 | Execute action |
| `/complete-and-send-bca` | POST | carrier_vetting_completion.py | 117 | Execute action |
| `/compliance-watcher/tick` | POST | failure_detectors_part2.py | 781 | Execute action |
| `/config` | GET | aether_governor.py | 363 | Get record |
| `/config` | GET | auto_dispatch.py | 303 | Get record |
| `/config` | GET | eld_integration.py | 337 | Get record |
| `/config` | GET | finance_strategy.py | 290 | Get record |
| `/config` | GET | hardware_sentinel.py | 470 | Get record |
| `/config` | GET | load_intake.py | 502 | Get record |
| `/config` | GET | market_intel.py | 232 | Get record |
| `/config` | GET | profit_split.py | 428 | Get record |
| `/config` | GET | rfq_imap_poller.py | 252 | Get record |
| `/config` | GET | volume_throttle.py | 260 | Get record |
| `/config` | PUT | aether_governor.py | 367 | Update record |
| `/config` | PUT | auto_dispatch.py | 312 | Update record |
| `/config` | PUT | hardware_sentinel.py | 474 | Update record |
| `/config` | PUT | market_intel.py | 236 | Update record |
| `/config` | PUT | profit_split.py | 432 | Update record |
| `/config` | PUT | rfq_imap_poller.py | 267 | Update record |
| `/config` | PUT | volume_throttle.py | 264 | Update record |
| `/contacts` | GET | telnyx_webrtc.py | 736 | Get record |
| `/contacts` | POST | telnyx_webrtc.py | 839 | Execute action |
| `/contacts/{contact_id}` | DELETE | telnyx_webrtc.py | 871 | Delete record |
| `/conversion-dashboard` | GET | bulk_conversion_dashboard.py | 47 | Get record |
| `/corridor-config` | GET | multimodal_override.py | 512 | Get record |
| `/counter-offer` | POST | load_intake.py | 441 | Execute action |
| `/create` | POST | synthetic_load_replay.py | 375 | Create record |
| `/credentials/{slug}` | DELETE | factor_api_integration.py | 527 | Delete record |
| `/credentials/{slug}` | PUT | factor_api_integration.py | 505 | Update record |
| `/credentials/{slug}/test` | POST | factor_api_integration.py | 533 | Execute action |
| `/credit-check` | POST | auto_credit_check.py | 172 | Execute action |
| `/credit-check` | POST | factor_matrix.py | 1486 | Execute action |
| `/critical-active` | GET | telemetry.py | 137 | Get record |
| `/cron` | POST | banker_digest.py | 1420 | Execute action |
| `/cron/prepickup-tick` | POST | prevet_audit.py | 248 | Execute action |
| `/dashboard` | GET | mercury_client.py | 187 | Get record |
| `/dashboard` | GET | cost_monitor\cost_router.py | 61 | Get record |
| `/data` | GET | business_plan.py | 238 | Get record |
| `/deactivate` | POST | phase2_readiness.py | 335 | Execute action |
| `/decision-matrix` | POST | load_intake.py | 371 | Execute action |
| `/demand-letter/{invoice_id}/generate` | POST | collections_packets.py | 267 | Execute action |
| `/demand-letter/{invoice_id}/send` | POST | collections_packets.py | 277 | Execute action |
| `/demote` | POST | failover\failover_router.py | 110 | Execute action |
| `/detention-disputes/list` | GET | failure_detectors_part2.py | 845 | List records |
| `/detention-disputes/{load_id}/resolve` | POST | failure_detectors_part2.py | 856 | Execute action |
| `/detention/tick` | POST | failure_detectors_part2.py | 719 | Execute action |
| `/dev_review_queue/bulk_reject` | POST | self_healing\dev_review_alerts_router.py | 714 | Execute action |
| `/dev_review_queue/pending` | GET | self_healing\dev_review_alerts_router.py | 120 | Get record |
| `/dev_review_queue/{review_id}/approve` | POST | self_healing\dev_review_alerts_router.py | 173 | Execute action |
| `/dev_review_queue/{review_id}/reject` | POST | self_healing\dev_review_alerts_router.py | 335 | Execute action |
| `/dial-search` | GET | dial_search.py | 52 | Get record |
| `/digest` | GET | client_error_digest_router.py | 22 | Get record |
| `/digest` | GET | system_health_digest_router.py | 23 | Get record |
| `/digest` | GET | self_healing\anomaly_digest_router.py | 29 | Get record |
| `/digest-log` | GET | saved_searches.py | 295 | Get record |
| `/digest/log` | GET | profit_split.py | 504 | Get record |
| `/digest/send-now` | POST | profit_split.py | 490 | Execute action |
| `/disarm-all` | POST | kill_switch.py | 87 | Execute action |
| `/dismiss` | POST | day1_walkthrough.py | 272 | Execute action |
| `/dispatch-link` | POST | system_health.py | 299 | Execute action |
| `/documents/{document_id}/acknowledge` | POST | compliance.py | 544 | Execute action |
| `/double-broker-flags/list` | GET | failure_detectors_part2.py | 869 | List records |
| `/double-broker-flags/{load_id}/resolve` | POST | failure_detectors_part2.py | 880 | Execute action |
| `/double-broker/tick` | POST | failure_detectors_part2.py | 727 | Execute action |
| `/draft` | POST | banker_digest.py | 1207 | Execute action |
| `/draft/{draft_id}` | GET | banker_digest.py | 1253 | Get record |
| `/draft/{draft_id}` | PATCH | banker_digest.py | 1260 | Update record |
| `/draft/{draft_id}/approve` | POST | banker_digest.py | 1292 | Execute action |
| `/draft/{draft_id}/discard` | POST | banker_digest.py | 1353 | Execute action |
| `/draft/{draft_id}/pdf` | GET | banker_digest.py | 1495 | Get record |
| `/drafts` | GET | banker_digest.py | 1238 | Get record |
| `/driver-presence` | GET | system_health.py | 255 | Get record |
| `/driver/{driver_id}/messages` | GET | command_control.py | 494 | Get record |
| `/driver/{driver_id}/messages/{message_id}/read` | POST | command_control.py | 506 | Execute action |
| `/drivers/hos` | GET | eld_integration.py | 295 | Get record |
| `/drivers/hos/mock` | POST | eld_integration.py | 315 | Execute action |
| `/drivers/hos/{driver_id}` | DELETE | eld_integration.py | 355 | Delete record |
| `/drivers/{carrier_id}/documents` | POST | compliance.py | 147 | Execute action |
| `/drivers/{carrier_id}/status` | GET | compliance.py | 263 | Health/status check |
| `/eld_check` | POST | eld_fleet_size_check.py | 595 | Execute action |
| `/eld_check/_health` | GET | eld_fleet_size_check.py | 613 | Health/status check |
| `/email` | POST | owner_resume.py | 180 | Execute action |
| `/email/{email_id:path}` | GET | inbound_email\inbox_router.py | 621 | Get record |
| `/entries` | GET | vault.py | 117 | Get record |
| `/entries` | POST | vault.py | 136 | Execute action |
| `/entries/{entry_id}` | DELETE | vault.py | 190 | Delete record |
| `/entries/{entry_id}` | GET | vault.py | 156 | Get record |
| `/entries/{entry_id}` | PUT | vault.py | 174 | Update record |
| `/error` | POST | telemetry.py | 52 | Execute action |
| `/eta-slip/list` | GET | failure_detectors_part2.py | 748 | List records |
| `/eta-slip/tick` | POST | failure_detectors_part2.py | 743 | Execute action |
| `/eta-slip/{load_id}/resolve` | POST | failure_detectors_part2.py | 757 | Execute action |
| `/evaluate` | POST | multimodal_override.py | 560 | Execute action |
| `/event` | POST | factor_performance.py | 274 | Execute action |
| `/events` | DELETE | playback.py | 117 | Delete record |
| `/events` | GET | carrier_vetting_completion.py | 251 | Get record |
| `/events` | GET | gap_close_watcher.py | 459 | Get record |
| `/events` | GET | hardware_sentinel.py | 481 | Get record |
| `/events` | GET | observability.py | 476 | Get record |
| `/events` | GET | playback.py | 76 | Get record |
| `/events/{submission_id}` | GET | factor_performance.py | 317 | Get record |
| `/exceptions` | GET | ghost_visibility.py | 428 | Get record |
| `/execute` | POST | broker_carrier_agreement.py | 409 | Execute action |
| `/execution` | POST | throughput_governor.py | 424 | Execute action |
| `/export` | POST | vault.py | 197 | Execute action |
| `/external-loads/adopt` | POST | auto_load_pipeline.py | 300 | Execute action |
| `/factor-decline/handle` | POST | failure_detectors.py | 287 | Execute action |
| `/factors` | GET | factor_api_integration.py | 463 | Get record |
| `/feature-state` | GET | driver_preferences.py | 358 | Get record |
| `/feature-state` | PATCH | driver_preferences.py | 362 | Update record |
| `/first-live-load` | GET | first_live_load.py | 133 | Get record |
| `/first-live-load/reset` | POST | first_live_load.py | 138 | Execute action |
| `/flags` | GET | reconciliation.py | 140 | Get record |
| `/flags/{mercury_txn_id}/action` | POST | reconciliation.py | 154 | Execute action |
| `/fleet/driver` | POST | carrier_hub.py | 308 | Execute action |
| `/fleet/drivers/{mc_number}` | GET | carrier_hub.py | 334 | Get record |
| `/folders` | GET | inbound_email\inbox_router.py | 469 | Get record |
| `/followups/{followup_id}/complete` | POST | inquiry_pipeline.py | 411 | Execute action |
| `/followups/{followup_id}/email-rate-con` | POST | inquiry_pipeline.py | 423 | Execute action |
| `/force-close` | POST | hardware_sentinel.py | 503 | Execute action |
| `/force-trip` | POST | hardware_sentinel.py | 490 | Execute action |
| `/forecast` | GET | cost_monitor\cost_router.py | 141 | Get record |
| `/foresight` | GET | aether_governor.py | 387 | Get record |
| `/freshness` | GET | carriers_freshness_router.py | 80 | Get record |
| `/friday-digest-candidates` | GET | observability.py | 591 | Get record |
| `/from-event` | POST | inquiry_pipeline.py | 168 | Execute action |
| `/funding-trapped` | GET | liquidity_provider.py | 573 | Get record |
| `/fx/rate` | GET | multimodal.py | 266 | Get record |
| `/fx/refresh` | POST | multimodal.py | 270 | Execute action |
| `/generate` | POST | investor_one_pager.py | 720 | Execute action |
| `/ghosting/tick` | POST | failure_detectors_part2.py | 723 | Execute action |
| `/github_actions_webhook` | POST | ci_intake\github_actions_router.py | 251 | Webhook handler |
| `/governor/nudges` | GET | aether_governor.py | 356 | Get record |
| `/governor/state` | GET | aether_governor.py | 340 | Get record |
| `/governor/tick` | POST | aether_governor.py | 352 | Execute action |
| `/gps-silent/tick` | POST | failure_detectors.py | 279 | Execute action |
| `/handshake-status` | GET | loadboards.py | 1580 | Health/status check |
| `/headroom` | GET | shipper_credit_headroom.py | 162 | Get record |
| `/health` | GET | observability.py | 460 | Health/status check |
| `/health` | GET | system_health.py | 61 | Health/status check |
| `/health` | GET | cost_monitor\cost_router.py | 199 | Health/status check |
| `/heartbeat` | DELETE | compliance.py | 530 | Delete record |
| `/heartbeat` | GET | system_health.py | 198 | Get record |
| `/heartbeat` | POST | compliance.py | 493 | Execute action |
| `/history` | GET | banker_digest.py | 1378 | Get record |
| `/history` | GET | factor_credit_lookup.py | 186 | Get record |
| `/history` | GET | investor_one_pager.py | 847 | Get record |
| `/history` | GET | night_watchman.py | 316 | Get record |
| `/history` | GET | throttle_system.py | 485 | Get record |
| `/history` | GET | weekend_reset_cron.py | 183 | Get record |
| `/identity` | GET | company_identity.py | 86 | Get record |
| `/identity` | GET | posting_blob.py | 164 | Get record |
| `/identity` | PUT | company_identity.py | 140 | Update record |
| `/identity/activate` | POST | company_identity.py | 163 | Execute action |
| `/ingest-csv` | POST | canadian_carriers.py | 395 | Execute action |
| `/issue-token` | POST | ghost_visibility.py | 411 | Execute action |
| `/lane-overrides` | GET | market_sentinel.py | 264 | Get record |
| `/lane-stats` | GET | driver_preferences.py | 372 | Get record |
| `/lane/{equipment}/{state}` | GET | seo_landers.py | 173 | Get record |
| `/lanes` | GET | seo_landers.py | 158 | Get record |
| `/last` | GET | day1_readiness_email.py | 478 | Get record |
| `/last` | GET | match_dry_run.py | 524 | Get record |
| `/last-run` | GET | night_watchman.py | 307 | Get record |
| `/last-run` | GET | weekend_reset_cron.py | 178 | Get record |
| `/last-send.html` | GET | banker_digest.py | 1434 | Get record |
| `/last-send.pdf` | GET | banker_digest.py | 1479 | Get record |
| `/last.pdf` | GET | investor_one_pager.py | 859 | Get record |
| `/latest` | GET | live_wire_diary.py | 200 | Get record |
| `/latest` | GET | morning_brief.py | 165 | Get record |
| `/ledger` | GET | cost_monitor\cost_router.py | 92 | Get record |
| `/limit` | POST | shipper_credit_headroom.py | 322 | Execute action |
| `/list` | GET | aged_ar.py | 111 | List records |
| `/list` | GET | agent_journal.py | 185 | List records |
| `/list` | GET | broker_carrier_agreement.py | 391 | List records |
| `/list` | GET | canadian_carriers.py | 426 | List records |
| `/list` | GET | carrier_hub.py | 214 | List records |
| `/list` | GET | mercury_payouts.py | 210 | List records |
| `/list` | GET | shipper_credit_headroom.py | 300 | List records |
| `/list` | GET | shipper_scorecard.py | 482 | List records |
| `/list` | GET | synthetic_load_replay.py | 458 | List records |
| `/list` | GET | inbound_email\inbox_router.py | 477 | List records |
| `/load-status/{load_id}` | POST | manual_override.py | 52 | Health/status check |
| `/load/{load_id}` | GET | multimodal_override.py | 553 | Get record |
| `/load/{load_id}` | GET | posting_blob.py | 182 | Get record |
| `/load/{load_id}` | GET | sop_engine.py | 437 | Get record |
| `/load/{load_id}` | GET | visibility_bonus.py | 289 | Get record |
| `/load/{load_id}` | POST | finance_strategy.py | 318 | Execute action |
| `/load/{load_id}/advance` | POST | sop_engine.py | 470 | Execute action |
| `/loads` | GET | sop_engine.py | 376 | Get record |
| `/loads/{load_id}/accessorials` | GET | required_tasks.py | 480 | Get record |
| `/loads/{load_id}/accessorials` | PUT | required_tasks.py | 498 | Update record |
| `/loads/{load_id}/assign` | POST | liquidity_provider.py | 500 | Execute action |
| `/loads/{load_id}/auto-repost` | GET | auto_repost_routes.py | 33 | Get record |
| `/loads/{load_id}/auto-repost` | PUT | auto_repost_routes.py | 58 | Update record |
| `/loads/{load_id}/auto-repost/disable` | POST | auto_repost_routes.py | 87 | Execute action |
| `/loads/{load_id}/auto-repost/fire-now` | POST | auto_repost_routes.py | 99 | Execute action |
| `/loads/{load_id}/billing-summary` | GET | billing_automation.py | 479 | Get record |
| `/loads/{load_id}/build-packets` | POST | billing_automation.py | 484 | Execute action |
| `/loads/{load_id}/detention` | GET | billing_automation.py | 467 | Get record |
| `/loads/{load_id}/events` | POST | billing_automation.py | 391 | Execute action |
| `/loads/{load_id}/funding` | GET | liquidity_provider.py | 535 | Get record |
| `/loads/{load_id}/funding` | POST | liquidity_provider.py | 539 | Execute action |
| `/loads/{load_id}/refusal-guard` | GET | factor_refusal_guard.py | 592 | Get record |
| `/loads/{load_id}/remit-to` | GET | liquidity_provider.py | 527 | Get record |
| `/loads/{load_id}/variance` | GET | billing_automation.py | 473 | Get record |
| `/loads/{load_ref}` | GET | command_control.py | 208 | Get record |
| `/loads/{load_ref}/comms-history` | GET | command_control.py | 519 | Get record |
| `/loads/{load_ref}/dispatcher-message` | POST | command_control.py | 338 | Execute action |
| `/loads/{load_ref}/emergency-contact` | POST | command_control.py | 409 | Execute action |
| `/loads/{load_ref}/lock-status` | GET | compliance.py | 480 | Health/status check |
| `/loads/{load_ref}/verify-documents` | POST | compliance.py | 290 | Execute action |
| `/log` | GET | auto_dispatch.py | 380 | Get record |
| `/log` | GET | manual_override.py | 84 | Get record |
| `/lookup` | GET | lane_rate_history.py | 132 | Get record |
| `/lookup/by-name` | GET | shipper_scorecard.py | 475 | Get record |
| `/lumper/tick` | POST | failure_detectors_part2.py | 735 | Execute action |
| `/magic-link` | POST | notification_service.py | 602 | Execute action |
| `/manual` | PUT | factor_credit_lookup.py | 153 | Update record |
| `/manual-attribute` | POST | factors\reconciler_router.py | 80 | Execute action |
| `/manual-pause` | POST | volume_throttle.py | 271 | Execute action |
| `/manual-sms` | POST | notification_service.py | 631 | Execute action |
| `/map` | GET | sop_engine.py | 355 | Get record |
| `/mca/sign` | POST | carrier_hub.py | 254 | Execute action |
| `/mca/template` | GET | carrier_hub.py | 250 | Get record |
| `/me` | GET | driver_preferences.py | 308 | Get record |
| `/me` | PATCH | driver_preferences.py | 313 | Update record |
| `/me/hos` | POST | driver_preferences.py | 347 | Execute action |
| `/me/onboarding` | POST | driver_preferences.py | 325 | Execute action |
| `/mercy/{load_id}` | POST | visibility_bonus.py | 338 | Execute action |
| `/metrics` | GET | factor_performance.py | 257 | Get record |
| `/milestone` | POST | ghost_visibility.py | 421 | Execute action |
| `/missing` | GET | factors\reconciler_router.py | 67 | Get record |
| `/mode` | GET | load_intake.py | 430 | Get record |
| `/modules` | GET | health_monitor.py | 166 | Get record |
| `/monitor/{load_id}` | GET | field_test_monitor.py | 33 | Get record |
| `/month-end-audit` | GET | multimodal_override.py | 597 | Get record |
| `/multimodal/canadian-tax-config` | GET | multimodal.py | 274 | Get record |
| `/multimodal/canadian-tax-config` | PUT | multimodal.py | 281 | Update record |
| `/multimodal/compute-tax` | POST | multimodal.py | 292 | Execute action |
| `/multimodal/equipment-types` | GET | multimodal.py | 254 | Get record |
| `/multimodal/validate-postal` | GET | multimodal.py | 262 | Get record |
| `/no-show/tick` | POST | failure_detectors.py | 283 | Execute action |
| `/note` | POST | day1_walkthrough.py | 291 | Execute action |
| `/now` | GET | system_clock.py | 283 | Get record |
| `/now/iso` | GET | system_clock.py | 288 | Get record |
| `/operation/{name}` | GET | sla_monitor.py | 388 | Get record |
| `/operational_state` | GET | integrity\integrity_router.py | 134 | Get record |
| `/operator_push_queue/stats` | GET | self_healing\dev_review_alerts_router.py | 578 | Get record |
| `/optin/{token}` | GET | consent_capture.py | 293 | Get record |
| `/orphans` | GET | factors\reconciler_router.py | 53 | Get record |
| `/outcomes` | GET | self_healing\outcome_router.py | 183 | Get record |
| `/outcomes/metrics` | GET | self_healing\outcome_router.py | 139 | Get record |
| `/outcomes/{anomaly_id}` | GET | self_healing\outcome_router.py | 162 | Get record |
| `/outcomes/{anomaly_id}` | POST | self_healing\outcome_router.py | 70 | Execute action |
| `/override` | DELETE | throttle_system.py | 514 | Delete record |
| `/override` | POST | pre_authority.py | 221 | Execute action |
| `/override` | POST | throttle_system.py | 494 | Execute action |
| `/override-log` | GET | pre_authority.py | 263 | Get record |
| `/parse` | POST | load_intake.py | 336 | Execute action |
| `/parse-text` | POST | rfq_inbound.py | 395 | Execute action |
| `/patch_proposals` | GET | self_healing\patch_proposal_router.py | 76 | Get record |
| `/patch_proposals/{anomaly_id}/approve` | POST | self_healing\patch_proposal_router.py | 165 | Execute action |
| `/patch_proposals/{anomaly_id}/reject` | POST | self_healing\patch_proposal_router.py | 233 | Execute action |
| `/pdf` | GET | business_plan.py | 242 | Get record |
| `/pdf` | GET | owner_resume.py | 165 | Get record |
| `/pending-review` | GET | compliance.py | 355 | Get record |
| `/poll` | POST | bmc84_watcher.py | 164 | Execute action |
| `/poll-now` | POST | rfq_imap_poller.py | 278 | Execute action |
| `/posters/ar-positions` | GET | factor_refusal_guard.py | 630 | Get record |
| `/posters/top-by-ar` | GET | factor_refusal_guard.py | 654 | Get record |
| `/posters/{poster_mc}/ar-headroom` | GET | factor_refusal_guard.py | 622 | Get record |
| `/prepickup-due` | GET | prevet_audit.py | 167 | Get record |
| `/prepickup-run/{load_id}` | POST | prevet_audit.py | 187 | Execute action |
| `/preview` | GET | banker_digest.py | 1196 | Get record |
| `/preview` | GET | day1_readiness_email.py | 471 | Get record |
| `/preview` | GET | market_intel.py | 249 | Get record |
| `/preview` | POST | profit_split.py | 457 | Execute action |
| `/preview-payload` | GET | trust_footprint.py | 340 | Get record |
| `/preview.html` | GET | investor_one_pager.py | 749 | Get record |
| `/preview.pdf` | GET | banker_digest.py | 1469 | Get record |
| `/preview.pdf` | GET | investor_one_pager.py | 763 | Get record |
| `/promote` | POST | failover\failover_router.py | 83 | Execute action |
| `/promote/{mc}` | POST | carrier_prevet.py | 348 | Execute action |
| `/protest-countdown` | GET | company_identity.py | 94 | Get record |
| `/providers` | GET | factor_matrix.py | 1395 | Get record |
| `/providers` | GET | liquidity_provider.py | 458 | Get record |
| `/providers` | POST | liquidity_provider.py | 465 | Execute action |
| `/providers/seed` | POST | factor_matrix.py | 1422 | Execute action |
| `/providers/seed` | POST | liquidity_provider.py | 495 | Execute action |
| `/providers/{provider_id}` | PUT | liquidity_provider.py | 480 | Update record |
| `/public/load-link/{load_ref}` | GET | command_control.py | 183 | Get record |
| `/publish` | POST | investor_one_pager.py | 783 | Execute action |
| `/pulse` | GET | aether_governor.py | 333 | Get record |
| `/push-all` | POST | trust_footprint.py | 364 | Execute action |
| `/push-to-factor` | POST | factor_matrix.py | 1433 | Execute action |
| `/push/{partner_slug}` | POST | trust_footprint.py | 345 | Execute action |
| `/queue` | GET | bulk_followup_sequencer.py | 431 | Get record |
| `/queue` | GET | factors\factor_submission_router.py | 57 | Get record |
| `/quote-accept-gate` | POST | margin_floor_router.py | 60 | Execute action |
| `/quote/{quote_id}/regenerate-draft` | POST | auto_quote_draft.py | 220 | Execute action |
| `/quote/{quote_id}/send-draft` | POST | auto_quote_draft.py | 226 | Execute action |
| `/quotes/{quote_id}/accept` | POST | auto_load_pipeline.py | 175 | Execute action |
| `/ramp-history` | GET | throughput_governor.py | 416 | Get record |
| `/rank` | POST | load_intake.py | 358 | Execute action |
| `/raw` | GET | manual_coverage.py | 330 | Get record |
| `/rearm-all` | POST | kill_switch.py | 111 | Execute action |
| `/rebuild` | POST | factor_performance.py | 302 | Execute action |
| `/rebuild` | POST | observability.py | 492 | Execute action |
| `/recent` | GET | agent_journal.py | 178 | Get record |
| `/recent` | GET | rfq_inbound.py | 496 | Get record |
| `/recent` | GET | silent_shipper_ping.py | 179 | Get record |
| `/recent` | GET | telemetry.py | 126 | Get record |
| `/recent` | GET | inbound_email\sender_router.py | 165 | Get record |
| `/recent` | GET | inbox_triage\triage_router.py | 75 | Get record |
| `/recent` | GET | self_healing\self_heal_router.py | 52 | Get record |
| `/recent-pulses` | GET | auto_repost_scheduler.py | 249 | Get record |
| `/recent_promotions` | GET | carrier_tier_promoter\promoter_router.py | 46 | Get record |
| `/recent_revocations` | GET | credit_override_revoker\revoker_router.py | 44 | Get record |
| `/recents` | GET | telnyx_webrtc.py | 674 | Get record |
| `/recheck/{load_id}` | POST | margin_floor_router.py | 140 | Execute action |
| `/recheck/{load_id}` | POST | visibility_bonus.py | 348 | Execute action |
| `/recipients` | GET | banker_digest.py | 1386 | Get record |
| `/recognize` | POST | factor_matrix.py | 1428 | Execute action |
| `/record` | POST | playback.py | 112 | Execute action |
| `/red-carpet/run` | POST | compliance.py | 585 | Execute action |
| `/refresh` | POST | mercury_client.py | 217 | Execute action |
| `/refresh` | POST | shipper_credit_headroom.py | 349 | Execute action |
| `/refresh` | POST | tcr_status.py | 291 | Execute action |
| `/register` | POST | carrier_hub.py | 189 | Execute action |
| `/release/{load_id}` | DELETE | load_dispatch_lock.py | 283 | Delete record |
| `/reports` | GET | stress_test.py | 183 | Get record |
| `/required-tasks` | GET | required_tasks.py | 90 | Get record |
| `/reset` | POST | day1_walkthrough.py | 286 | Execute action |
| `/reset-snapshot` | POST | gap_close_watcher.py | 471 | Execute action |
| `/reweigh/tick` | POST | failure_detectors_part2.py | 739 | Execute action |
| `/rfq/{rfq_id}/factor` | GET | factor_api_integration.py | 657 | Get record |
| `/rfq/{rfq_id}/factor` | PUT | factor_api_integration.py | 626 | Update record |
| `/robo-call` | POST | notification_service.py | 613 | Execute action |
| `/rules` | GET | observability.py | 579 | Get record |
| `/run` | POST | stress_test.py | 178 | Execute action |
| `/run-now` | POST | anomaly_sniffer.py | 324 | Execute action |
| `/run-now` | POST | auto_repost_scheduler.py | 242 | Execute action |
| `/run-now` | POST | bulk_followup_sequencer.py | 422 | Execute action |
| `/run-now` | POST | live_wire_diary.py | 206 | Execute action |
| `/run-now` | POST | market_sentinel.py | 274 | Execute action |
| `/run-now` | POST | match_dry_run.py | 509 | Execute action |
| `/run-now` | POST | morning_brief.py | 159 | Execute action |
| `/run-now` | POST | night_watchman.py | 322 | Execute action |
| `/run-now` | POST | profit_first_cron.py | 222 | Execute action |
| `/run-now` | POST | reconciliation.py | 172 | Execute action |
| `/run-now` | POST | weekend_reset_cron.py | 174 | Execute action |
| `/run/{mc}` | POST | carrier_prevet.py | 252 | Execute action |
| `/runs` | GET | profit_first_cron.py | 228 | Get record |
| `/runs` | GET | reconciliation.py | 176 | Get record |
| `/schedule` | GET | banker_digest.py | 1391 | Get record |
| `/schema-completeness/batch` | GET | load_schema_v2.py | 714 | Get record |
| `/schema-completeness/summary` | GET | load_schema_v2.py | 688 | Get record |
| `/scrapers` | GET | scrapers\scrapers_router.py | 57 | Get record |
| `/search` | GET | load_dossier.py | 332 | Get record |
| `/seed-known` | POST | canadian_carriers.py | 367 | Execute action |
| `/seed/midwest-warm-bench` | POST | carrier_prevet.py | 406 | Execute action |
| `/segment/{segment_key}` | GET | bulk_carrier_hunter.py | 241 | Get record |
| `/segments` | GET | bulk_carrier_hunter.py | 230 | Get record |
| `/semaphore/health` | GET | notification_service.py | 951 | Health/status check |
| `/send` | POST | banker_digest.py | 1367 | Execute action |
| `/send-now` | POST | day1_readiness_email.py | 459 | Execute action |
| `/send-now` | POST | market_intel.py | 257 | Execute action |
| `/sentinel/clear` | POST | volume_throttle.py | 294 | Execute action |
| `/sentinel/history` | GET | volume_throttle.py | 302 | Get record |
| `/sentinel/trip` | POST | volume_throttle.py | 285 | Execute action |
| `/sequestered` | GET | throughput_governor.py | 360 | Get record |
| `/sequestered/{sid}/discard` | POST | throughput_governor.py | 387 | Execute action |
| `/sequestered/{sid}/release` | POST | throughput_governor.py | 370 | Execute action |
| `/session-continuity` | GET | agent_journal.py | 202 | Get record |
| `/shadow-banned` | GET | night_watchman.py | 327 | Get record |
| `/share-packet` | POST | share_packet.py | 269 | Execute action |
| `/share-packet/history` | GET | share_packet.py | 353 | Get record |
| `/share-packet/preview` | GET | share_packet.py | 252 | Get record |
| `/shipper-loyalty/lookup` | GET | performance.py | 212 | Get record |
| `/shipper-profiles/{email}` | GET | required_tasks.py | 352 | Get record |
| `/shipper-profiles/{email}` | PUT | required_tasks.py | 407 | Update record |
| `/shipper-profiles/{email}/complete` | POST | required_tasks.py | 452 | Execute action |
| `/shipper/{shipper_id}` | POST | finance_strategy.py | 309 | Execute action |
| `/shippers` | GET | auto_dispatch.py | 359 | Get record |
| `/shippers/{shipper_id}` | POST | auto_dispatch.py | 368 | Execute action |
| `/simulate` | POST | aether_governor.py | 377 | Execute action |
| `/sitemap.xml` | GET | seo_landers.py | 180 | Get record |
| `/skipped_today` | GET | factor_schedule_of_accounts.py | 871 | Get record |
| `/snapshot` | GET | gap_close_watcher.py | 465 | Get record |
| `/sources` | GET | loadboards.py | 1571 | Get record |
| `/spam_review` | GET | inbound_email\sender_router.py | 228 | Get record |
| `/start` | POST | day1_walkthrough.py | 228 | Execute action |
| `/state` | GET | day1_walkthrough.py | 224 | Get record |
| `/state` | GET | failure_detectors.py | 296 | Get record |
| `/state` | GET | hardware_sentinel.py | 450 | Get record |
| `/state` | GET | load_throttle.py | 76 | Get record |
| `/state` | GET | ops_state.py | 272 | Get record |
| `/state` | GET | volume_throttle.py | 256 | Get record |
| `/state-extended` | GET | failure_detectors_part2.py | 904 | Get record |
| `/state/public` | GET | ops_state.py | 276 | Get record |
| `/stats` | GET | bulk_followup_sequencer.py | 466 | Get record |
| `/stats` | GET | canadian_carriers.py | 342 | Get record |
| `/stats` | GET | multimodal_override.py | 521 | Get record |
| `/stats` | GET | playback.py | 85 | Get record |
| `/stats/pending` | GET | mercury_payouts.py | 261 | Get record |
| `/status` | GET | bmc84_watcher.py | 168 | Health/status check |
| `/status` | GET | market_sentinel.py | 250 | Health/status check |
| `/status` | GET | mercury_client.py | 126 | Health/status check |
| `/status` | GET | pre_authority.py | 215 | Health/status check |
| `/status` | GET | profit_first_cron.py | 234 | Health/status check |
| `/status` | GET | rfq_imap_poller.py | 282 | Health/status check |
| `/status` | GET | synthetic_auto_wipe.py | 131 | Health/status check |
| `/status` | GET | tcr_status.py | 287 | Health/status check |
| `/status` | GET | throttle_system.py | 481 | Health/status check |
| `/status` | GET | trust_footprint.py | 336 | Health/status check |
| `/status` | GET | failover\failover_router.py | 76 | Health/status check |
| `/status-for` | GET | notification_service.py | 678 | Health/status check |
| `/status/{load_id}` | GET | load_dispatch_lock.py | 294 | Health/status check |
| `/storage` | GET | lane_rate_history.py | 197 | Get record |
| `/stripe` | GET | stripe_health_router.py | 249 | Get record |
| `/stripe/connect/carriers` | GET | stripe_connect.py | 191 | Get record |
| `/stripe/connect/onboard` | POST | stripe_connect.py | 204 | Execute action |
| `/stripe/connect/pay-carrier` | POST | stripe_connect.py | 305 | Execute action |
| `/stripe/connect/payments` | GET | stripe_connect.py | 372 | Get record |
| `/stripe/connect/pending-payments` | GET | stripe_connect.py | 378 | Get record |
| `/stripe/connect/pending-payments/{pending_id}/assign-carrier` | POST | stripe_connect.py | 493 | Execute action |
| `/stripe/connect/pending-payments/{pending_id}/dismiss` | POST | stripe_connect.py | 474 | Execute action |
| `/stripe/connect/pending-payments/{pending_id}/pay` | POST | stripe_connect.py | 405 | Execute action |
| `/stripe/connect/platform-status` | GET | stripe_connect.py | 166 | Health/status check |
| `/stripe/connect/refresh-link/{carrier_id}` | POST | stripe_connect.py | 288 | Execute action |
| `/stripe/connect/status/{carrier_id}` | GET | stripe_connect.py | 276 | Health/status check |
| `/submissions` | GET | factor_matrix.py | 1442 | Get record |
| `/submissions/{load_id}` | GET | factor_api_integration.py | 617 | Get record |
| `/submissions/{submission_id}/pdf` | GET | factor_matrix.py | 1459 | Get record |
| `/submit` | POST | fast_lane.py | 62 | Execute action |
| `/submit` | POST | manual_override_router.py | 94 | Execute action |
| `/submit/{load_id}` | POST | factor_api_integration.py | 574 | Execute action |
| `/suggest/{shipper_id}` | GET | finance_strategy.py | 305 | Get record |
| `/suggestions` | GET | observability.py | 500 | Get record |
| `/suggestions/for-field` | GET | observability.py | 513 | Get record |
| `/suggestions/{suggestion_id}/decide` | POST | observability.py | 531 | Execute action |
| `/summary` | GET | anomaly_sniffer.py | 290 | Get record |
| `/summary` | GET | carrier_prevet.py | 190 | Get record |
| `/summary` | GET | sla_monitor.py | 368 | Get record |
| `/summary` | GET | sop_engine.py | 411 | Get record |
| `/summary` | GET | renewals\renewals_router.py | 210 | Get record |
| `/system` | GET | health_monitor.py | 160 | Get record |
| `/system-state` | GET | system_state_router.py | 588 | Get record |
| `/targets` | GET | carrier_prevet.py | 202 | Get record |
| `/targets` | POST | carrier_prevet.py | 216 | Execute action |
| `/targets/{mc}` | DELETE | carrier_prevet.py | 244 | Delete record |
| `/technical-advantage` | GET | technical_advantage.py | 137 | Get record |
| `/telemetry` | GET | aether_governor.py | 327 | Get record |
| `/thresholds` | GET | margin_floor_router.py | 55 | Get record |
| `/throttle` | POST | throughput_governor.py | 309 | Execute action |
| `/throttle/state` | GET | throughput_governor.py | 348 | Get record |
| `/tick` | POST | gap_close_watcher.py | 453 | Execute action |
| `/tick` | POST | silent_shipper_ping.py | 175 | Execute action |
| `/tick` | POST | synthetic_auto_wipe.py | 126 | Execute action |
| `/timeline` | GET | notification_service.py | 781 | Get record |
| `/toggle-mode` | POST | load_intake.py | 409 | Execute action |
| `/tollfree-create-new` | POST | tollfree_compliance.py | 364 | Create record |
| `/tollfree-packet` | GET | tollfree_compliance.py | 282 | Get record |
| `/tollfree-resubmit` | POST | tollfree_compliance.py | 328 | Execute action |
| `/tollfree-status` | GET | tollfree_compliance.py | 310 | Health/status check |
| `/top` | GET | lane_pair_targeting.py | 70 | Get record |
| `/track-open` | POST | notification_service.py | 746 | Execute action |
| `/track-open.gif` | GET | notification_service.py | 724 | Get record |
| `/transactions` | GET | mercury_client.py | 162 | Get record |
| `/transfers` | GET | profit_split.py | 449 | Get record |
| `/trigger/{load_id}` | POST | mercury_payouts.py | 231 | Execute action |
| `/trigger/{load_id}` | POST | multimodal_override.py | 580 | Execute action |
| `/trucks` | GET | loadboards.py | 1657 | Get record |
| `/twilio/phone-capabilities` | GET | notification_service.py | 886 | Get record |
| `/unban/{mc_number}` | POST | night_watchman.py | 335 | Execute action |
| `/unsubscribe/{token}` | GET | consent_capture.py | 315 | Get record |
| `/urgent_alerts/active` | GET | self_healing\dev_review_alerts_router.py | 429 | Get record |
| `/urgent_alerts/bulk_ack` | POST | self_healing\dev_review_alerts_router.py | 874 | Execute action |
| `/urgent_alerts/{alert_id}/ack` | POST | self_healing\dev_review_alerts_router.py | 501 | Execute action |
| `/valid-stages` | GET | synthetic_load_replay.py | 515 | Get record |
| `/violations` | GET | sla_monitor.py | 375 | Get record |
| `/voicemail-callback` | POST | telnyx_webrtc.py | 921 | Execute action |
| `/voicemails` | GET | telnyx_webrtc.py | 951 | Get record |
| `/voicemails/{vm_id}/mark_read` | POST | telnyx_webrtc.py | 978 | Execute action |
| `/walkthrough_aggregates` | GET | pipeline\walkthrough_router.py | 365 | Get record |
| `/walkthrough_load/{load_id}` | GET | pipeline\walkthrough_router.py | 537 | Get record |
| `/walkthrough_summary` | GET | pipeline\walkthrough_router.py | 293 | Get record |
| `/webhook` | POST | rfq_inbound.py | 424 | Webhook handler |
| `/webhook/motive` | POST | eld_integration.py | 258 | Webhook handler |
| `/webhook/samsara` | POST | eld_integration.py | 221 | Webhook handler |
| `/webhook/sms/checkin-reply` | POST | automations.py | 916 | Webhook handler |
| `/webhook/stripe-connect` | POST | stripe_connect.py | 522 | Webhook handler |
| `/webhook/voice/agent` | POST | automations.py | 1063 | Webhook handler |
| `/weight_proposals` | GET | self_healing\weight_tuning_router.py | 96 | Get record |
| `/weight_proposals/_health` | GET | self_healing\weight_tuning_router.py | 154 | Health/status check |
| `/weight_proposals/{proposal_id}` | GET | self_healing\weight_tuning_router.py | 184 | Get record |
| `/weight_proposals/{proposal_id}/approve` | POST | self_healing\weight_tuning_router.py | 198 | Execute action |
| `/weight_proposals/{proposal_id}/reject` | POST | self_healing\weight_tuning_router.py | 263 | Execute action |
| `/{action_id}` | GET | self_healing\self_heal_router.py | 107 | Get record |
| `/{carrier_mc}` | GET | broker_carrier_agreement.py | 399 | Get record |
| `/{carrier_mc}/pdf` | GET | broker_carrier_agreement.py | 450 | Get record |
| `/{carrier_mc}/text` | GET | broker_carrier_agreement.py | 435 | Get record |
| `/{category}/carriers` | GET | hauler_types\hauler_lists_router.py | 254 | Get record |
| `/{category}/export.csv` | GET | hauler_types\hauler_lists_router.py | 335 | Get record |
| `/{dispute_id}` | GET | factors\dispute_router.py | 83 | Get record |
| `/{dispute_id}/resolve` | POST | factors\dispute_router.py | 106 | Execute action |
| `/{entry_id}/audio.mp3` | GET | live_wire_diary.py | 211 | Get record |
| `/{event_id}/override_sender_type` | POST | inbound_email\sender_router.py | 281 | Execute action |
| `/{event_id}/resolve` | POST | telemetry.py | 151 | Execute action |
| `/{key}` | DELETE | bulk_copy_variants.py | 107 | Delete record |
| `/{key}` | PUT | bulk_copy_variants.py | 92 | Update record |
| `/{key}/reset` | POST | circuit_breaker.py | 210 | Execute action |
| `/{lane_id}` | GET | public_lanes.py | 82 | Get record |
| `/{load_id}/advance` | POST | synthetic_load_replay.py | 688 | Execute action |
| `/{load_id}/advance-options` | GET | synthetic_load_replay.py | 521 | Get record |
| `/{load_id}/apply-compliance-defaults` | POST | load_schema_v2.py | 511 | Execute action |
| `/{load_id}/approve` | POST | factors\factor_submission_router.py | 94 | Execute action |
| `/{load_id}/compliance` | PUT | load_schema_v2.py | 672 | Update record |
| `/{load_id}/compliance-defaults` | GET | load_schema_v2.py | 487 | Get record |
| `/{load_id}/dat-post` | PUT | load_schema_v2.py | 676 | Update record |
| `/{load_id}/equipment-block` | PUT | load_schema_v2.py | 680 | Update record |
| `/{load_id}/jump` | POST | synthetic_load_replay.py | 722 | Execute action |
| `/{load_id}/schema-completeness` | GET | load_schema_v2.py | 350 | Get record |
| `/{load_id}/vetting-gates` | PUT | load_schema_v2.py | 684 | Update record |
| `/{master_id}` | GET | multi_leg.py | 131 | Get record |
| `/{master_id}/legs/{leg_id}/rate-con` | POST | multi_leg.py | 140 | Execute action |
| `/{master_id}/legs/{leg_id}/status` | PATCH | multi_leg.py | 184 | Health/status check |
| `/{mc_number}/find-loads-suggestions` | GET | carrier_load_match.py | 205 | Get record |
| `/{negotiation_id}` | GET | inquiry_pipeline.py | 207 | Get record |
| `/{negotiation_id}` | PATCH | inquiry_pipeline.py | 214 | Update record |
| `/{negotiation_id}/complete` | POST | inquiry_pipeline.py | 241 | Execute action |
| `/{origin_state}/{destination_state}/carriers` | GET | lane_pair_targeting.py | 113 | Get record |
| `/{payout_id}` | GET | mercury_payouts.py | 224 | Get record |
| `/{payout_id}/mark-paid` | POST | mercury_payouts.py | 247 | Execute action |
| `/{preset_id}` | DELETE | saved_searches.py | 264 | Delete record |
| `/{preset_id}` | PATCH | saved_searches.py | 238 | Update record |
| `/{preset_id}/run-now` | POST | saved_searches.py | 273 | Execute action |
| `/{ref_or_id}` | GET | load_dossier.py | 341 | Get record |
| `/{ref_or_id}/notes` | POST | load_dossier.py | 349 | Execute action |
| `/{renewal_id}` | DELETE | renewals\renewals_router.py | 580 | Delete record |
| `/{renewal_id}` | GET | renewals\renewals_router.py | 279 | Get record |
| `/{renewal_id}` | PATCH | renewals\renewals_router.py | 449 | Update record |
| `/{renewal_id}/mark-renewed` | POST | renewals\renewals_router.py | 384 | Execute action |
| `/{rfq_id}/draft-quote` | POST | auto_quote_draft.py | 186 | Execute action |
| `/{rfq_id}/drafts` | GET | auto_quote_draft.py | 190 | Get record |
| `/{shipper_id}` | GET | shipper_scorecard.py | 503 | Get record |
| `/{shipper_id}/credit` | POST | shipper_scorecard.py | 559 | Execute action |
| `/{shipper_id}/note` | POST | shipper_scorecard.py | 515 | Execute action |
| `/{shipper_id}/note/{note_id}` | DELETE | shipper_scorecard.py | 547 | Delete record |
| `/{token}` | GET | ghost_visibility.py | 362 | Get record |
| `/{token}/qr.png` | GET | ghost_visibility.py | 379 | Get record |
| `/{user_id}/docs` | GET | fast_lane.py | 207 | Get record |

---

## Endpoints by Domain

- **/1099**: 1 endpoints
- **/10dlc**: 2 endpoints
- **/_health**: 6 endpoints
- **/accounts**: 1 endpoints
- **/activate**: 1 endpoints
- **/aggregate**: 1 endpoints
- **/alert-dismiss**: 1 endpoints
- **/alerts**: 3 endpoints
- **/all**: 2 endpoints
- **/anomalies**: 1 endpoints
- **/anomaly**: 1 endpoints
- **/append**: 1 endpoints
- **/assign**: 1 endpoints
- **/attempts**: 1 endpoints
- **/audit**: 6 endpoints
- **/audit-export**: 1 endpoints
- **/audit-loads**: 1 endpoints
- **/audit-log**: 1 endpoints
- **/audit_trail**: 1 endpoints
- **/authority-lost**: 1 endpoints
- **/auto-pair**: 7 endpoints
- **/auto-watch**: 1 endpoints
- **/automations**: 14 endpoints
- **/backhaul**: 4 endpoints
- **/banker-digest**: 1 endpoints
- **/bankruptcy**: 1 endpoints
- **/blacklist-mc**: 1 endpoints
- **/blast**: 6 endpoints
- **/boc3**: 5 endpoints
- **/bol-mismatch**: 1 endpoints
- **/book**: 2 endpoints
- **/briefing**: 1 endpoints
- **/browser-incoming**: 2 endpoints
- **/browser-outgoing**: 2 endpoints
- **/browser-phone-config**: 2 endpoints
- **/browser-status**: 2 endpoints
- **/browser-token**: 2 endpoints
- **/buckets**: 1 endpoints
- **/budgets**: 1 endpoints
- **/bulk-apply-defaults**: 2 endpoints
- **/bulk-promote**: 1 endpoints
- **/bulk-run**: 1 endpoints
- **/calendar**: 6 endpoints
- **/call-history**: 1 endpoints
- **/cap**: 1 endpoints
- **/capabilities**: 2 endpoints
- **/captured-fee**: 1 endpoints
- **/captured-margin**: 1 endpoints
- **/cargo-claim**: 3 endpoints
- **/cargo-claims**: 2 endpoints
- **/carrier**: 5 endpoints
- **/carrier-leaderboard**: 1 endpoints
- **/carriers**: 2 endpoints
- **/categories**: 1 endpoints
- **/charges**: 1 endpoints
- **/check**: 1 endpoints
- **/check-can-book**: 1 endpoints
- **/check-load**: 1 endpoints
- **/checklist**: 2 endpoints
- **/circuit_breakers**: 2 endpoints
- **/claim**: 1 endpoints
- **/classify**: 1 endpoints
- **/cleanup**: 2 endpoints
- **/collections-packets**: 1 endpoints
- **/complete**: 1 endpoints
- **/complete-and-send-bca**: 1 endpoints
- **/compliance-watcher**: 1 endpoints
- **/config**: 17 endpoints
- **/contacts**: 3 endpoints
- **/conversion-dashboard**: 1 endpoints
- **/corridor-config**: 1 endpoints
- **/counter-offer**: 1 endpoints
- **/create**: 1 endpoints
- **/credentials**: 3 endpoints
- **/credit-check**: 2 endpoints
- **/critical-active**: 1 endpoints
- **/cron**: 2 endpoints
- **/dashboard**: 2 endpoints
- **/data**: 1 endpoints
- **/deactivate**: 1 endpoints
- **/decision-matrix**: 1 endpoints
- **/demand-letter**: 2 endpoints
- **/demote**: 1 endpoints
- **/detention**: 1 endpoints
- **/detention-disputes**: 2 endpoints
- **/dev_review_queue**: 4 endpoints
- **/dial-search**: 1 endpoints
- **/digest**: 5 endpoints
- **/digest-log**: 1 endpoints
- **/disarm-all**: 1 endpoints
- **/dismiss**: 1 endpoints
- **/dispatch-link**: 1 endpoints
- **/documents**: 1 endpoints
- **/double-broker**: 1 endpoints
- **/double-broker-flags**: 2 endpoints
- **/draft**: 6 endpoints
- **/drafts**: 1 endpoints
- **/driver**: 2 endpoints
- **/driver-presence**: 1 endpoints
- **/drivers**: 5 endpoints
- **/eld_check**: 2 endpoints
- **/email**: 2 endpoints
- **/entries**: 5 endpoints
- **/error**: 1 endpoints
- **/eta-slip**: 3 endpoints
- **/evaluate**: 1 endpoints
- **/event**: 1 endpoints
- **/events**: 7 endpoints
- **/exceptions**: 1 endpoints
- **/execute**: 1 endpoints
- **/execution**: 1 endpoints
- **/export**: 1 endpoints
- **/external-loads**: 1 endpoints
- **/factor-decline**: 1 endpoints
- **/factors**: 1 endpoints
- **/feature-state**: 2 endpoints
- **/first-live-load**: 2 endpoints
- **/flags**: 2 endpoints
- **/fleet**: 2 endpoints
- **/folders**: 1 endpoints
- **/followups**: 2 endpoints
- **/force-close**: 1 endpoints
- **/force-trip**: 1 endpoints
- **/forecast**: 1 endpoints
- **/foresight**: 1 endpoints
- **/freshness**: 1 endpoints
- **/friday-digest-candidates**: 1 endpoints
- **/from-event**: 1 endpoints
- **/funding-trapped**: 1 endpoints
- **/fx**: 2 endpoints
- **/generate**: 1 endpoints
- **/ghosting**: 1 endpoints
- **/github_actions_webhook**: 1 endpoints
- **/governor**: 3 endpoints
- **/gps-silent**: 1 endpoints
- **/handshake-status**: 1 endpoints
- **/headroom**: 1 endpoints
- **/health**: 3 endpoints
- **/heartbeat**: 3 endpoints
- **/history**: 6 endpoints
- **/identity**: 4 endpoints
- **/ingest-csv**: 1 endpoints
- **/issue-token**: 1 endpoints
- **/lane**: 1 endpoints
- **/lane-overrides**: 1 endpoints
- **/lane-stats**: 1 endpoints
- **/lanes**: 1 endpoints
- **/last**: 2 endpoints
- **/last-run**: 2 endpoints
- **/last-send.html**: 1 endpoints
- **/last-send.pdf**: 1 endpoints
- **/last.pdf**: 1 endpoints
- **/latest**: 2 endpoints
- **/ledger**: 1 endpoints
- **/limit**: 1 endpoints
- **/list**: 10 endpoints
- **/load**: 6 endpoints
- **/load-status**: 1 endpoints
- **/loads**: 23 endpoints
- **/log**: 2 endpoints
- **/lookup**: 2 endpoints
- **/lumper**: 1 endpoints
- **/magic-link**: 1 endpoints
- **/manual**: 1 endpoints
- **/manual-attribute**: 1 endpoints
- **/manual-pause**: 1 endpoints
- **/manual-sms**: 1 endpoints
- **/map**: 1 endpoints
- **/mca**: 2 endpoints
- **/me**: 4 endpoints
- **/mercy**: 1 endpoints
- **/metrics**: 1 endpoints
- **/milestone**: 1 endpoints
- **/missing**: 1 endpoints
- **/mode**: 1 endpoints
- **/modules**: 1 endpoints
- **/monitor**: 1 endpoints
- **/month-end-audit**: 1 endpoints
- **/multimodal**: 5 endpoints
- **/no-show**: 1 endpoints
- **/note**: 1 endpoints
- **/now**: 2 endpoints
- **/operation**: 1 endpoints
- **/operational_state**: 1 endpoints
- **/operator_push_queue**: 1 endpoints
- **/optin**: 1 endpoints
- **/orphans**: 1 endpoints
- **/outcomes**: 4 endpoints
- **/override**: 3 endpoints
- **/override-log**: 1 endpoints
- **/parse**: 1 endpoints
- **/parse-text**: 1 endpoints
- **/patch_proposals**: 3 endpoints
- **/pdf**: 2 endpoints
- **/pending-review**: 1 endpoints
- **/poll**: 1 endpoints
- **/poll-now**: 1 endpoints
- **/posters**: 3 endpoints
- **/prepickup-due**: 1 endpoints
- **/prepickup-run**: 1 endpoints
- **/preview**: 4 endpoints
- **/preview-payload**: 1 endpoints
- **/preview.html**: 1 endpoints
- **/preview.pdf**: 2 endpoints
- **/promote**: 2 endpoints
- **/protest-countdown**: 1 endpoints
- **/providers**: 6 endpoints
- **/public**: 1 endpoints
- **/publish**: 1 endpoints
- **/pulse**: 1 endpoints
- **/push**: 1 endpoints
- **/push-all**: 1 endpoints
- **/push-to-factor**: 1 endpoints
- **/queue**: 2 endpoints
- **/quote**: 2 endpoints
- **/quote-accept-gate**: 1 endpoints
- **/quotes**: 1 endpoints
- **/ramp-history**: 1 endpoints
- **/rank**: 1 endpoints
- **/raw**: 1 endpoints
- **/rearm-all**: 1 endpoints
- **/rebuild**: 2 endpoints
- **/recent**: 7 endpoints
- **/recent-pulses**: 1 endpoints
- **/recent_promotions**: 1 endpoints
- **/recent_revocations**: 1 endpoints
- **/recents**: 1 endpoints
- **/recheck**: 2 endpoints
- **/recipients**: 1 endpoints
- **/recognize**: 1 endpoints
- **/record**: 1 endpoints
- **/red-carpet**: 1 endpoints
- **/refresh**: 3 endpoints
- **/register**: 1 endpoints
- **/release**: 1 endpoints
- **/reports**: 1 endpoints
- **/required-tasks**: 1 endpoints
- **/reset**: 1 endpoints
- **/reset-snapshot**: 1 endpoints
- **/reweigh**: 1 endpoints
- **/rfq**: 2 endpoints
- **/robo-call**: 1 endpoints
- **/rules**: 1 endpoints
- **/run**: 2 endpoints
- **/run-now**: 11 endpoints
- **/runs**: 2 endpoints
- **/schedule**: 1 endpoints
- **/schema-completeness**: 2 endpoints
- **/scrapers**: 1 endpoints
- **/search**: 1 endpoints
- **/seed**: 1 endpoints
- **/seed-known**: 1 endpoints
- **/segment**: 1 endpoints
- **/segments**: 1 endpoints
- **/semaphore**: 1 endpoints
- **/send**: 1 endpoints
- **/send-now**: 2 endpoints
- **/sentinel**: 3 endpoints
- **/sequestered**: 3 endpoints
- **/session-continuity**: 1 endpoints
- **/shadow-banned**: 1 endpoints
- **/share-packet**: 3 endpoints
- **/shipper**: 1 endpoints
- **/shipper-loyalty**: 1 endpoints
- **/shipper-profiles**: 3 endpoints
- **/shippers**: 2 endpoints
- **/simulate**: 1 endpoints
- **/sitemap.xml**: 1 endpoints
- **/skipped_today**: 1 endpoints
- **/snapshot**: 1 endpoints
- **/sources**: 1 endpoints
- **/spam_review**: 1 endpoints
- **/start**: 1 endpoints
- **/state**: 7 endpoints
- **/state-extended**: 1 endpoints
- **/stats**: 5 endpoints
- **/status**: 12 endpoints
- **/status-for**: 1 endpoints
- **/storage**: 1 endpoints
- **/stripe**: 12 endpoints
- **/submissions**: 3 endpoints
- **/submit**: 3 endpoints
- **/suggest**: 1 endpoints
- **/suggestions**: 3 endpoints
- **/summary**: 5 endpoints
- **/system**: 1 endpoints
- **/system-state**: 1 endpoints
- **/targets**: 3 endpoints
- **/technical-advantage**: 1 endpoints
- **/telemetry**: 1 endpoints
- **/thresholds**: 1 endpoints
- **/throttle**: 2 endpoints
- **/tick**: 3 endpoints
- **/timeline**: 1 endpoints
- **/toggle-mode**: 1 endpoints
- **/tollfree-create-new**: 1 endpoints
- **/tollfree-packet**: 1 endpoints
- **/tollfree-resubmit**: 1 endpoints
- **/tollfree-status**: 1 endpoints
- **/top**: 1 endpoints
- **/track-open**: 1 endpoints
- **/track-open.gif**: 1 endpoints
- **/transactions**: 1 endpoints
- **/transfers**: 1 endpoints
- **/trigger**: 2 endpoints
- **/trucks**: 1 endpoints
- **/twilio**: 1 endpoints
- **/unban**: 1 endpoints
- **/unsubscribe**: 1 endpoints
- **/urgent_alerts**: 3 endpoints
- **/valid-stages**: 1 endpoints
- **/violations**: 1 endpoints
- **/voicemail-callback**: 1 endpoints
- **/voicemails**: 2 endpoints
- **/walkthrough_aggregates**: 1 endpoints
- **/walkthrough_load**: 1 endpoints
- **/walkthrough_summary**: 1 endpoints
- **/webhook**: 6 endpoints
- **/weight_proposals**: 5 endpoints
- **/{action_id}**: 1 endpoints
- **/{carrier_mc}**: 3 endpoints
- **/{category}**: 2 endpoints
- **/{dispute_id}**: 2 endpoints
- **/{entry_id}**: 1 endpoints
- **/{event_id}**: 2 endpoints
- **/{key}**: 3 endpoints
- **/{lane_id}**: 1 endpoints
- **/{load_id}**: 11 endpoints
- **/{master_id}**: 3 endpoints
- **/{mc_number}**: 1 endpoints
- **/{negotiation_id}**: 3 endpoints
- **/{origin_state}**: 1 endpoints
- **/{payout_id}**: 2 endpoints
- **/{preset_id}**: 3 endpoints
- **/{ref_or_id}**: 2 endpoints
- **/{renewal_id}**: 4 endpoints
- **/{rfq_id}**: 2 endpoints
- **/{shipper_id}**: 4 endpoints
- **/{token}**: 2 endpoints
- **/{user_id}**: 1 endpoints
- **api/_carrier_redispatch**: 2 endpoints
- **api/_client_errors**: 1 endpoints
- **api/_compliance**: 1 endpoints
- **api/_dispatch**: 1 endpoints
- **api/_enrichment**: 4 endpoints
- **api/_factor_decline**: 4 endpoints
- **api/_monitor**: 1 endpoints
- **api/_rate_conf**: 2 endpoints
- **api/_records-request**: 3 endpoints
- **api/_tracking**: 1 endpoints
- **api/admin**: 1 endpoints
- **api/auth**: 2 endpoints
- **api/broker**: 5 endpoints
- **api/broker-invoice**: 8 endpoints
- **api/capacity-hunter**: 4 endpoints
- **api/carrier**: 14 endpoints
- **api/carrier-discovery**: 1 endpoints
- **api/carriers**: 7 endpoints
- **api/email-ingestion**: 7 endpoints
- **api/facility-plan**: 3 endpoints
- **api/fmcsa**: 1 endpoints
- **api/fmcsa-census**: 3 endpoints
- **api/hardware-key**: 4 endpoints
- **api/lanes**: 2 endpoints
- **api/live-board**: 5 endpoints
- **api/live-map**: 1 endpoints
- **api/loads**: 12 endpoints
- **api/margin-erosion**: 4 endpoints
- **api/market-radar**: 1 endpoints
- **api/noa**: 5 endpoints
- **api/ops**: 1 endpoints
- **api/outbound-autobid**: 3 endpoints
- **api/payouts**: 3 endpoints
- **api/privacy**: 3 endpoints
- **api/public**: 21 endpoints
- **api/rate-cons**: 3 endpoints
- **api/records-request**: 1 endpoints
- **api/replies**: 3 endpoints
- **api/tracking**: 4 endpoints
- **api/webhook**: 1 endpoints
- **api/webhooks**: 1 endpoints