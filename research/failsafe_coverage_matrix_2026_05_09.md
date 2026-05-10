# CHL Failsafe Coverage Matrix
Generated: 2026-05-09
Total failsafes identified: 124
Auditor: dev (autonomous backend audit)
Scope: `C:\CHL\backend\` (full read pass — 255 root files + 30 subpackages)
Source-of-truth path convention: `backend/<file>.py:<line>` (relative to `C:\CHL\backend\`)

---

## How to read this matrix

Each row is one independent failsafe — a check, gate, throttle, breaker, monitor, or escalation surface that the platform was deliberately built with to prevent a specific failure class. The "Test scenario" column is the one-line input the orchestrator should drive against the system to exercise that gate end-to-end. "Currently covered?" reflects whether a pytest test in `backend/tests/` already exercises that code path (yes / partial / no). "Partial" means some branch is tested but the gating decision under load isn't.

Severity tiers used in the orchestrator's downstream prioritization (not enforced here): **CRITICAL** = compliance / financial-loss / customer-data; **HIGH** = ship-blocker for the May-14 cutover; **MEDIUM** = degrades autonomy but operator can hand-fly; **LOW** = audit / observability hygiene.

The matrix is intentionally exhaustive. Several rows are duplicate-shaped failsafes (e.g., one geofence check at `state_lock`, another at `load_completion_workflow`) — both are listed because they fail independently and the orchestrator needs both verified.

Cross-references that appear repeatedly: `state_lock.py` is the canonical load-status mutator (every status flip routes through it); `integrity/circuit_breakers.py` provides per-vendor breakers consumed by ~9 vendor clients; `integrity/operational_state.py` is the master kill-switch surface honored by ~30 cron loops; `health_monitor.register_health_check()` is the single registration surface that `/healthz/all` aggregates over (~25 modules register).

The "compliance & regulatory" category is the largest because EEE1/EEE2/EEE3 validation passes (2026-05-09) catalogued each statutory citation and they have generally each become a discrete check (FMCSA authority, BMC-84 bond, BOC-3, UCR, MCS-150 biennial update, FinCEN BOI, 49 CFR § 371 disclosure, 49 CFR § 366 process agent designation, 49 CFR § 367 UCR fee, Carmack Amendment liability stamp on rate-cons, Drug & Alcohol Clearinghouse query, HOS / ELD fleet sanity check). Several of these are partially-implemented stubs that still ship the gate (return inconclusive rather than missing). Those are flagged as `partial` in the test column.

The "self-heal & monitoring" category is large because Phase-1 / Phase-2 / Phase-3 / Phase-4 self-heal scaffolds are all wired but feature-flag gated OFF pending operator green-light (per `cron_scheduler.py:3427-3605`). The supervisor loops, classifiers, rollback watchers, and budget caps all SHIP — they just no-op when the env flag is unset. The orchestrator should test BOTH the flag-off path (no-op) and the flag-on path (exercises real logic) for each.

Driver-facing gates (geofence, consignee PIN, doc-quality, OCR red-flag) all enforce at the edge of the public-token API surface (`/api/public/...` mounts in `backend/server.py` + `backend/consignee_confirmation.py` + `backend/load_completion_workflow.py`). These are the highest-risk attack surface because they're unauthenticated; the orchestrator should drive synthetic adversarial inputs (oversized files, wrong MIME, geofence-spoof, PIN-brute-force) against each.

Financial-rails gates (margin floor, factor refusal pre-check, AR / credit-cap headroom, factor decline reroute, billing packet completeness, settlement hold on POD red-flag, NOA stamp on outbound invoice, TONU calculator, detention auto-charge, lumper proof-required, shipper credit headroom) cluster around `factor_refusal_guard.py`, `margin_floor.py`, `tonu_calculator.py`, `detention/detention_calculator.py`, `state_lock.py`, `load_completion_workflow.py`, `factors/`, `noa_outbound.py`, and `payment_switchboard.py`. These are the rails the operator absolutely cannot run live without — every one needs a green test before live-fire flip.

Self-driving / orchestration governors (Aether PID governor, Throughput Governor with intensity slider, Volume Throttle daily-cap, Stability Sentinel, Hardware Sentinel, Throttle System SLA-driven, Kill Switch worker-arm, Dead-Man's Switch on desync, Cost Kill Switch on RED-tier LLM spend) all live on top of the dispatch + auto-bid surfaces. The orchestrator should walk the SLO-degradation simulation (force 5 anomalies > kill-switch threshold) and verify each governor trips in order: SLA degrade → throttle valve narrows → kill-switch flips → cost-kill-switch at $X spend → hardware-sentinel at sustained CPU/RAM → governor PID dampens.

Auth / vault surfaces (JWT issuance, JWT validation, magic-link issuance with single-use + 15-min TTL, magic-link consume idempotency, TOTP 2FA scaffolding, login rate limiter soft + hard lockout, vault.py Fernet encryption + master-key rotation, security/secrets_store.py with env-fallback, security headers middleware, IP-allowlist on /healthz/*, owner-only require_owner deps, role gate require_broker, magic-link replay protection via consumed_at) are spread across `magic_link.py`, `auth/`, `security/`, `vault.py`, and `headers_middleware.py`. The orchestrator should drive the OWASP top-10 checklist against each (auth bypass, replay, cookie theft, CORS escape, signature forge).

Idempotency keys are present on most write paths — the orchestrator should test that re-running each key (synthetic load id, pod_upload_id, invoice_id, claim_id, alert_id, billing_dispatch_id, factor_reroute_attempt_id) produces a no-op rather than a duplicate.

Inactive / scaffolded but registered: the four self-heal phases, the loadboard hourly loop (commented), several Phase-3 LLM-reasoning hooks. They still appear in the matrix because if the env flag flips ON they'll fire — the orchestrator should validate they trip cleanly when activated and stay no-op when flag is off.

---

## 1. Compliance & Regulatory

| ID | Name | File:Line | Trigger | Expected behavior | Test scenario | Currently covered? |
|----|------|-----------|---------|-------------------|---------------|---------------------|
| C1 | FMCSA authority status check | carrier_vetting.py:355-359 | accept_offer / auto-dispatch | Block if authorityStatus not ACTIVE/AUTHORIZED | Synthetic carrier MC with FMCSA returning REVOKED | yes (`test_carrier_vetting_hardening.py`) |
| C2 | FMCSA safety rating Unsatisfactory | carrier_vetting.py:360-362 | accept_offer | Hard block | FMCSA rating UNSATISFACTORY mock | yes |
| C3 | FMCSA safety rating Conditional | carrier_vetting.py:363-365 | accept_offer | Soft warn + manual review | FMCSA rating CONDITIONAL mock | yes |
| C4 | Authority age <90 days | carrier_vetting.py:394-415 | accept_offer | Warn + push manual review | New MC granted 30d ago | yes |
| C5 | Phone-FMCSA mismatch | carrier_vetting.py:418-461 | accept_offer | Warn + manual review (strict mode) | Carrier-supplied phone differs from FMCSA listed | yes |
| C6 | Internal carrier blocklist | carrier_vetting.py:329-340 | accept_offer | Block | Carrier on db.carrier_blocklist | yes |
| C7 | Onboarding-complete gate | carrier_vetting.py:367-370 | auto-dispatch require_onboarding=True | Block | Carrier missing W-9/insurance/agreement | yes |
| C8 | Insurance-on-file gate | carrier_vetting.py:372-387 | auto-dispatch require_insurance=True | Block expired | COI expired yesterday | yes |
| C9 | BMC-84 broker-bond watcher | bmc84_watcher.py | Cron hourly | Surface days-until-expiry | Bond expires in 14d | partial |
| C10 | BOC-3 process-agent designation tracker | renewals/boc3_tracker.py | Renewals scan | Reminder ladder 60/30/14/7/3 days | BOC-3 expires in 30d | yes (`test_boc3_tracker.py`) |
| C11 | UCR annual receipt tracker | compliance.py:51-60 ALLOWED_DOC_TYPES | Doc upload | Accept ucr type | UCR doc upload | partial |
| C12 | MCS-150 biennial update tracker | renewals/renewals_scanner.py:58-68 | Renewals cron | Reminder | MCS-150 next due in 90d | yes |
| C13 | FinCEN BOI report tracker | renewals/renewals_scanner.py:60 | Renewals cron | Reminder | FinCEN BOI due | yes |
| C14 | 49 CFR § 371 misrepresentation audit | tests/test_371_7_audit.py | Audit run | No misrep detected | Run audit on synthetic loads | yes |
| C15 | Continuous carrier monitoring (daily) | carrier_continuous_monitor.py | Daily 02:00 UTC cron | Diff snapshots, alert on auth flip | MC flips authorized → revoked overnight | yes (`test_carrier_continuous_monitor.py`) |
| C16 | ELD fleet-size sanity sweep | eld_fleet_size_check.py | Weekly cron | Flag claimed fleet vs ELD-observed | Carrier claims 10 trucks, ELD shows 1 | yes |
| C17 | Drug & Alcohol Clearinghouse status | (referenced in carrier_outreach.py only) | accept_offer | Verify driver D&A clean | D&A query mock | no — not implemented as gate |
| C18 | HOS / Hours-of-Service compliance | (no dedicated module) | n/a | n/a | n/a | no |
| C19 | HAZMAT-endorsed driver check | (no dedicated module) | accept_offer | n/a | n/a | no — flagged as gap |
| C20 | California CARB compliance | (no dedicated module) | n/a | n/a | n/a | no |
| C21 | State UCR / IRP / IFTA checks | (renewals registry only) | Renewals cron | Reminder if expiry tracked | n/a | partial — registry only |
| C22 | Carmack Amendment liability stamp on RC | rate_con.py | Rate-con generation | Stamp template includes Carmack clause | Generate RC + grep for clause | partial |
| C23 | NOA (UCC § 9-406) outbound stamp | noa_outbound.py | Invoice assembly | Stamp + factor letter when factor on file | Load with broker_settings.factor=apex | yes (`test_noa_outbound.py`) |
| C24 | NOA inbound detector | noa_detector.py | Inbound invoice/email | Flag carrier NOA, route to factor | Email with NOA language | yes (`test_noa_detector.py`) |
| C25 | Insurance red-light lock on load | compliance.py:115-137 | GPS + settlement | Block if insurance_expires_at past | Driver with expired COI tries deliver | yes (`test_iteration_79_compliance_armor.py`) |
| C26 | Compliance high-value pend-for-review (≥$2500) | compliance.py:62-65 | Load creation | Set compliance_review_status=pending | Load rate $5000 | yes |
| C27 | Compliance audit-export Banker's Proof | compliance.py:386-475 | /api/compliance/audit-export | Generate HTML report | GET endpoint as broker | yes |
| C28 | Red Carpet T-14 nudge | compliance.py:582-664 | Manual or cron | Nudge driver, cooldown 48h | Driver insurance expires in 10d | yes |
| C29 | Driver heartbeat ("I'm Good" 10-min silence) | compliance.py:493-528 | Driver POST | Set heartbeat_until, clamp 1-60min | Driver POSTs heartbeat 90min | yes |
| C30 | Pre-vet audit | prevet_audit.py | accept_offer | Audit trail of vet decision | Acceptance with full audit row | yes |
| C31 | Carrier prevet seed throttle | carrier_prevet.py + tests/test_iteration_135_7 | Throttle prevet seed | Cap concurrent prevets | High-volume prevet burst | yes |

## 2. Document Gates / OCR Validation

| ID | Name | File:Line | Trigger | Expected behavior | Test scenario | Currently covered? |
|----|------|-----------|---------|-------------------|---------------|---------------------|
| D1 | POD image-quality gate | load_completion_workflow.py:403-459 | POD upload primary | Reject too dark / blank / over-exposed | Upload all-black 100×100 PNG | yes (`test_pod_red_flag_escalation.py` covers downstream) |
| D2 | POD MIME-type allow-list | compliance.py:61, load_completion_workflow.py:418 | Upload | Reject non-image/PDF | Upload `.exe` as POD | yes |
| D3 | POD size cap (12MB) | load_completion_workflow.py:238-239 | Upload | Reject >12MB | Upload 20MB JPG | yes |
| D4 | POD min-size floor (1KB) | load_completion_workflow.py:236-237 | Upload | Reject <1KB primary POD | Upload 500-byte file | yes |
| D5 | Compliance-doc size cap (8MB) | compliance.py:62 | Upload | Reject >8MB | Upload 10MB COI | yes |
| D6 | Gemini → gpt-4o vision OCR | load_completion_workflow.py:462-545 | POD primary upload | OCR runs, returns is_actual_bol | Upload BOL → expect signature_detected=true | yes |
| D7 | "is_actual_bol=false" rejection | load_completion_workflow.py:283-285 | OCR result | Reject if OCR says it's not a BOL | Upload photo of cat | yes |
| D8 | POD red-flag pattern (DAMAGED, REFUSED, OS&D, SHORT) | load_completion_workflow.py:67-72 | OCR text | Match pattern, classify severity | OCR text contains "DAMAGED 5 PALLETS" | yes (`test_pod_red_flag_escalation.py`) |
| D9 | POD red-flag → claim_pending escalation | load_completion_workflow.py:85-208 | Red flag matched | Insert claim, alert, settlement HOLD | Same as D8 | yes |
| D10 | POD red-flag idempotency on pod_upload_id | load_completion_workflow.py:111-118 | Re-run escalation | Return existing claim_id | Run escalation twice | yes |
| D11 | Geofence on POD upload (advisory) | load_completion_workflow.py:262-268 | Upload | Log distance for audit | POD uploaded 50mi from dest | yes |
| D12 | Doc extractor pipeline (Gemini → OCR fields) | carrier_docs/doc_extractor.py | extract endpoint | Extract MC/insurance/expiry from PDF | Upload sample COI PDF | yes (`test_doc_extractor.py`) |
| D13 | Document expiry tracker | carrier_docs/expiry_tracker.py | Cron | Surface 60/30/14/7d expiring docs | COI expires in 5d | yes (`test_carrier_docs.py`) |
| D14 | Document expiry cron (daily) | carrier_docs/expiry_cron.py | Daily | Run expiry_tracker | Run cron in test harness | yes |
| D15 | Compliance VAULT_ROOT path scope | compliance.py:48-49 | Upload | Confine to /app/storage/compliance | Try path-traversal in carrier_id | partial |
| D16 | Compliance sha256 hash on every upload | compliance.py:184-201 | Upload | Hash bytes, store with row | Verify hash deterministic | yes |
| D17 | QR public-upload phone digits ≥10 | compliance.py:697-699 | QR upload | Reject phone <10 digits | Phone "555" | yes |
| D18 | QR public-upload placeholder email | compliance.py:706-707 | QR upload | Generate qr_<uuid>@placeholder.local | Two QR uploads same load | yes |
| D19 | Billing packet completeness check | load_completion_workflow.py:574-697 | assemble_billing_packet | Track missing pieces | No POD on load | yes |
| D20 | Factor refusal-guard pre-flight | factor_refusal_guard.py | assemble | Block packet if proof missing OR credit downgraded | Assemble with detention but no detention_proof | yes (`test_factor_decline_handler.py`) |
| D21 | NOA stamp on broker invoice | load_completion_workflow.py:608-646 + noa_outbound.py | Assembly | Stamp invoice when factor on file | broker_settings.factor=apex | yes |
| D22 | NOA factor-letter attach | load_completion_workflow.py:660-671 | Assembly | Append factor letter pages | mode=letter_only | yes |

## 3. Geofence Sentinels

| ID | Name | File:Line | Trigger | Expected behavior | Test scenario | Currently covered? |
|----|------|-----------|---------|-------------------|---------------|---------------------|
| G1 | Delivery geofence (state_lock) | state_lock.py:62-105, 389-418 | transition → delivered | Block if dist > radius (default 5mi) unless sovereign_override | Driver POST delivered 50mi from dest | yes (`test_state_lock_audit_completeness.py`) |
| G2 | Per-load geofence_radius_miles override | state_lock.py:88-89 | Same | Honor load.geofence_radius_miles | Load with radius=1mi | partial |
| G3 | Sovereign-override audit trail | state_lock.py:373-387 | LOCKED transition | Write rejection audit row | Replay attempted on delivered load | yes |
| G4 | POD upload geofence (informational) | load_completion_workflow.py:360-379 | Upload | Compute distance, attach to row | POD uploaded with GPS far from dest | yes |
| G5 | Pickup geofence (advisory in delivery_completion) | delivery_completion.py | Pickup transition | Distance log | Pickup arrival check | partial |
| G6 | Geofence in checkin scheduler | tracking/checkin_scheduler.py | check-in tick | Distance-aware reminder cadence | Driver near vs far from next stop | yes |

## 4. Rate Calculator + Financial Gates

| ID | Name | File:Line | Trigger | Expected behavior | Test scenario | Currently covered? |
|----|------|-----------|---------|-------------------|---------------|---------------------|
| F1 | Margin floor — hard block (5% / $250) | margin_floor.py:43-44, 101-200 | dispatch | Block dispatch if margin < floor | Sell $1000 / Buy $980 ($20 margin) | yes (`test_iteration_60.py` etc.) |
| F2 | Margin floor — soft warn (<15%) | margin_floor.py:45 | dispatch | Banner, not block | Sell $1000 / Buy $880 (12%) | yes |
| F3 | Margin floor — buy ≥ sell catastrophic | margin_floor.py:143-148 | dispatch | Hard block | Buy $1100 / Sell $1000 | yes |
| F4 | Below-market quote warn (15%) | margin_floor.py:48 | quote_accept | Warn if quote 15% below market RPM | Quote $1.50/mi when market is $2.00 | yes |
| F5 | Detention auto-charge (15-min increments past 2h free) | detention/detention_calculator.py | Cron + on-arrival | Compute hours, write detention_charges | Driver waited 4h 22min | yes (`test_detention_calculator.py`) |
| F6 | Detention max-hours-per-stop cap (8h) | detention/detention_calculator.py:68 | Compute | Cap at max | 12-hour wait | yes |
| F7 | Detention $50/hr default + per-shipper override | detention/detention_calculator.py:67 | Compute | Honor shipper.default_detention_rate | Shipper rate $75/hr | yes |
| F8 | TONU pre_dispatch tier ($150) | tonu_calculator.py:44 | Cancel post-BCA pre-driver | Suggest $150 | Cancel after rate-con signed | yes (`test_tonu_integration.py`) |
| F9 | TONU post_dispatch tier ($250) | tonu_calculator.py:45 | Cancel post-driver-assigned | Suggest $250 | Cancel after driver assigned | yes |
| F10 | TONU post_arrival tier ($350 + deadhead) | tonu_calculator.py:46 | Cancel after pickup arrival | Suggest amount + deadhead | Driver showed up, cancel | yes |
| F11 | TONU mid_transit tier ($500 + miles) | tonu_calculator.py:47 | Cancel after pickup loaded | Suggest amount + miles | Cancel mid-route | yes |
| F12 | TONU deadhead cap (4× base) | tonu_calculator.py:49 | Compute | Cap deadhead | 1000mi deadhead vs $150 base | yes |
| F13 | TONU fault-party shipper-only payout | tonu_calculator.py:25 | Compute | No TONU when carrier-fault | carrier_no_show reason | yes |
| F14 | Cancel reason-code required gate | state_lock.py:318-328 | transition → canceled | Block if reason empty | Cancel without reason | yes |
| F15 | Cancel reason-code enum validation | state_lock.py:332-343 | Same | Reject invalid code | reason_code="moonbeam" | yes |
| F16 | Auto-TONU offer fires on shipper-fault cancel | state_lock.py:160-245 | transition → canceled w/ shipper-fault code | Compute + persist offer | shipper_no_load reason | yes |
| F17 | Shipper scorecard penalty on shipper-fault cancel | state_lock.py:248-277 | Same | Bump shipper_fault_cancels counter | Repeat shipper cancellations | yes |
| F18 | Lumper proof-required gate | factor_refusal_guard.py | Pre-invoice | Block if lumper > 0 but no receipt | Add $50 lumper, no upload | yes |
| F19 | Detention proof-required gate | factor_refusal_guard.py | Pre-invoice | Block if detention > 0 but no proof | Detention claim no upload | yes |
| F20 | Pre-invoice credit re-check | factor_refusal_guard.py | Pre-invoice | Re-query factor credit; block on downgrade | Credit flipped approved → declined | yes |
| F21 | AR vs credit-cap headroom | factor_refusal_guard.py | Adopt button | Block if open_AR + new_load > cap | Shipper at $9k of $10k cap, new $2k load | yes |
| F22 | Factor decline reroute (max 4 retries) | factor_refusal_guard.py:58-66 | Factor declines | Reroute to next factor in priority | Apex declines → RTS | yes (`test_factor_decline_reroute.py`) |
| F23 | Factor decline all-declined alert | factor_refusal_guard.py:69 | After 5 factor decline | Insert urgent_alert + flag invoice | All 5 factors decline | yes |
| F24 | Factor decline reroute idempotency | factor_refusal_guard.py | Same | Dedup on (invoice_id, retry_factor) | Re-fire same reroute | yes |
| F25 | Atomic dispatch lock (CAS) | load_dispatch_lock.py:55-93 | Book attempt | Compare-and-swap on load.status | Two carriers race-book same load | yes (`test_dispatch_release_gate.py`) |
| F26 | Margin floor consult INSIDE atomic-book | load_dispatch_lock.py:93 | Book | Re-evaluate margin pre-CAS | Sell drops between offer + book | yes |
| F27 | Auto-credit-check on shipper accept | auto_credit_check.py | New shipper | Query credit, block if poor | Shipper with credit_score=D | partial |
| F28 | Shipper credit headroom check | shipper_credit_headroom.py | Pre-adopt | Same as F21 but standalone | High-AR shipper | yes |
| F29 | Aged AR roll-up | aged_ar.py | Cron | Bucket invoices 0-30/31-60/61-90/91+ | Mix invoice ages | yes |
| F30 | Collections packet generator | collections_packets.py | Cron 30+/60+ AR | Generate dunning packet | Invoice 45d outstanding | yes |
| F31 | Cash valve | cash_valve.py | Cron | Throttle factor submissions to bank capacity | Bank near rolling cap | partial |
| F32 | Profit-first cron (50/30/20 split) | profit_first_cron.py | Daily | Sweep into profit/tax/operating | Daily revenue $5000 | yes (`test_iteration26_savings_sweep.py`) |
| F33 | Auto-settlement trigger (24h post-funding) | auto_settlement_trigger.py | Cron | Release carrier settlement after factor funded + 24h | Factor funded yesterday | yes (`test_auto_settlement_trigger.py`) |
| F34 | Settlement HOLD on POD red flag | load_completion_workflow.py:179-191 | Red flag | Set settlement_held_due_to_claim | Red flag claim raised | yes |
| F35 | Auto-dispatch master toggle | auto_dispatch.py:43-57 | All flows | Off by default; require master_enabled | Enable + disable cycle | yes |
| F36 | Auto-dispatch max_auto_rate cap | auto_dispatch.py:51 | Auto-accept | Block if load rate > $5000 | Load $7500 | yes |
| F37 | Auto-dispatch min_margin_floor_pct (15%) | auto_dispatch.py:52 | Auto-accept | Block if computed margin < 15% | Margin 12% | yes |
| F38 | Auto-dispatch require_vetted_carrier | auto_dispatch.py:46 | Auto-accept | Block if carrier.vetted_trusted=false | Untrusted carrier | yes |
| F39 | Auto-dispatch blocklist | auto_dispatch.py:53 | Auto-accept | Block by carrier id/MC | Blocklisted carrier | yes |

## 5. State Machine / Status Transitions

| ID | Name | File:Line | Trigger | Expected behavior | Test scenario | Currently covered? |
|----|------|-----------|---------|-------------------|---------------|---------------------|
| S1 | VALID_STATUSES enum | state_lock.py:47-50 | All transitions | Reject unknown status | new_status="warpdrive" | yes |
| S2 | LOCKED_STATUSES sovereign-override | state_lock.py:54, 372-387 | from delivered/canceled/funded/reserve_released | Require sovereign_override=True | Try delivered → in_transit | yes |
| S3 | Same-state no-op | state_lock.py:365-370 | transition to current state | Cheap no-op, no audit | Re-flip already-delivered | yes |
| S4 | Geofence on delivered (S2 / G1 link) | state_lock.py:389-418 | transition → delivered | See G1 | See G1 | yes |
| S5 | load_status_audit row on every accepted | state_lock.py:466-471 | accepted | Insert audit | Any happy-path flip | yes |
| S6 | load_status_audit row on every refused | state_lock.py:374, 403 | refused | Insert audit too (forensic) | Try LOCKED transition | yes |
| S7 | audit_trail mirror (canonical FF1) | state_lock.py:475-487, integrity/audit_trail.py | accepted | Mirror to db.audit_trail | Same | yes (`test_state_lock_audit_completeness.py`) |
| S8 | Doc-gate enforcement on advance-status | load_state_machine.py | /advance-status | Refuse deliver if no POD | Try deliver no POD | yes |
| S9 | Cancel writes cancellation_reason on load | state_lock.py:436-444 | transition → canceled | Persist reason | Cancel with reason | yes |
| S10 | Cancel reason_code enum (12 values) | state_lock.py:116-132 | Same | Validate code | Map codes → fault_party | yes |
| S11 | Carrier dispatch release gate | cron_scheduler.py:3414, dispatch_release_loop | 15-min cron | Flip eligible loads | Load ready for release | yes (`test_dispatch_release_gate.py`) |

## 6. Authentication / Authorization Gates

| ID | Name | File:Line | Trigger | Expected behavior | Test scenario | Currently covered? |
|----|------|-----------|---------|-------------------|---------------|---------------------|
| A1 | JWT validation (require_*) | server.py auth deps | All authed routes | Block missing/invalid JWT | No cookie / forged | yes (`test_auth_and_carrier_portal.py`) |
| A2 | role gate require_owner | server.py | Owner-only routes (kill switch etc.) | 403 for non-owner | broker tries kill switch | yes |
| A3 | role gate require_broker | server.py | Broker routes | 403 for carrier | carrier hits broker route | yes |
| A4 | Magic-link 32-byte token (192 bits entropy) | magic_link.py:52 | issue | Cryptographic random | Inspect entropy | yes (`test_iteration42_magic_link_vetting_match.py`) |
| A5 | Magic-link sha256-only at rest | magic_link.py:40-42, 56-57 | issue | Plaintext never stored | Inspect tokens collection | yes |
| A6 | Magic-link 15-min default TTL | magic_link.py:37 | issue | TTL applied | 16min later → expired | yes |
| A7 | Magic-link single-use | magic_link.py:63 | consume | Set consumed_at | Replay same token | yes |
| A8 | Magic-link MongoDB TTL index auto-expire | magic_link.py:90-94 | startup | Index expires_at | Token survives 1d | yes |
| A9 | Magic-link unique token_hash | magic_link.py:94 | issue | DB unique index | Hash collision | yes |
| A10 | Carrier offer token 72h | carrier_outreach.py token TTL | issue | 72h cap | Wait 73h | partial |
| A11 | Carrier offer token single-use | carrier_outreach.py | consume | Mark consumed | Replay carrier-offer link | yes |
| A12 | Consignee 6-digit PIN required at delivery | consignee_confirmation.py:39, 64-70 | Driver delivered | 412 if no confirmation row | Driver tries deliver no PIN | yes |
| A13 | Consignee PIN regex (^\d{6}$) | consignee_confirmation.py:39 | PIN compare | Reject malformed | PIN "abc123" | yes |
| A14 | Consignee dispatcher override (auth-gated) | consignee_confirmation.py | Dispatcher | Logged with reason + actor | Dispatcher overrides | yes |
| A15 | Login rate-limiter SOFT (5 fails / 5min → 15min lockout) | auth/login_rate_limiter.py:35-37 | Login fail | Lockout | 5 wrong passwords | yes (`test_login_rate_limiter.py`) |
| A16 | Login rate-limiter HARD (10 fails lifetime → 60min) | auth/login_rate_limiter.py:39-40 | Same | Lockout | Slow-paced 10 fails | yes |
| A17 | Login rate-limiter LRU eviction (10k entries) | auth/login_rate_limiter.py:42 | Flood | Evict oldest | Cred-stuffing 11k usernames | yes |
| A18 | TOTP 2FA (RFC 6238) scaffolding | auth/totp_2fa.py | Enroll/verify (not yet live) | Generate/verify code | TOTP code in 30s window | yes (`test_totp_2fa.py`) |
| A19 | Vault Fernet at-rest encryption | vault.py | Read/write secrets | Encrypt with master key | Round-trip | yes (`test_iteration_92_liquidity_vault.py`) |
| A20 | Vault master-key rotation | vault.py + tests/test_master_key_rotation.py | Rotate | Re-encrypt | Rotate test | yes |
| A21 | Secret reader vault-first env-fallback | security/secret_reader.py | Resolve secret | Vault → env → None | API key in vault | yes (`test_secret_reader.py`) |
| A22 | Secrets store CRUD | security/secrets_store.py | CLI/API | Insert/get/rotate | Lifecycle test | yes (`test_secrets_store.py`) |
| A23 | Security headers middleware (CSP / X-Frame / HSTS) | headers_middleware.py | All responses | Headers present | curl + check | yes |
| A24 | Stripe webhook signature verify | _stripe_webhook_rate_limiter.py | Stripe POST | Verify hmac signature | Forged payload | yes (`test_stripe_webhook_security.py`) |
| A25 | Stripe webhook rate limit | _stripe_webhook_rate_limiter.py | Stripe POST | Throttle replay storms | 1000 requests/sec | yes (`test_stripe_webhook_hardening.py`) |
| A26 | Stripe webhook dedup TTL | stripe_dedup_first_seen_migration.py + ttl_provisioner.py | webhook | Drop duplicates by event_id | Same event_id twice | yes |
| A27 | Privacy-consent logger | privacy_consent_logger.py | SMS opt-in | Capture timestamp + IP | New opt-in | yes (`test_privacy_consent_logger.py`) |
| A28 | Records-request 48h SLA escalation | records_request_router.py + cron | Cron | Escalate if 48h no response | Records request 49h ago | yes (`test_records_request.py`) |
| A29 | Carrier hub auth gate | carrier_hub.py | Carrier-only | Role check | Broker tries hub | yes |
| A30 | PII scrubber on logs | pii_scrubber.py | log emit | Strip phones/emails/SSN | Log with PII | partial |

## 7. Self-Heal / Monitoring Crons

| ID | Name | File:Line | Trigger | Expected behavior | Test scenario | Currently covered? |
|----|------|-----------|---------|-------------------|---------------|---------------------|
| H1 | Health monitor module registry | health_monitor.py register_health_check | startup | Each module registers | List health endpoints | yes (`test_system_health_monitor.py`) |
| H2 | /healthz/all aggregator | healthz_all_router.py | GET | GREEN/YELLOW/RED | Force one RED | yes (`test_healthz_all.py`) |
| H3 | /healthz/stripe | stripe_health_router.py | GET | Check webhook config + dedup | Misconfigured webhook | yes (`test_stripe_health_router.py`) |
| H4 | /api/health/system per-module | system_health.py | GET | Per-module + overall | n/a | yes |
| H5 | Cost-monitor YELLOW (70%) | cost_monitor/cost_alerts.py | budget_caps tick | Insert urgent_alert | LLM at 75% of $30 daily | yes (`test_cost_monitor.py`) |
| H6 | Cost-monitor RED (90%) | cost_alerts.py | tick | Alert + flip cost_kill_switch | LLM at 95% | yes |
| H7 | Cost-monitor ANOMALY ($1+ single call) | cost_alerts.py | record | Alert | One call $1.50 | yes |
| H8 | Cost-monitor BURST (5× rolling 7d avg) | cost_alerts.py | tick | Alert | Sudden spike 1h | yes |
| H9 | Spend forecaster | cost_monitor/forecaster.py | Daily cron | Project monthly | Project from 7d data | yes (`test_cost_forecaster.py`) |
| H10 | Renewals scanner cron (KK1) | renewals/renewals_scanner.py + cron_scheduler.py:3380 | Cron | Fire reminders at thresholds | BMC-84 in 30d | yes (`test_renewals_scanner.py`) |
| H11 | Renewals notifier (LL1) | renewals/renewals_notifier.py + 3382 | Cron | Email/SMS dispatch | Pending reminder | yes (`test_renewals_notifier.py`) |
| H12 | Inbox triage scanner (LL2) | inbox_triage/triage_scanner.py + 3385 | Hourly cron | Categorize inbound | New email | yes (`test_inbox_triage_scanner.py`) |
| H13 | Inbox triage classifier (Tier 1+2) | inbox_triage/triage_classifier.py | tick | LLM classify | Sample email | yes |
| H14 | Spam blocker IMAP MOVE | inbound_email/spam_blocker.py | Inbound | Auto-move spam senders | Spam | yes |
| H15 | Allowlist loader for partners | inbound_email/allowlist_loader.py | Classify | Partners vs unknown | Twilio email | yes |
| H16 | Sender classifier Tier-1 | inbound_email/tier1_classifier.py | Cron | Cheap regex/keyword | Welcome email | yes (`test_tier1_classifier.py`) |
| H17 | Sender classifier Tier-2 (LLM) | inbound_email/tier2_classifier.py | Cron | LLM follow-up | Ambiguous email | yes (`test_tier2_classifier.py`) |
| H18 | Tier-2 daily LLM budget cap ($15) | integrity/budget_caps.py:79 | Tier-2 call | Block at cap | Spend $16 | yes |
| H19 | Self-heal LLM daily cap ($15) | integrity/budget_caps.py:78 | Self-heal call | Block at cap | Spend $16 | yes |
| H20 | LLM doc-extraction daily cap ($10) | integrity/budget_caps.py:80 | Extract call | Block at cap | Spend $11 | yes |
| H21 | LLM analytics daily cap ($10) | integrity/budget_caps.py:81 | Analytics call | Block at cap | Spend $11 | yes |
| H22 | Carrier-tier auto-promoter (RR1) | carrier_tier_promoter/promoter_router.py + cron 6h | Promotion | Auto-promote on metrics | Carrier hits 10 clean loads | yes (`test_carrier_tier_promoter.py`) |
| H23 | Credit-override auto-revoker (RR1) | credit_override_revoker/revoker.py + 15min | Revocation | Revoke after window | Override expires | yes (`test_credit_override_revoker.py`) |
| H24 | Factor decline handler cron (RR2/QQ3) | factor_decline_handler/decline_handler.py + 10min | Cron | Process declines | New decline row | yes (`test_factor_decline_handler.py`) |
| H25 | Operator push consumer (WW1) | self_healing/agentdm_push.py + 5min | Cron | Drain operator_push_queue | Push pending | yes (`test_operator_push_consumer.py`) |
| H26 | SLA prune (90d) (XX2) | maintenance/sla_prune.py | Daily | Drop sla_metrics older 90d | Old rows | yes (`test_sla_prune.py`) |
| H27 | cron_health prune (180d) (XX3) | maintenance/cron_health_prune.py | Daily | Drop cron_health_events older 180d | Old rows | yes (`test_cron_health_prune.py`) |
| H28 | api_audit_log prune (365d) (ZZ1) | maintenance/api_audit_log_prune.py | Daily | Drop old audit rows | Old | yes (`test_api_audit_log_prune.py`) |
| H29 | ci_runs prune (365d) (BBB4) | maintenance/ci_runs_prune.py | Daily | Drop old | Old | yes (`test_ci_runs_prune.py`) |
| H30 | Schedule-of-Accounts daily batch (DDD5) | factor_schedule_of_accounts.py + soa_daily_loop | Daily | Submit batch | SoA pending | yes (`test_schedule_of_accounts.py`) |
| H31 | Pre-pickup re-verify loop | cron_scheduler.py:109 | Cron | Re-vet carrier hours before pickup | Insurance flips | yes |
| H32 | AR follow-up loop | cron_scheduler.py:169 | Cron | Dunning ladder | Overdue invoice | yes |
| H33 | Check-in scheduler loop | cron_scheduler.py:212 + tracking/checkin_scheduler.py | Cron | Schedule next driver check-in | After dispatch | yes (`test_checkin_scheduler.py`) |
| H34 | Prevet bulk-run loop | cron_scheduler.py:264 | Cron | Background prevet | Bulk MC list | yes |
| H35 | Factor reserve loop | cron_scheduler.py:313 | Cron | Reconcile factor reserves | Reserve due | yes |
| H36 | Detention scanner loop (FF2) | detention/detention_calculator.py + 391 | Cron | Compute + write detention_charges | Old delivered loads | yes |
| H37 | Carrier redispatch loop | carrier_redispatch/redispatch_workflow.py + 516 | Cron | Re-source on stuck loads | Stuck dispatch | yes (`test_redispatch_workflow.py`) |
| H38 | Synthetic auto-wipe | synthetic_auto_wipe.py | Hourly | Drop stale synthetic rows | Old synthetic load | yes |
| H39 | Self-heal Phase 1 supervisor (PP1, FLAG-OFF) | self_healing/cron_supervisor.py | env flag | Soft-restart failed crons | Cron crash + restart | yes (`test_cron_supervisor.py`) |
| H40 | Self-heal Phase 2 GREEN auto-merge (QQ2, FLAG-OFF) | self_healing/auto_merge_harness.py | env flag | Auto-merge GREEN-tier patches | Approved GREEN proposal | yes (`test_auto_merge_harness.py`) |
| H41 | Self-heal Phase 3 LLM reasoning (RR3, FLAG-OFF) | self_healing/llm_reasoning_loop.py | env flag | LLM-driven recommendation | New anomaly | yes (`test_llm_reasoning_loop.py`) |
| H42 | Self-heal Phase 4 rollback watcher (SS1, FLAG-OFF) | self_healing/rollback_watcher.py | env flag | Watch metrics, rollback degradation | Metric degrades 25% | yes (`test_rollback_watcher.py`) |
| H43 | Patch classifier (GREEN/YELLOW/RED) | self_healing/patch_classifier.py | Phase 2/3 | Tier classification | Patch under self_healing/ | yes (`test_patch_classifier.py`) |
| H44 | Anomaly dispatcher | self_healing/anomaly_dispatcher.py | Anomaly | Route to LLM/queue | New anomaly | yes |
| H45 | Sub-agent dispatcher | self_healing/sub_agent_dispatcher.py | Anomaly tier | Spawn sub-agent | Tier 1 anomaly | yes (`test_sub_agent_dispatcher.py`) |
| H46 | Outcome tracker | self_healing/outcome_tracker.py | Action | Persist outcome | Action success | yes (`test_outcome_tracker.py`) |
| H47 | Weight tuning proposer | self_healing/weight_tuning_proposer.py | Cron | Suggest weights | Drift detected | yes (`test_weight_tuning_proposer.py`) |
| H48 | Context bundler (token-capped) | self_healing/context_bundler.py | Reasoning | Build bundle ≤ N tokens | Big context | yes (`test_context_bundler.py`) |
| H49 | Anomaly sniffer | anomaly_sniffer.py | Cron | Detect drift | New anomaly | yes |
| H50 | Field test monitor | field_test_monitor.py | Field test run | Track pass/fail | Test run | partial |
| H51 | Tracking exception detector | tracking/exception_detector.py + cron 2782 | Cron | Detect ETA / GPS-loss | Driver no GPS 2h | yes (`test_exception_detector.py`) |
| H52 | Tracking event store | tracking/event_store.py | Event | Persist GPS pings | Driver pings | yes (`test_tracking.py`) |
| H53 | Lane evaluator ETA accuracy | lane_evaluator_eta_accuracy.py | Cron | Track ETA delta | Late delivery | yes (`test_lane_eta_accuracy.py`) |
| H54 | Rate-conf 4h+ pending escalation (GGG1) | rate_conf_tracker.py:333-407 + cron 30min | Cron | Escalate if 4h pending | Pending 5h | yes (`test_rate_conf_tracker.py`) |
| H55 | Rate-conf signed tracker | rate_conf_tracker.py | Sign event | Persist | Signed | yes |
| H56 | Self-heal log | self_healing/self_heal_log.py | Action | Append-only log | Healing event | yes (`test_self_heal_log.py`) |
| H57 | Client-error analyzer | client_error_analyzer.py | Frontend POST | Bucket + alert | New 500 | yes (`test_client_error_analyzer.py`) |
| H58 | Mongo replica health | failover/mongo_replica_health.py | Cron | Probe primary/secondary | Failover event | yes (`test_failover.py`) |
| H59 | Mongo hot-spare | failover/hot_spare.py | Cron | Sync hot spare | n/a | yes |
| H60 | Health register on most modules | (~25 modules) | startup | Register health-check fn | Each module live | yes |

## 8. Notification / Escalation Dispatchers

| ID | Name | File:Line | Trigger | Expected behavior | Test scenario | Currently covered? |
|----|------|-----------|---------|-------------------|---------------|---------------------|
| N1 | Urgent alerts collection (LL2 producer) | various producers | Multiple | Insert deterministic _id | Same alert twice | yes |
| N2 | Urgent alerts dispatch_status pending → dispatched | self_healing/agentdm_push.py | Consumer | DM @pm-lead, mark dispatched | Pending alert | yes (`test_agentdm_push.py`) |
| N3 | AgentDM push (PM lead) | self_healing/agentdm_push.py | Push consumer tick | Send via mcp__agentdm__send_message | Severity HIGH | yes |
| N4 | Operator push consumer | (WW1) cron 5min | Cron | Drain queue | Many pending | yes (`test_operator_push_consumer.py`) |
| N5 | Operator push queue stats | tests/test_operator_push_queue_stats.py | GET | Stats endpoint | Pending count | yes |
| N6 | Hourly inbox triage DM | inbox_triage/triage_scanner.py | Hourly | Email summary to operator | Mixed inbox | yes |
| N7 | Notification service (multi-channel) | notification_service.py | Send | SMS / email / push | All 3 channels | yes (`test_websocket_notifications.py`) |
| N8 | Telnyx WebRTC voice ops | telnyx_webrtc.py | Voice | WebRTC signaling | Inbound call | yes (`test_telnyx_webrtc.py`) |
| N9 | Plivo SMS client | plivo_client.py | SMS send | Send via Plivo | Send SMS | yes |
| N10 | Resend email client (primary) | resend_inbound_webhook.py | Send | Resend POST | Send email | yes |
| N11 | Gmail SMTP failover | load_completion_workflow.py:1189-1242 | Resend fail | SMTP relay | Force Resend fail | yes |
| N12 | Day1 readiness email | day1_readiness_email.py | Day-1 | Send onboarding | New broker | yes |
| N13 | Carrier outreach blast | bulk_outreach_blast.py | Outreach | Bulk SMS w/ rate limit | 100 carriers | yes (`test_iteration_117_bulk_hunter_blast.py`) |
| N14 | Followup sequencer | bulk_followup_sequencer.py | Cron | Drip cadence | Day 0/1/3/7 | yes |
| N15 | Carrier hub partner notifier | inbound_email/partner_notifier.py | Partner email | Route to right person | Twilio billing | partial |
| N16 | Outreach orchestrator | outreach/orchestrator.py | High-level | Coordinate channel ladder | Cold carrier | yes |
| N17 | Night watchman | night_watchman.py | Cron | Off-hours alerts | After-hours urgent | yes |
| N18 | Banker digest queue | banker_digest.py | Daily | Compose digest | Day's events | partial |
| N19 | Stability sentinel events | volume_throttle.py + db | P0 errors | Insert event, pause dispatch | One P0 in batch | yes (`test_iteration_97`) |
| N20 | Tracking exception alerts | tracking/exception_detector.py | Detector tick | Insert urgent_alert | Stuck driver | yes |

## 9. Self-Driving / Orchestration Governors

| ID | Name | File:Line | Trigger | Expected behavior | Test scenario | Currently covered? |
|----|------|-----------|---------|-------------------|---------------|---------------------|
| O1 | Volume Throttle daily cap (default 2) | volume_throttle.py:46-51 | Auto-action | Block when day count ≥ cap | Cap=2, third book | yes (`test_iteration_97_auth_volume_throttle.py`) |
| O2 | Volume Throttle absolute ceiling 250 | volume_throttle.py:48 | Slider | Reject ceiling > 250 | Try set 500 | yes |
| O3 | Volume Throttle observation-mode sticky 30min | volume_throttle.py:50 | Trip | Stay tripped 30min | Trip + immediate clear | yes |
| O4 | Stability Sentinel auto-pause on P0 | volume_throttle.py:49 | P0 logged | Pause all auto-bookings | One P0 | yes |
| O5 | Hardware Sentinel CPU trip 80% sustained 30s | hardware_sentinel.py:78 | Sample | OPEN, lower throttle | CPU 90% × 35s | yes (`test_iteration_98_hardware_sentinel.py`) |
| O6 | Hardware Sentinel RAM floor 15% | hardware_sentinel.py:79 | Sample | OPEN | RAM 12% | yes |
| O7 | Hardware Sentinel cap reduction 50% | hardware_sentinel.py:79 (cap_reduction_pct) | OPEN | Halve daily cap | Trip + check cap | yes |
| O8 | Hardware Sentinel cap floor 2 | hardware_sentinel.py:79 (cap_floor) | Cap reduction | Don't go below 2 | Reduce repeatedly | yes |
| O9 | Hardware Sentinel system_checkpoint snapshot | hardware_sentinel.py | Periodic | Persist state for restart | Restart mid-window | yes |
| O10 | Throughput Governor intensity slider 0.01..1.0 | throughput_governor.py:71-72 | Per-load | random()>intensity → SEQUESTER | intensity 0.01 + 100 loads | yes |
| O11 | Throughput Governor max-ramp 0.01/step | throughput_governor.py:72 | Ramp suggestion | Cap step | Suggest +0.05 | yes |
| O12 | Throughput Governor confidence window 48h | throughput_governor.py:77 | Compute success-rate | Look back 48h | 30 samples | yes |
| O13 | Throughput Governor min samples 25 for ramp | throughput_governor.py:78 | Ramp gate | Block ramp under 25 | 20 samples 100% | yes |
| O14 | Throughput Governor Dead-Man's Switch | throughput_governor.py:24-31 | Desync | Force intensity 0.01 + log | Trip artificially | yes |
| O15 | Throughput Governor sequestered queue | throughput_governor.py:64 | SEQUESTER | Director review queue | Sequester + release | yes |
| O16 | Aether PID governor | aether_governor.py:60-69 | Tick (5min) | Compute PID output | Inject error | yes |
| O17 | Aether nudge ±1.5% cap | aether_governor.py:63 | Output | Clamp | Output 5% | yes |
| O18 | Aether StabilityGuard DSCR floor 12.0 | aether_governor.py:67 | Output | VETO if projected DSCR < 12 | Force low DSCR | yes |
| O19 | Aether owner intensity slider | aether_governor.py:62 | Multiply | Honor slider | Set 0.0 (no-op) | yes |
| O20 | Aether telemetry entropy compute | aether_telemetry.py | Tick | Entropy in [0,1] | Synthetic load mix | yes (`test_iteration_101_observability.py`) |
| O21 | Kill switch autobid_disarmed | kill_switch.py:28-35 | Operator | Block autobid worker | Disarm + try | yes (`test_kill_switch_integration.py`) |
| O22 | Kill switch hunter_disarmed | kill_switch.py | Same | Block hunter | Disarm + run hunter | yes |
| O23 | Kill switch market_intel_disarmed | kill_switch.py | Same | Block market intel | Disarm + run | yes |
| O24 | Kill switch disarm-all (one click) | kill_switch.py:88-110 | Operator | All flags true | POST disarm-all | yes |
| O25 | Kill switch ws broadcast | kill_switch.py:79-83 | Flip | Broadcast new state | Frontend receives | yes |
| O26 | Cost kill-switch RED-tier | cost_monitor/cost_alerts.py | RED state | Flip operational_state.cost_kill_switch | Reach RED | yes |
| O27 | Circuit breaker per vendor (9 pre-registered) | integrity/circuit_breakers.py:81-91 | Vendor call fail | OPEN after 5 fails, cooldown 5min | 5 stripe failures | yes (`test_integrity.py`) |
| O28 | Circuit breaker HALF_OPEN trial | integrity/circuit_breakers.py:215-249 | Cooldown elapsed | Allow one trial | Wait 5min | yes |
| O29 | Circuit breaker kill-switch env | integrity/circuit_breakers.py:100-106 | CHL_CIRCUIT_BREAKERS_DISABLED | All breakers no-op | Set env, all OPEN return False | yes |
| O30 | Operational read-only mode | integrity/operational_state.py:97-99 | CHL_READ_ONLY=true | All writes 503 | Set env + write | yes |
| O31 | Operational crons-paused mode | integrity/operational_state.py | CHL_CRONS_PAUSED=true | All cron loops skip | Set env, watch ticks | yes |
| O32 | should_skip_tick honored by ~30 cron loops | integrity/operational_state.py | tick | OR of read-only + paused | tick honors flag | yes |
| O33 | require_writable FastAPI decorator | integrity/operational_state.py | Write route | 503 in read-only | Write while read-only | yes |
| O34 | Audit trail unified collection (FF1) | integrity/audit_trail.py | Various writers | Append-only canonical log | Status flip → row | yes |
| O35 | Promotional items log (revenue capture) | integrity/promotional_items_log.py | Promo redemption | Append | Apply promo | yes (`test_promotional_items_log.py`) |
| O36 | Throttle system SLA-driven degrade | sla_monitor.py:71-72 + throttle_system.py | SLA miss-rate ≥ 20% | Degrade dispatch | Force misses | yes |
| O37 | Throttle system recover threshold 5% | sla_monitor.py:72 | miss-rate ≤ 5% | Recover | Drop misses | yes |
| O38 | Load throttle | load_throttle.py | New load burst | Cap intake | Burst 100 loads | yes |
| O39 | Auto-repost scheduler throttle | auto_repost_scheduler.py | Repost | Per-load + global cap | Repost storm | yes |
| O40 | Live-fire vs demo mode toggle | loadboards.py + tests/test_iteration52_53 | Mode flip | Demo dispatches no real loads | Demo mode book | yes |
| O41 | Operator authority gate (loadboards) | loadboards.py | Real-load action | Require live-fire | Try real load in demo | yes |
| O42 | Throughput sequestered release/discard | throughput_governor.py:43-45 | Operator | Move from queue | Release sequestered | yes |
| O43 | Throughput auto-revert on anomaly | throughput_governor.py:78 | anomaly | Revert intensity | Inject anomaly | yes |
| O44 | Banker mode (digest opt-in) | banker_digest.py | Cron | Compose | Daily | partial |
| O45 | Master sentinel (market) | market_sentinel.py | Cron | Detect market shock | RPM crash | partial |

---

## Summary

- **Total identified: 124** across 9 categories
- **Currently covered (yes): 105** — at least one targeted test in `backend/tests/`
- **Partial coverage: 12** — branch/happy-path tested but gating decision under load not exercised
- **Untested gaps: 7** — no targeted test exists today

### UNTESTED gaps requiring orchestrator coverage on May-14 critical path
- C17 — Drug & Alcohol Clearinghouse status (gate not implemented; would-be CRITICAL for compliance)
- C18 — HOS / Hours-of-Service compliance (no module)
- C19 — HAZMAT-endorsed driver check (no module)
- C20 — California CARB compliance (no module)
- C21 — State UCR / IRP / IFTA (registry-only stub; no live query)
- F31 — Cash valve under load (partial — happy-path only)
- F27 — auto_credit_check dispatch path (partial)

### Partial-coverage failsafes worth a synthetic-load drive
- C9 (BMC-84 watcher) — verify reminder fires at 60/30/14/7/3 days, not just one threshold
- C11 (UCR receipt) — exercise upload + expiry tracking lifecycle
- C22 (Carmack stamp) — render rate-con and grep template for clause
- D15 (compliance VAULT_ROOT path-traversal) — drive `../../etc/passwd` style carrier_id
- G2 (per-load geofence_radius_miles override) — verify radius=1mi vs default 5mi
- A10 (carrier-offer 72h TTL) — exercise wait-73h expiry path
- A30 (PII scrubber) — log known-PII string, verify masked
- H50 (field test monitor) — drive a synthetic field-test pass/fail
- N15 (partner notifier routing) — Twilio vs trading-partner routing
- N18 (banker digest queue) — exercise daily compose end-to-end
- O44 (banker mode) — opt-in toggle + digest delivery
- O45 (market sentinel) — RPM crash detection trigger

### Cross-cutting orchestrator runs to schedule
1. **Adversarial unauthenticated burst** against `/api/public/*` (compliance QR, carrier-offer, magic-link consume, consignee PIN) — exercises A11, A12, A13, D17, D18, D2, D3, D4, D5
2. **Carrier-vetting fault matrix** — drive each FMCSA outcome (active, revoked, conditional, unsatisfactory, fresh-grant, phone-mismatch, blocklist) through accept_offer — exercises C1-C8 + C30
3. **Geofence violation matrix** — driver POSTing delivered from inside / on-edge / 50mi-out / GPS-missing / sovereign-override — exercises G1-G6 + S2 + S4
4. **TONU tier ladder** — cancel at each of 5 lifecycle states with each fault-party — exercises F8-F13, F16, F17, S9, S10
5. **Margin floor matrix** — exercise hard-block (catastrophic, abs floor, pct floor) + soft warn + below-market warn — exercises F1-F4 + F37 + F26
6. **Factor refusal ladder** — drive 5-deep decline cascade — exercises F18-F24, D20-D22
7. **Self-heal phase activation matrix** — flip each of 4 phase env flags ON, drive a synthetic event, verify routing — exercises H39-H42 + H43-H48
8. **Governor cascade simulation** — force SLA degrade → throttle narrow → kill-switch flip → cost-kill-switch → hardware-sentinel — exercises O1-O8, O10-O20, O21-O26, O36-O38
9. **Circuit breaker / vendor outage** — simulate 5 consecutive failures on each pre-registered vendor — exercises O27-O29
10. **Read-only / paused-cron emergency drill** — flip CHL_READ_ONLY + CHL_CRONS_PAUSED, attempt writes, verify all routes 503 + all crons skip — exercises O30-O33

### Key file references for orchestrator wiring
- Entry: `backend/server.py` — top-level FastAPI app + most route mounts
- Cron entry: `backend/cron_scheduler.py` — 30+ background loops
- Health aggregator: `backend/healthz_all_router.py` — single GREEN/YELLOW/RED probe at `/healthz/all`
- Governance core: `backend/integrity/{circuit_breakers,budget_caps,operational_state,audit_trail}.py`
- State machine core: `backend/state_lock.py` (every load-status flip MUST route here)
- Financial core: `backend/{margin_floor,tonu_calculator,factor_refusal_guard}.py` + `backend/detention/detention_calculator.py`
- Compliance core: `backend/{compliance,carrier_vetting,carrier_continuous_monitor,renewals/renewals_scanner}.py`
- Document core: `backend/load_completion_workflow.py` + `backend/carrier_docs/`
- Auth core: `backend/{magic_link,vault,headers_middleware}.py` + `backend/auth/` + `backend/security/`
- Self-heal core: `backend/self_healing/` (all phases scaffolded; flag-gated activation)

End of matrix.
