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
| O19 | **Update FMCSA SAFER public contact info** — add phone + email to public record. DirectFreight verify showed `missing phone number` / `missing email address` for DOT 4569843 (2026-05-11). SAFER lags new authorities; manually pushing contact info via FMCSA Portal eliminates auto-verify failures on every future partner platform. | $0 | 30 min | Unblocks self-serve verify on every load board / factor / TMS. |

---

## 🟡 CODE GAPS — high-priority build items (next 1-2 sessions)

| # | Item | Effort | Why |
|---|---|---|---|
| B1 | **Remove PRE-AUTHORITY MODE banner from dashboard** | 15 min | Authority is now ACTIVE per FMCSA; banner is stale. Update authority-detection logic. |
| ~~B1b~~ | **DONE 2026-05-11 — SAM.gov adapter switched from NAICS to PSC filter + title-keyword belt-and-suspenders + US-only default.** Replaced unreliable `naics=` filter with PSC codes V111/V112/V114/V119/V121/V122/V125/V302/V303 (federal-procurement service codes — verified reliable). Added title-regex filter (`transport\|freight\|cargo\|shipping\|hauling\|trucking\|drayage\|intermodal\|tanker\|flatbed\|truckload\|cartage\|stevedor\|household goods\|barge\|movement of\|van transport`). Default `us_only=True` to skip overseas RFPs. Manual smoke test 2026-05-11 showed 22 actual freight RFPs across V112/V114/V119 in 30d. Code at `loadboards.py:145-260` (SamGovAdapter). Live verification gated until rate-limit reset 2026-05-12 00:00 UTC. |
| B1c | **SAM.gov rate-limit upgrade to non-federal 1k/day tier** | varies | Current API key appears throttled to "general 10/day" tier — burned through quota on initial smoke tests 2026-05-11. To unlock 1k/day non-federal tier, complete SAM.gov entity registration (UEI + CAGE code — same as backlog O5 / blocked on UEI). Federal-user 10k/day tier requires GovCloud-attached account, not applicable to us. |
| B2 | **Shipper credit check ("Pre-clear with factor")** — Phase 2 of canonical workflow | 1-2 days | Operator's "Step 2"; requires factor company selection (see D2) |
| B3 | **Submit-to-factor post-POD flow** — Phase 6 | 1 day | Email template or factor API integration |
| B4 | **Manual "Pay Carrier" trigger on Accounting tab** | 4 hours | Currently view-only; needs button per audit |
| B5 | **Acknowledge button on Operator Alerts panel** | 2 hours | Audit finding — alerts pile up with no clear action |
| B6 | **Cold-Outreach button on CarrierDetailModal** (currently only on Load Detail) | 3 hours | Audit finding — operator workflow lives on wrong screen |
| B7 | **Per-load vetting gate `vet_for_load(mc, load)`** | 1 day | Research file #4 + Wave 1 infrastructure; wire into dispatch path |
| B8 | **set_env_var.ps1 helper** | 2 hours | Standing operator directive since 2026-05-08 — never built. Eliminates manual `.env` edit friction. |
| B9 | **Fix "Post Load" → "+ Add Load" label drift** OR fix SOP to match | 5 min | Audit finding — label mismatch with SOP |
| B10 | **Fix BOL Search "Search" → "Find Documents" label** OR keep | 5 min | Audit finding (low priority since tab removed; but the BOL Quick Search card inside Ops Console v2 still uses "Search" label) |
| B11 | **Make renewal urgency-band tiles clickable filters** | 1 hour | Currently read-only counters; should filter the list below |
| B12 | **Add "policy/reference number" edit field to renewal rows** | 30 min | Operator needs to record actual policy numbers per renewal |
| B13 | **Production load creation form fields enforce 49 CFR §371.3 minimums** | 4 hours | Earlier dashboard showed 0/11k loads federal-compliant; verify new loads satisfy the 6 §371.3 fields |
| B14 | **Re-validate Day-1 Monitor 4 quadrants show real data** | 30 min | Built today; not visually verified by operator yet |
| B15 | **Re-validate Operator Queues 5 panels populate when data exists** | 30 min | Built today; not visually verified by operator yet |

---

## 🟢 MEDIUM-PRIORITY CODE WORK (can defer 1-2 weeks)

| # | Item | Effort | Source |
|---|---|---|---|
| M1 | **6-layer pricing pipeline build** (L2 fuel surcharge + L3 regional density + L4 accessorial config + L5 mode wiring + L1 7-day rolling) | 2-3 days | Operator-walked-through spec at `architecture/pricing_pipeline_spec_2026_05_10.md` |
| M2 | **SOP gap analysis** (Phase 3 of input-surface initiative; Phase 2 = entity-to-input matrix is also pending) | 1-2 days | `project_input_surface_sop_alignment.md` |
| M3 | **Wire SOURCE_PARSER_FAILURE into self_healing.anomaly_dispatcher** | 30 lines | Gated on real load-board connection (O1-O4) producing parser failures |
| M4 | **Flip CHL_SELF_HEAL_PHASE{1,2,3}_ENABLED feature flags** | 5 min | Gated on M3 + 30 days of telemetry validation |
| M5 | **payload_replay_sandbox.py module** for self-correction loop | 4 hours | Captures real failed payloads + replays against patched parsers |
| M6 | **Layer B Next.js `__NEXT_DATA__` extractor** for HTML parser | 1 hour | BulkLoads-style sites failed today's parser; this fixes them |
| M7 | **5 pricing modes activation** (BID_AGGRESSIVE / DEFENSIVE / etc.) | 2 hours + operator decisions on lane assignment | Requires D2 + Phase 2 capacity confirmation |
| M8 | **Frontend unit tests** (we have 0 today; only E2E Playwright) | 1-2 days | Quality safety net |
| M9 | **DeliveryReceipt mount in LoadDetailModal** | 15 min | Already mounted in 2 other views; polish |
| M10 | **ORPHAN module cleanup** (52 orphans flagged by asset inventory) | 2-3 hours | Internal hygiene |
| M11 | **Operator-tunable settings UI** — wire `db.business_settings` editing into a Settings panel | 1 day | Many flags currently DB-only (e.g., `delivered_autofire_enabled`, `parser_llm_daily_cap_usd`) |
| M12 | **Cache namespacing in llm_parser** (SHA-256 cache should include source_name) | 30 min | Verifier-gate flagged 2026-05-10 |
| M13 | **Mongo URL/JWT/basic-auth redaction patterns in operator_alerts** | 30 min | Verifier-gate flagged 2026-05-10 |
| M14 | **Twilio alerts $5/day budget cap** | 30 min | Verifier-gate flagged 2026-05-10 |
| M15 | **Rate-con autofire race fix** (unique partial index + atomic find_one_and_update) | 30 min | Verifier-gate flagged 2026-05-10 |

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
