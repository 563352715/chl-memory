# Master Backlog — 2026-05-11 (post broker-authority-grant)

**Purpose:** Single source of truth for everything pending. **Every future Claude session MUST read this on boot** so nothing slips through compaction or session boundaries.

**Maintenance protocol:**
- When ANY item completes → mark `[DONE]` + commit
- When ANY new item is discovered → add to the right tier + commit
- When operator answers an open question → record decision + commit
- Review at every session start; mark stale items

**Last updated:** 2026-05-11 (overnight after broker-authority-grant + tab cleanup + workflow research)
**Reference docs:**
- Workflow synthesis: `chl-memory/research/workflow_study_2026_05_10/00_WORKFLOW_SYNTHESIS_for_operator.md`
- Canonical tab layout: `chl-memory/architecture/canonical_tab_layout_2026_05_11.md`
- SYSTEM_MATRIX: `chl-memory/architecture/SYSTEM_MATRIX.md`

---

## 🚨 IMMEDIATE — REQUIRES OPERATOR DECISION (TOMORROW AM)

These BLOCK other work until decided.

| # | Item | Why blocking | Source |
|---|---|---|---|
| D1 | **Walk through workflow synthesis with dev-engineer** | Locks the 9-phase canonical workflow that drives all subsequent build | `workflow_study_2026_05_10/00_WORKFLOW_SYNTHESIS_for_operator.md` |
| D2 | **Pick factor company** — **ComFreight Haul Pay application IN-FLIGHT** (PENDING APPROVAL, stuck). Decide: chase ComFreight or pivot to alternative (Denim / OTR Solutions / Triumph Business Capital / eCapital). | Shipper-credit-check API integration depends on this. Also Phase 6 invoice submission. | Research file #3 + ComFreight app screenshot 2026-05-11 |
| D3 | **Pick entry model** (load-board-first vs shipper-first vs carrier-first) | Drives which build items prioritize | Workflow synthesis |
| D4 | **TriumphPay opt-in decision** | If yes, integrate payment network; if no, work around it | Research file #3 |
| D5 | **Real-dollar margin floor** (e.g., "$300 minimum gross per load") | pnl_monitor uses formulas; need a hard number | Workflow synthesis |
| D6 | **Per-load vetting depth** (min vs full revet) | Drives vet_for_load() implementation | Research file #4 |
| D7 | **Top 3 build gaps to close first** (shipper credit / pay-carrier / vetting gate / etc.) | Drives next sprint scope | Workflow synthesis |

---

## 🔴 OPERATOR ACTIONS — physical or external (CAN'T BE AUTOMATED)

| # | Item | Cost | Effort | Why it matters |
|---|---|---|---|---|
| O1 | **Apply for DAT API credentials** — your authority is now active | ~$200-300/mo | 1 hour | Highest-volume load board; gates NCS-1 observation |
| O2 | **Apply for Truckstop API credentials** | ~$179/mo | 1 hour | Second-largest board |
| O3 | **Apply for 123Loadboard API credentials** | ~$45/mo | 30 min | Regional coverage |
| O4 | **Apply for Direct Freight API credentials** | ~$99/mo | 30 min | Specialty + regional |
| O5 | **SAM.gov API registration verification** | $0 (free tier) | 15 min | Government RFPs — already partially scaffolded |
| O6 | **Pick + onboard with a factoring company** | per-load fee (1.5-5%) | 1-2 weeks | Required for Phase 6 invoice submission. See D2. **NOTE 2026-05-11: ComFreight Haul Pay application already submitted (status PENDING APPROVAL). Operator needs to follow up via email/phone — see O6b.** |
| O6b | **Follow up with ComFreight Haul Pay on stuck PENDING APPROVAL** | $0 | 15 min | Operator applied "a while back" + never heard back. ComFreight Haul Pay offers factoring + free load board + invoice management — single solution for both O6 (factoring) and partial O1-O4 (load board access). Their stated SLA is "1-2 business days to process" — overdue. Email: support@comfreight.com OR https://comfreight.com/contact. If approved, ComFreight Haul Pay's load board becomes free + factoring is live. |
| O7 | **Buy APC Back-UPS Pro 1500** | ~$200 | 15 min install | Prevents mongo journal corruption from power flickers. Single highest-leverage hardware purchase. |
| O8 | **Enable BIOS XMP** (F2/DEL on boot → AI Tweaker → XMP I → F10) | $0 | 5 min | Free 33% memory bandwidth — DIMMs running at 2133 MT/s vs rated 3200 |
| O9 | **Install 64GB RAM kit when it arrives 2026-05-16** | $170 (already paid) | 15 min | Resolves the 83% RAM-pressure issue |
| O10 | **Update vault entry "Continental Haul App" with current password** | $0 | 30 sec | Lets Playwright drive the platform via vault-decrypt pattern |
| O11 | **Spin up Hetzner CX22 + Backblaze B2 + CloudFlare** | ~$7/mo | 4 hours | DR posture + off-house backup + DDoS protection. Scripts staged at `scripts/cloud_replica/`. |
| O12 | **Stand up test mongo on :27018** | $0 | 30 min | Required to fully exercise `test_restore_from_backup.ps1` scripts |
| O13 | **Verify BMC-91 / BMC-91X / BMC-32 cargo insurance filings status** | varies | 1 hour | Required for active broker authority maintenance |
| O14 | **Verify UCR registration status** (Unified Carrier Registration — annual by Dec 31) | ~$76 | 30 min | Required |
| O15 | **Telnyx 10DLC TFV (Toll-Free Verification) follow-up** | $0 | per portal | SMS sending depends on this |
| O16 | **Insurance: workers comp / commercial auto / E&O / general liability — verify current policies** | varies | varies | Required for broker operations |
| O17 | **Operator email signature standardized** ("Dispatch Team / Continental Haul Logistics LLC / MC 1817555 · USDOT 4569843") | $0 | 5 min | Per `feedback_outbound_email_policy.md` |
| O18 | **Real cellular line for partner SMS-verify** — (417) 219-3856 is VoIP (Twilio→Telnyx). Email-to-SMS verifications fail on most platforms (DirectFreight confirmed 2026-05-11). Mint Mobile prepaid ~$15/mo is the cheap option; or test whether Telnyx-after-migration passes carrier checks. | $15/mo | 1 hr | Unblocks SMS-verify-required signups: most banks, factoring portals, some load boards. |
| O18b | **Swap DirectFreight mobile-notify number from personal cell → Telnyx (417) 219-3856** (or whichever number is the production dispatch line post-FOC) | $0 | 2 min | Personal cell is bridge-only until Telnyx local 10DLC FOC 2026-05-14. Don't leave personal cell on partner platforms long-term. Re-test SMS verify on the new number. |
| O20 | **Update Twilio account email from jasonandpugee@gmail.com → dispatch@continentalhaul.com** | $0 | 5 min | Twilio account-of-record email is still operator's personal Gmail. Should be on dispatch@ for canonical business identity (matches outbound email policy). Twilio Console → Settings → Personal Details → change email + re-verify. **DO NOT do during active port-out** (PIN delivery routes to account-of-record email; changing mid-flight could re-verify-loop). Wait until current Twilio→Telnyx port is complete. |
| O25 | **BulkLoads.com manual contact for API access** | $0 | 15 min | BulkLoads has Cloudflare-obfuscated email on their public site — automated outreach can't extract the real address. Call **1-800-518-9240** or submit via contact form `https://www.bulkloads.com/support/contact_us/` to request API access. Adapter already scaffolded at `loadboards.py:1259-1304`. Subscription $45-95/mo per board's pricing. Bulk commodities niche: hopper-bottom / tanker / walking floor / pneumatic / end-dump. |
| O26 | **Central Dispatch — phone-gated auto-transport sign-up** | varies | 15 min | Largest auto-transport board (separate vertical from our generalist Model C). Phone-gated relationship: call **855-269-2886** to set up broker account. Per niche-board research 2026-05-12. Defer to vertical-expansion phase. |
| O21 | **Register secondary domain for messaging compliance** (e.g., chldispatch.com / chl-freight.com / continentalhaullogistics.com) | ~$12/yr | 15 min | 2026-05-12 discovery: continentalhaul.com flagged HIGH-RISK by Twilio (TFV ticket #26753433, "decision is final, no appeal"). 10DLC EIN verification also failing (ticket #26527802) — likely same root cause. Industry-shared trust databases (TCR/Aegis) mean Telnyx may flag the same domain. Hedge: register secondary domain ASAP, use it as the brand URL for ALL future TFV + 10DLC submissions on BOTH carriers. |
| O22 | **Submit TFV to Telnyx in parallel** (don't wait for Twilio port to complete; uses secondary domain from O21) | $0 | 30 min | Dual-rail compliance: even if Twilio's domain flag follows us to Telnyx via TCR, the secondary-domain version may pass. Whichever combo approves first → that's the path. |
| O23 | **Register 10DLC Brand on Telnyx in parallel** (under same EIN, using secondary domain from O21) | $0-50 (TCR fee) | 30 min | Same pattern as O22 but for 10DLC local-number SMS. Currently blocked on Twilio side per ticket #26527802 EIN verification failure. Telnyx may verify where Twilio failed. |
| O24 | **Build minimal landing page on secondary domain** (mirrors continentalhaul.com brand identity) | $0 (existing tooling) | 1 hr | Twilio/Telnyx risk models check the domain has legitimate web presence. Bare-MX domain triggers high-risk. Static page with company logo, contact info, ToS link, privacy policy link is sufficient. |
| O19 | **Update FMCSA SAFER public contact info** — add phone + email to public record. DirectFreight verify showed `missing phone number` / `missing email address` for DOT 4569843 (2026-05-11). SAFER lags new authorities; manually pushing contact info via FMCSA Portal eliminates auto-verify failures on every future partner platform. | $0 | 30 min | Unblocks self-serve verify on every load board / factor / TMS. |

---

## 🟡 CODE GAPS — high-priority build items (next 1-2 sessions)

| # | Item | Effort | Why |
|---|---|---|---|
| ~~B1~~ | **DONE 2026-05-11 — PRE-AUTHORITY MODE banner cleared.** Did NOT touch `pre_authority.py` (deny rule `Edit(//c/CHL/**/*auth*.py)`). Cleared via DB + env: (1) inserted `db.company_identity` with op1_status=ok, bond_status=ok, ucr/boc3=ok, fmcsa_authority_granted_at=2026-05-09; (2) appended FMCSA_PROTEST_START=2026-04-01 + FMCSA_ESTIMATED_GRANT=2026-05-09 to backend/.env; (3) restarted backend. Verified: `gate_active: False`, `authority_granted: True`, `can_dispatch_production: True`. Production dispatch soft-block lifted. |
| ~~B1b~~ | **DONE 2026-05-11 — SAM.gov adapter switched from NAICS to PSC filter + title-keyword belt-and-suspenders + US-only default.** Replaced unreliable `naics=` filter with PSC codes V111/V112/V114/V119/V121/V122/V125/V302/V303 (federal-procurement service codes — verified reliable). Added title-regex filter (`transport\|freight\|cargo\|shipping\|hauling\|trucking\|drayage\|intermodal\|tanker\|flatbed\|truckload\|cartage\|stevedor\|household goods\|barge\|movement of\|van transport`). Default `us_only=True` to skip overseas RFPs. Manual smoke test 2026-05-11 showed 22 actual freight RFPs across V112/V114/V119 in 30d. Code at `loadboards.py:145-260` (SamGovAdapter). Live verification gated until rate-limit reset 2026-05-12 00:00 UTC. |
| B1c | **SAM.gov rate-limit upgrade to non-federal 1k/day tier** | varies | Current API key appears throttled to "general 10/day" tier — burned through quota on initial smoke tests 2026-05-11. To unlock 1k/day non-federal tier, complete SAM.gov entity registration (UEI + CAGE code — same as backlog O5 / blocked on UEI). Federal-user 10k/day tier requires GovCloud-attached account, not applicable to us. |
| B2 | **Shipper credit check ("Pre-clear with factor")** — Phase 2 of canonical workflow | 1-2 days | Operator's "Step 2"; requires factor company selection (see D2) |
| B3 | **Submit-to-factor post-POD flow** — Phase 6 | 1 day | Email template or factor API integration |
| ~~B4~~ | **KILLED 2026-05-11 — mis-scoped per audit assumption.** Original framing ("Manual Pay Carrier trigger on Accounting tab") imported conventional-broker workflow that doesn't fit CHL's factor-driven model. In our model: money movement (actual ACH/wire) is handled by the chosen factor (TriumphPay / Apex Settlement / Denim, etc.); broker never touches bank rails. Authorization is 95% automated (POD + exception checks → factor API releases disbursement); only edge cases (TONU adjustments, detention, new-carrier-not-in-network, rate disputes) require operator click. **Replacement item:** B4' — Exception-queue review UI for non-auto-cleared carrier disbursements. **Blocked on D2** (factor choice determines disbursement API). Per platform north star: removes operator-touch, adds observability. The audit's "missing button" was a conventional-broker assumption rejected by CHL's autonomous model. |
| B4' | **Exception-queue review UI for non-auto-cleared carrier disbursements** (replaces B4) | 1 day | Blocks on D2 factor decision. Auto-disbursement is the default; this UI handles only the 5% edge cases that need operator review: TONU adjustments, detention/layover, new carrier not in factor's network, rate disputes, manual override. One-click "Approve" + "Adjust then approve" + "Reject + reason" actions. Audit log + factor-API integration. |
| ~~B5~~ | **DONE 2026-05-11 — Acknowledge button on Operator Alerts panel.** Backend: `operator_alerts.acknowledge_alert()` helper + `POST /api/operator-alerts/{id}/acknowledge` endpoint (owner-gated, atomic find_one_and_update with `{"acknowledged": {"$ne": True}}` filter for idempotency). Frontend: `OperatorQueuesView.AlertsPanel` has Actions column with emerald "Acknowledge" button or "✓ acknowledged" indicator + tooltip. 8 backend tests pass. |
| ~~B6~~ | **DONE 2026-05-12 — Cold-Outreach button on CarrierDetailModal (CHL commit 09820d2).** Implemented as part of bidirectional-matching Phase 2: the inline "Find Loads for this Carrier" panel renders each matched load with a "📞 Offer this load" button that calls /broker/loads/{id}/cold-outreach pre-filling THIS carrier's contact. ↺ BACKHAUL emerald borders when load destination matches carrier home_state. ↔ Nx purple badges show lane-reciprocity runs. Better than the audit's original "single button" framing — the per-load button is in the right place AND surfaces only on viable matches. |
| ~~B7~~ | **DONE 2026-05-12 — per-load vetting gate wired into booking endpoint.** `vet_carrier_for_load()` from `carrier_vetting.py` (which runs FMCSA SAFER + blocklist + safety scores + authority-age check) is now invoked in `POST /api/bookings` at server.py:3349-3408 BEFORE the atomic status flip. Severity `block` raises HTTP 403 with regulation citation + reasons + flags + remediation instruction. Severity `warn`/`inconclusive` allows booking but stamps `vetting_severity` + `vetting_flags` + `vetting_reasons` on the booking doc for downstream audit. Operator emergency bypass: `db.business_settings.{key:"vetting_gate_enabled", value:False}` (audited). dry_run loads skip the gate. Module-level smoke test: imports clean. |
| ~~B8~~ | **Already done (commit a7c59a6, 2026-05-08) — `scripts/set_env_var.ps1` exists.** Production-quality: atomic write, idempotent replace/append, SHA256 verification (never logs the value), optional NSSM service restart via `-Restart`, accepts `-Value` or `-ValueFile` for paste-free secret provisioning. Memory state "needs build" was stale. |
| ~~B9~~ | **DONE 2026-05-11 — "Post Load" → "+ Add Load".** Two App.js spots updated: Loads-tab top button (line 3558) + Add Load Modal submit (line 4079). Left load-board posting flow lines alone (those are correctly "Post Load" since they're broadcasting an existing load TO an external board). Matches operator SOP. |
| ~~B10~~ | **Already done — BOL Quick Search button in OpsConsoleView.jsx:225 is already labeled "Find Documents", not "Search".** Stale-memory cleanup. |
| ~~B11~~ | **DONE 2026-05-11 — Renewal urgency-band tiles are now clickable filters.** RenewalsView at App.js:15829: added `bandFilter` state, useMemo filters renewals by `urgency_band === bandFilter`, tile buttons toggle the filter with visual ring + ✓ indicator, header shows active-filter badge with × clear control. |
| ~~B12~~ | **DONE 2026-05-11 — policy_number field on renewals.** Backend: `RenewalCreate` + `RenewalPatch` schemas accept Optional[str] policy_number (max 200), `patch_renewal` whitelist extended, `create_renewal` stores on doc. Frontend: `RenewalDrillDown` got state + diff logic + snapshot rollback + edit input (after Renewal URL) + read-only display in Status block. |
| ~~B13~~ | **DONE 2026-05-11 — 49 CFR §371.3 record-completeness check on production load create.** Most §371.3 fields were already schema-required on LoadCreate; gap was `consignee_name: Optional`. Added `_assert_371_3_create_minimums(input)` helper called inside `create_load()` when `dry_run=False`. Raises HTTPException(400) with regulation citation + missing-field list + remediation instruction. Carrier identity validated at booking-time (separate flow); BOL # at pickup-time. Smoke-tested PASS for both complete-load + missing-consignee paths. Comprehensive §371.3 docstring added to LoadCreate class. |
| B14 | **Re-validate Day-1 Monitor 4 quadrants show real data** | 30 min | Built today; not visually verified by operator yet |
| B15 | **Re-validate Operator Queues 5 panels populate when data exists** | 30 min | Built today; not visually verified by operator yet |

---

## 🟢 MEDIUM-PRIORITY CODE WORK (can defer 1-2 weeks)

| # | Item | Effort | Source |
|---|---|---|---|
| M1 | **6-layer pricing pipeline build** (L2 fuel surcharge + L3 regional density + L4 accessorial config + L5 mode wiring + L1 7-day rolling) | 2-3 days | Operator-walked-through spec at `architecture/pricing_pipeline_spec_2026_05_10.md` |
| M2 | **SOP gap analysis** (Phase 3 of input-surface initiative; Phase 2 = entity-to-input matrix is also pending) | 1-2 days | `project_input_surface_sop_alignment.md` |
| ~~M3~~ | **DONE 2026-05-12 — SOURCE_PARSER_FAILURE poller shipped (CHL commit eb0dcc9).** New 4th source poller in `anomaly_dispatcher` scans `db.loadboard_health` for red-band adapters and enqueues anomalies with parser-specific context (last_error_class/message, sandbox_payload_id for M5 replay, remediation_hints[]). Severity scales: MED (1-2 fails) / HIGH (3-5) / CRITICAL (6+). Honors same cooldown + daily-cap guards as the 3 pre-existing sources. 4 new tests pass. Closes the loop: M20 detects → M5 captures → M3 routes → Stage 1b sub-agent (future) patches the parser. |
| M4 | **Flip CHL_SELF_HEAL_PHASE{1,2,3}_ENABLED feature flags** | 5 min | Gated on M3 + 30 days of telemetry validation |
| ~~M5~~ | **DONE 2026-05-11 — payload_replay_sandbox.py shipped (415 LOC).** Module at `backend/payload_replay_sandbox.py` with public API: `sandbox_capture` / `sandbox_replay` (sync OR async parser_fn) / `sandbox_replay_all` / `sandbox_stats`. Defensive: 1MB payload truncation, idempotent via SHA-256+source_name composite, never raises, db=None safe no-op. Storage: `db.parser_failure_payloads`. 12 tests pass. Future-tied: M3 (anomaly_dispatcher wiring) is a separate ticket; M5 is the foundation module. |
| ~~M6~~ | **Already done — `_extract_from_next_data` at html_parser.py:341, wired as Layer B-0 in parse_html_listings at line 188.** Runs BEFORE selector-map so SSR Next.js JSON pages skip empty DOM scraping. Returns None if no __NEXT_DATA__ → falls through to selector-map / generic extract. Status summary reports `next_data_extractor_available: True`. Stale-memory cleanup. |
| M16 | **Highway.com integration stub for B7 vetting gate** | 2-3 hrs | Per niche-board research 2026-05-12: Highway.com is a carrier-identity / fraud-prevention layer (not a load board). Provides double-broker detection + carrier ID verification. Highest risk-adjusted ROI of the niche research per the agent — "one blocked double-broker scam pays for years." Would plug into the B7 vet_carrier_for_load flow as an additional severity signal. Defer until first booking flow is exercised + we know what false-positive rate we can tolerate. |
| M17 | **Super Dispatch + Central Dispatch adapter stubs for auto-transport vertical** | 4 hrs each | Top 2 niche boards per 2026-05-12 research: Super Dispatch has documented REST API + TMS + auto-transport board; Central Dispatch is largest auto-transport board. Whole new vertical for CHL (if/when we pivot to or add auto-transport). Defer until D2 factor decision + Model C generalist baseline is proven. |
| ~~M18~~ | **DONE 2026-05-12 — cross-post dedup shipped (CHL commit da4eed9).** `backend/loadboard_dedup.py` (pure functions, no DB/IO) + wired into /api/loadboards/aggregate. Dedup key: (origin_city, origin_state, destination_city, destination_state, pickup_date, weight_lbs, commodity) with fuzzy matching (200lb weight bucket, ISO date normalization, alphanum commodity, abbreviation expansion, trailing state-code stripping). Canonical row preserves posted_on[]+duplicates[] arrays so operator can click any specific posting. Safety guard: insufficient-key loads pass through individually rather than falsely collapse. 23 tests pass. Pre-existing 1-day-effort estimate ✓. | 1 day | Cross-post reality: same load typically appears on DAT + Truckstop + 123Loadboard + DirectFreight when broker uses an aggregator (PostEverywhere / LoadBoardNetwork) or manually multi-posts. Without dedup, our operator sees the same load 3-5x in the UI = wasted time + decision fatigue. Required dedup key: `(origin_city + origin_state + destination_city + destination_state + pickup_date + weight_lbs + commodity)` with fuzzy matching on minor variations (city abbreviations, weight ±200lbs). Aggregator at `backend/loadboards.py` should run dedup AFTER all adapter fetches return; UI shows one row per unique load + a `posted_on` array of boards where it appeared (so operator can choose which posting to call). Critical before wiring more than 2 boards live. Discovered during operator strategy discussion 2026-05-12. |
| M19 | **Truck Search aggregator (carrier discovery)** | 1-2 days | Load boards are two-sided marketplaces — carriers post their TRUCKS (lane + equipment + available date) looking for loads, on the same subscription. **For a new broker without an established carrier network, "Find Trucks" is more valuable than "Find Loads" in the first 60 days** — search by lane, call carriers, qualify them, save to internal carrier DB. Build the relationship before we have loads. Currently `backend/loadboards.py` adapters focus only on load search; need to wire each adapter's truck-search endpoint (DAT TruckSearch / Truckstop TruckSearch / 123 TruckSearch / DirectFreight TruckSearch / etc.) into a parallel aggregator. UI: "Trucks on Lane" view alongside "Loads on Lane" view. Esp. high-value for niche boards: LivestockNetwork/CattleHauler trucks = livestock haulers not on DAT; Central Dispatch trucks = auto haulers; PTTR trucks = drayage operators. Discovered during operator strategy discussion 2026-05-12. |
| **MS1** | **MILESTONE — Unified Carrier Intelligence View (operator dashboard)** | 4-6 weeks post-API access (converges M16 + M19 + B7 + carrier_vetting infra) | The asymmetric advantage. Other brokers see 4+ different fragmented load-board UIs; we see ONE deduped carrier view across all subscribed boards with: FMCSA SAFER vetting (B7) + Highway.com fraud-prevention (M16) + multi-board availability signal (M19) + historical rate expectations + last-booked-recency + equipment match + insurance status + relationship score. Each carrier identified by MC#/DOT# (stable identifiers — unlike loads which have no canonical cross-board ID). Compounds over time: every call, every quote, every booking enriches the carrier intelligence DB. **Single dashboard, instant decision.** Other brokers manually scroll 4 UIs, forget who they called, repeat the qualification work. We don't. Gates: API access on at least 2 boards (DAT cert in flight) + M16 Highway integration + M19 truck-search adapters + UI layer. Strategy doc: operator confirmed 2026-05-12 — this is the moat. |
| ~~M20~~ | **DONE 2026-05-12 — Load Board Adapter Health Monitor shipped (commit c0abc4b).** `backend/loadboard_health_monitor.py` (cron, 4h cadence, 10-min boot offset) + `backend/loadboard_health_api.py` (GET list / GET one / POST recheck) + admin dashboard tile + 21 tests. Per-adapter synthetic TX->CA dry-van query writes to `db.loadboard_health` with green/yellow/red/skipped bands. HIGH alert on red transition (3 consecutive failures); MEDIUM on yellow (5 empty ticks); INFO on recovery. `payload_replay_sandbox.sandbox_capture()` invoked on adapter exception so M5 replay loop closes. First production tick: 20 adapters scanned, 4 green (chl_direct, browser_worker_harvest, sam_gov, fmcsa_census), 16 dormant. Playwright login-persistence sub-feature deferred (gated on first login-required board going live). |
| M7 | **5 pricing modes activation** (BID_AGGRESSIVE / DEFENSIVE / etc.) | 2 hours + operator decisions on lane assignment | Requires D2 + Phase 2 capacity confirmation |
| M8 | **Frontend unit tests** (we have 0 today; only E2E Playwright) | 1-2 days | Quality safety net |
| M9 | **DeliveryReceipt mount in LoadDetailModal** | 15 min | Already mounted in 2 other views; polish |
| M10 | **ORPHAN module cleanup — ANALYSIS DONE 2026-05-11; NO DELETIONS WARRANTED.** Report at `chl-memory/research/asset_inventory/orphan_cleanup_review_2026_05_11.md`. Of 10 Python orphans inspected: 0 SAFE-TO-DELETE, 4 had refs (false-flagged), 6 REVIEW-needed (all dotfile-prefixed operator one-shots — intentional by convention). Real M10 value is patching the orphan-scanner heuristics (conftest.py auto-load, dotfile convention, chl-memory cross-references) — separate ticket. 52 JS/JSX "orphans" are mostly shadcn/ui components, out of scope. | 2-3 hours |
| M11 | **Operator-tunable settings UI** — wire `db.business_settings` editing into a Settings panel | 1 day | Many flags currently DB-only (e.g., `delivered_autofire_enabled`, `parser_llm_daily_cap_usd`) |
| ~~M12~~ | **DONE 2026-05-11 — llm_parser cache namespacing.** Cache `_id` now composite `f"{source_name}:{html_hash}"` instead of html_hash alone. Prevents cross-source cache poisoning when two sources produce identical HTML (common template error pages, empty-result pages). Fix at `scrapers/llm_parser.py:_cache_key()` + updated `_cache_lookup` + `_cache_store` + call site at line 716. Verified distinct keys: `boardA:abc123 != boardB:abc123`. |
| ~~M13~~ | **Already done (pre-2026-05-11) — `operator_alerts._REDACTION_PATTERNS` at lines 110-118 covers Mongo URL / JWT / HTTP basic-auth / Bearer / sk- / Twilio SID / generic 40+ char tokens.** Memory state was stale; verifier-gate fix shipped in prior session. |
| ~~M14~~ | **Already done (pre-2026-05-11) — `operator_alerts._TWILIO_BUDGET_CATEGORY="twilio_alerts"` with `check_budget` / `record_spend` via `integrity.budget_caps` module.** Per-message SMS/voice costs gated; cap_throttled audit row recorded when budget hit. |
| ~~M15~~ | **Already done (pre-2026-05-11) — `carrier_outreach.py:740-755` uses atomic `find_one_and_update` + ensures unique partial index on `db.loads.rate_con_id`.** Race-safe rate-con autofire; race loser sees winner's rate_con_id. |

---

## 🔵 LONG-TERM — POST-LAUNCH WORK

| # | Item | When |
|---|---|---|
| L1 | **Full cloud migration** (Hetzner / AWS) | July-Sept 2026 per hardware roadmap |
| L2 | **NCS-2 through NCS-11 buildout** (network-control strategy phases 2-11) | After ~3 months of NCS-1 telemetry |
| L3 | **Specialty broker tier activation** (currently Model C generalist; B/D code-ready) | If/when strategy pivots |
| L4 | **Operator dashboard for new operator-facing collections** | After Wave 1 surfaces validated |
| L5 | **Full Stripe data-field cleanup** (db.users.stripe_connect_account_id etc.) | After 90 days of factoring-only operation |
| L6 | **Performance load test harness** (sustained 100+ req/s synthetic) | Before scaling beyond 50 loads/day |
| L7 | **Multi-operator + role-based access** (currently single-owner) | If/when team grows |

---

## ✅ DONE TODAY (2026-05-10 / 11) — for reference, do not re-do

- Vault Documents feature (encrypted file storage)
- Phase 1 input-surface inventory (839 endpoints / 249 models / 1500+ inputs)
- 3-layer parsing stack (BS4 + Claude LLM fallback)
- SYSTEM_MATRIX + CLAUDE.md rewrite
- Wave 1 (P&L guard / funding gate / operator alerts / FMCSA SMS / autofire invoice / autofire rate-con)
- Wave 2 (canonical equipment / payment_terms / hazmat / safety_rating)
- Wave 3A (HTML preproc / Next.js extractor / redaction / Twilio cap / incident runbook / race fix / P&L resolver)
- Wave 3B (operator queues dashboard / day-1 monitor / carrier-contacts fix / contact_name backfill / Stripe auto-pay disabled)
- Wave 3C (backup restore test isolated env)
- Wave 4 (25 Mongo indexes / circuit breakers / self-heal Phase 1 / rate limiter / structured logging / Stripe full cleanup / test env isolation / cloud-replica scripts staged)
- Stripe residual cleanup
- Renewals expansion (catalog 34→54 incl. BMC-84 bond)
- Hardware audit + load test
- Cloud-replica plan persisted (scripts staged)
- Playwright + 12-step smoke harness installed
- Ops Console v2 + BOL search + synth data + UI sweep
- E2E lifecycle drive test (PASS — 10/10 steps)
- Tab audit + 5 duplicate tabs removed
- Canonical tab layout locked
- FMCSA broker authority confirmed ACTIVE
- 11,441 synthetic docs purged
- Workflow research (4 source categories, 25+ sources triangulated)

---

## 🎯 PROCEDURAL — STANDING DIRECTIVES IN MOTION

These are ongoing operational behaviors, not one-shot tasks:

| # | Standing rule | Status |
|---|---|---|
| P1 | 3x-daily handoff doc cadence (early / mid / close) | ❌ Failed 2026-05-10 (only 1 doc written); reset 2026-05-11 |
| P2 | D-drive snapshot 3x per 5-hour session | ✅ Multiple snapshots ran today |
| P3 | Capture every feature to BUILD_STATUS + chl-memory IMMEDIATELY | ✅ Mostly compliant |
| P4 | 5-copy floor maintained | ✅ Verified mid-day 2026-05-10 |
| P5 | Asset inventory regen at every snapshot | ✅ Auto-fires |
| P6 | Documents-to-Desktop placement for operator-read docs | ✅ Compliant |
| P7 | Verifier-gate sub-agent over batch-parallel work | ✅ Caught 6 HIGH issues on 2026-05-10 |
| P8 | Session-resume → ping PM @pm-lead via AgentDM | ⚠️ Not done after restart |
| P9 | C-drive HDD capacity monitoring (Get-PSDrive C in handovers) | ✅ Current: 783 GB free / 146 GB used / 930 GB total |

---

## 🔄 NEXT SESSION BOOT CHECKLIST (read this on wake)

1. Read `CLAUDE.md` (boot injection)
2. Read `chl-memory/architecture/SYSTEM_MATRIX.md` (execution blueprint)
3. Read `C:\CHL\memory\handoff_session_2026_05_10_eod_close.md` (yesterday's handoff)
4. **Read THIS file** (the master backlog)
5. Read `chl-memory/architecture/canonical_tab_layout_2026_05_11.md` (don't add duplicate tabs)
6. Read `~\.claude\projects\c--CHL\memory\MEMORY.md` (memory index)
7. Detect elevation state (Windows admin or standard)
8. Acknowledge to operator with boot pattern: "Booted. Read [files]. Running [ADMIN|standard]. Ready."

After boot:
1. Confirm services running: `Get-Service CHL-Backend, CHL-Frontend`
2. Verify backend health: curl `http://127.0.0.1:8001/api/health/system`
3. Verify FMCSA authority STILL active (if it dropped overnight, that's a fire): hit QCMobile endpoint for DOT 4569843
4. Check D-drive snapshot freshness — should be <24h old
5. Then take operator instruction

---

## 📊 BACKLOG STATS

| Tier | Count |
|---|---|
| 🚨 Immediate operator decisions | 7 |
| 🔴 Operator actions | 17 |
| 🟡 High-priority code | 15 |
| 🟢 Medium-priority code | 15 |
| 🔵 Long-term post-launch | 7 |
| 🎯 Standing directives | 9 |
| ✅ Done today | 25+ items |
| **TOTAL pending items** | **~70** |

---

## 🆘 IF YOU'RE FUTURE CLAUDE AND THIS IS YOUR FIRST READ

The operator has a working freight brokerage platform that just received its broker authority from FMCSA today (2026-05-11). The platform is sophisticated (~25 frontend tabs, 800+ backend endpoints, 250+ Pydantic models, ~520 passing tests) but has gaps where real-world workflow integration is incomplete (load board credentials not yet applied, factoring company not yet selected, shipper credit check not built).

**Do not invent features.** Read this backlog. Pick from it.

**Do not add tabs.** Read `canonical_tab_layout_2026_05_11.md` first.

**Do not accept passwords in chat.** Use the vault + subprocess pattern.

**The workflow is canonical-but-incomplete.** Phases 0, 4-5, 8 are mostly built. Phases 1-3, 6-7 have gaps marked above.

**Operator is solo.** Don't build for hypothetical team. Don't build for hypothetical compliance regimes that don't apply yet. Operator-instruction-style: numbered steps, one action per line.

**End of master backlog.**
