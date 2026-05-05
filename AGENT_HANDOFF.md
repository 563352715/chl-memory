# 🛡️ AGENT_HANDOFF.md — READ THIS FIRST

> **Purpose:** Single entry-point document for any new agent / coder /
> contributor joining this codebase. Read this BEFORE writing any code,
> proposing any feature, or answering any question from Jason.
>
> **Last updated:** 2026-05-04 (Iter 139.36 close-out hardening)
> **Current iteration:** 139.36
> **Maintainer:** Jason Aaron Meyer (sole owner, broker, operator)

---

## 🚨 PROTOCOL FOR ANY NEW AGENT SESSION (do this in order)

1. **Read** `/app/memory/FACTS.md` — IMMUTABLE company identity. Never invent these.
2. **Read** `/app/memory/CAPABILITIES.md` — what's already built. Don't propose to rebuild.
3. **Skim the last 15 entries** of `/app/memory/SESSION_LOG.md` — what was done in the past 14 days.
4. **Read** `/app/memory/PRD.md` — current backlog and priorities.
5. **Skim** `/app/memory/OPERATING_MANUAL.md` — the doctrine that the platform implements.
6. **Output the FACT CONFIRMATION + SESSION CONTINUITY blocks** as the very first thing you say to Jason.

If you skip any of steps 1-5, you WILL repeat work, propose features that exist, or contradict the doctrine. Don't.

---

## 🔁 GIT SYNC PROTOCOL (standing rule — every iteration)

**Why this exists:** Jason runs a parallel local copy of this codebase in VS Code on a Windows desktop and pulls from GitHub between sessions. Every shipped iteration must be reflected on the remote so the local copy can `git pull` and stay in sync.

**Platform constraint (from Emergent support):** Agents *cannot* run `git push` directly. All GitHub sync flows through the **"Save to GitHub"** button in the chat input — manual user action required each time. There is no programmatic or scheduled push available on this platform.

**Standing protocol — every agent must follow at iteration close-out:**

1. Append the iteration row(s) to `/app/memory/SESSION_LOG.md`.
2. Bump the changelog in `/app/memory/PRD.md` and update the `Current State` line.
3. Update `/app/memory/AGENT_HANDOFF.md` if any rule under "DO NOT DO" or the critical-file map changed.
4. **As the final line of the `finish` summary**, include a one-liner reminder:
   > 🔁 **Sync to GitHub:** click *"Save to GitHub"* in the chat input to push Iter X.Y to the remote so VS Code can `git pull`.
5. Do **NOT** attempt `git push`, `git commit`, `gh ...`, or any direct remote write — the platform blocks these and they will silently no-op.

**When the reminder must be omitted:** never. Even pure documentation iterations need a sync so the local VS Code copy mirrors the agent's view of `/app/memory/*.md`.

---



**What CHL is:** A 1-broker freight brokerage (Continental Haul Logistics LLC, MC-1817555, USDOT 4569843) operated entirely by Jason Meyer with a custom-built TMS (this codebase) that automates ~85% of the operational workflow. Jason is the only employee.

**Why this matters:** The platform is NOT a SaaS for sale. It is Jason's own tool to run his own freight brokerage. Every feature must serve operational throughput, compliance, or cash-flow visibility for a 1-broker shop. Refactoring or generalizing the code is explicitly OUT OF SCOPE per Jason's standing rule.

**Mission:** Run a fully automated freight brokerage where Jason oversees, intervenes, and approves — but the TMS handles the wire-up: RFQ parsing, carrier vetting, rate-cons, dispatch, GPS tracking, exception handling, settlement, factoring, and compliance.

**Mode today:** TESTING. Authority granted: PENDING (FMCSA protest period ends **2026-05-13**, ~9 days from this writing). Until then, every load doc auto-stamps "PENDING — DO NOT EXECUTE" and `POST /api/loads` is throttled to **1 load/day**.

---

## 🟢 LIVE TODAY (no-touch)

### What shipped 2026-05-04 (Iter 139.7 → 139.36 — full day's work)

These are all done. Don't re-propose them.

| Iter | Module | One-line summary |
|---|---|---|
| 139.7 | doctrine + audit | SOPPlaybookView Cards 5–14 narrative · OPERATING_MANUAL.md RACI gap-fill + GAP-marker resync · `CODEBASE_AUDIT_2026_05_04.md` written |
| 139.8 | infra | (held — log slot) |
| 139.9 | tier-1 prep | Build-spec for Tier-1 Quick Wins decided + gated under `live_mode` |
| 139.10 | tier-1 prep | Schema additions for `cancellation_reason`, `audit_blocks_invoice`, `quote_outcome` |
| 139.11 | UI · invoices | InvoicePreview view shipped (line-by-line preview before send) |
| 139.12 | UI · claims | CargoClaimsView shipped (open / list / resolve cargo claims) |
| 139.13 | UI · detention | DetentionReviewView shipped (review + bill detention disputes) |
| 139.14 | UI · fraud | FraudConsoleView shipped (double-broker + bankruptcy review) |
| 139.15 | UI · AR | AgedARView shipped (4-bucket aged AR with day-count drill-down) |
| 139.16 | verification | Smoke-tested 139.11-139.15 + AR cron firing nightly |
| 139.17 | cron | Day-1 readiness email cron (`day1_readiness_email.py`, Sun 19:00 CT, idempotent per ISO week) |
| 139.18 | (held) | — |
| 139.19 | tier-1 GAP 1 | `cancellation_reason` enum on cancelled loads + 12-value reason_code modal on LoadDossier |
| 139.20 | tier-1 GAP 2 | Audit-blocks-invoice gate (POST /api/broker-invoice/execute returns 409 if `audit_failed_at` is set, with Sovereign Override carve-out) |
| 139.21 | tier-1 GAP 3 | ETA-slip detector + ETASlipWidget on /ops |
| 139.22 | tier-1 GAP 4 | Auto-quote draft generator + RFQ Inbox preview-and-send pane |
| 139.23 | tier-1 GAP 5 | Win/loss field dropdown on QuoteView |
| 139.24 | tier-1 GAP 6 | Shipper-block enforcement + ShipperScorecardView block-toggle UI |
| 139.25 | (held) | — |
| 139.26 | win/loss UI | Inline win/loss strip on QuoteView rows |
| 139.27 | (held) | — |
| 139.28 | (held) | — |
| 139.29 | P1 close-out | Cancel triad: cancellation_reason enum + auto-TONU calculator (10% of contract, $150 floor) + shipper scorecard penalty wiring inside `state_lock.py` |
| 139.30 | P1 close-out | 24h silent-shipper ping cron (`silent_shipper_ping.py`, every 30min) — pings 24-72h-old quotes, auto-expires >72h |
| 139.31 | P1 close-out | Auto credit-check on first lead (`auto_credit_check.py`, 30d cache, MOCKED until HaulPay API token lands) |
| 139.32 | P1 close-out | Demand-letter generator (3-tier tone: friendly@45d / firm@60d / legal@90d) + cargo-claim packet generator (Carmack Amendment, 270d filing deadline) → `collections_packets.py` + Resend send |
| **139.33** | **P0** | **Trust Footprint Updater** — one-click EIN/DUNS/MC#/DOT/BMC-84 push to all 8 connected loadboards & factor partners. Endpoints `POST /api/admin/trust-footprint/{push/{slug},push-all}` + `GET /{status,preview-payload}`. Audit at `db.trust_footprint_pushes`. `TrustFootprintWidget` mounted on `/ops`. |
| **139.34** | **P0** | **Posting Blob Generator** — paste-ready broker-identity + per-load posting blocks for manual-only loadboards (Sylectus, NextLoadBoard). 3 formats: plain / markdown / compact. Endpoints `GET /api/admin/posting-blob/{identity,load/{id}}`. `PostingBlobWidget` on `/ops` w/ copy-to-clipboard. |
| **139.35** | **P2** | **Find Loads for this Carrier** — `carrier_load_match.py` merges `carrier_preferences` + carrier doc + driver `preferred_lanes`. CTA on CarrierDetailModal navigates to `/loads?origin_state=…&equipment=…`. `LoadBoardAggregator` reads URLSearchParams on mount to auto-apply prefilters. |
| **139.36** | **🛠️** | **Synthetic Load Replay** — spawn complete fake load + quote + carrier + carrier_interests at any of 10 SOP stages in 1 click. `_build_stage_fields(stage, now)` writes only the milestones needed for `sop_engine.derive_stage()` to land on the requested stage. All synthetic docs tagged `synthetic:true` + `replay_run_id`. Endpoints `POST /api/admin/synthetic-load/{create,cleanup}` + `GET /{list,valid-stages}`. Widget on `/ops`. |
| **139.36** | **🛡️ close-out** | **Synthetic data quarantine + auto-wipe.** New `synthetic_filter.py` (`with_excl()` helper). New `synthetic_auto_wipe.py` (hourly cron, deletes synthetic rows >24h old). 18 query sites across failure_detectors (2), failure_detectors_part2 (11), ops_state (3), silent_shipper_ping (2), aged_ar (1) now exclude synthetic. Cron scheduler arms 6 loops (was 5). Endpoints `POST /api/admin/synthetic-auto-wipe/tick` + `GET /status`. |
| **139.36** | **🛡️ close-out drift detector** | **`/api/admin/synthetic-audit`** — read-only static-scan that lists per-collection live synthetic counts + flags every backend module querying a synthetic-bearing collection without the synthetic-exclusion filter. PK lookups (`find_one({"id":X})`) are auto-allowed since they can't leak. "Audit drift" button on SyntheticLoadReplayWidget. |

**Doctrine parity post-139.36 close-out:** 69 ✅ / 16 ⚠️ / 16 🚧 / 101 total = **68.3%**.

---

## 🟢 LIVE TODAY (no-touch)

| System | Status |
|---|---|
| FMCSA MC-1817555 / USDOT 4569843 | ✅ Issued, pending grant 5/13 |
| BMC-84 surety bond ($75K, Westfield, #540381P) | ✅ ACTIVE on FMCSA L&I |
| BOC-3 process agent | ✅ Filed |
| Resend email (dkim+spf at continentalhaul.com) | ✅ LIVE |
| Twilio voice (browser softphone + dispatch line + toll-free) | ✅ LIVE |
| Zoho Mail IMAP (RFQ poller) | ✅ LIVE |
| Free Caller Registry (anti-spam-flag) | ✅ Submitted 2026-05-04, 180-day expiry |
| MongoDB (210 collections) | ✅ LIVE |
| Backend (FastAPI, ~140 modules) | ✅ LIVE |
| Frontend (React SPA, ~115 views) | ✅ LIVE |
| Pre-Vet pipeline (50 carriers vetted + promoted) | ✅ LIVE |
| 12/12 Failure-Mode detectors armed | ✅ LIVE (Iter 138.8) |
| Compliance auto-watcher (A2P + TFV) | ✅ LIVE (15-min cadence, emails on flips) |
| Legal Readiness Dashboard on /ops | ✅ LIVE |
| Day-1 Throttle (1 load/day cap) | ✅ ENFORCED |

---

## 🟡 IN-FLIGHT (waiting on external)

| Item | Status | When |
|---|---|---|
| FMCSA broker authority grant | Pending — protest period ends 2026-05-13 | 9 days |
| A2P 10DLC campaign | Resubmitted to TCR 2026-05-03 with updated Privacy + Terms URLs | 24-72h vetting |
| TFV (Toll-Free Verification) | **Iter 139.3-139.4**: full SMS-consent compliance fix on /#quote → reply sent to reviewer <REDACTED-THIRD-PARTY-REVIEWER> → production redeployed → TFV currently in "verification in progress" status as of Iter 139.5 | Awaiting auto-recheck |
| HaulPay factoring | Application submitted; awaiting carrier ID + API token | TBD |
| 123Loadboard API | Application emailed to partner-integrations@ on 2026-05-04 | 2-6 weeks to prod creds |
| 123Loadboard Business Profile | ✅ Logo uploaded (Iter 139.1, chl-logo-400.jpg) — DUNS to be added when verified | Manual edit anytime |
| Truckstop.com (Pro Load Board) | **Iter 139.5**: account exists but registered as carrier (per URL trace `/product/load-board/carrier/`); duplicate-account block prevents new broker signup. Login + password vaulted. **Pending phone call to 1-800-203-2540** to convert to broker tier. | User-side action |
| D&B Business Credit Profile | DUNS issued 2026-05-04 (Iter 139.2). Profile setup at dnb.com pending — unlocks PAYDEX score | User-side action |
| SAM.gov registration | Unblocked by DUNS; not started | Backlog |
| Twilio CNAM display name | Not yet started | Backlog (paid 3rd party or support ticket) |
| Hiya permanent registration (replaces FCR's 180-day expiry) | DUNS-blocker cleared 2026-05-04, ready to start | Within 30 days |

---

## ❌ DO NOT DO

These are Jason's standing rules. Violating any one of them wastes hours.

1. **Do NOT refactor `App.js` or `server.py`.** Yes they're huge (10K+ lines each). No, do not carve them up. Jason has explicitly deprioritized this. Add code in new modules and import.
2. **Do NOT propose authentication changes** without first calling `integration_playbook_expert_v2`. Auth bugs are the #1 recurring issue. JWT cookies + ADMIN_PASSWORD env + bcrypt — don't touch the chain without consulting the playbook.
3. **Do NOT invent FMCSA, EIN, BMC-84, or any compliance numbers.** Read from FACTS.md.
4. **Do NOT generalize features for "future use."** This is a 1-broker tool, not a SaaS. Only the minimum needed for THIS broker's THIS workflow.
5. **Do NOT use `localhost:8001` in any e2e test.** Use `REACT_APP_BACKEND_URL` from `frontend/.env`.
6. **Do NOT mark a Day-1 gate green by hand-editing the DB.** The gates read from canonical sources (FMCSA L&I, Twilio API, prevet_targets, etc.) — fix the source if a gate is wrong, not the gate logic.
7. **Do NOT email anyone on Jason's behalf without showing him the draft first.** All Twilio / FMCSA / 123Loadboard / HaulPay correspondence goes through him.
8. **Do NOT use emoji icons in components.** Use FontAwesome / lucide-react / Phosphor (already installed).

---

## 📍 CRITICAL FILE MAP

### Memory (read these first)
| File | Purpose |
|---|---|
| `/app/memory/FACTS.md` | **IMMUTABLE.** Identity, MC, USDOT, EIN, bond, addresses. Never invent. |
| `/app/memory/CAPABILITIES.md` | Honest inventory of what's built. ~140 backend modules, ~115 views, 9 integrations. |
| `/app/memory/OPERATING_MANUAL.md` | Doctrine — what the platform implements + 🚧 GAP markers. |
| `/app/memory/SESSION_LOG.md` | Append-only iteration journal. Mirrors db.agent_journal. |
| `/app/memory/PRD.md` | Current backlog + priorities + recent changelog. |
| `/app/memory/DAY1_PLAN.md` | The Day-1 launch playbook (5/13/26 target). Includes ramp policy 1→3→5→10. |

### Compliance packets (paste-ready when Jason needs to file)
| File | Purpose |
|---|---|
| `/app/memory/A2P_10DLC_RESUBMISSION_PACKET.md` | A2P campaign rejection fix language. Used Iter 138.5. |
| `/app/memory/TOLLFREE_RESUBMISSION.md` | Original TFV packet doc. |
| `/app/memory/CNAM_SUPPORT_TICKET.md` | CNAM / caller-ID display-name application. |
| `/app/memory/123LOADBOARD_API_APPLICATION.md` | 123Loadboard API access application (sent Iter 138.9). |
| `/app/memory/TWILIO_BROWSER_PHONE_PLAYBOOK.md` | Browser softphone setup. |

### Audit docs (deep-dive references)
| File | Purpose |
|---|---|
| `/app/memory/COMPLIANCE_AUDIT.md` | Compliance posture audit. |
| `/app/memory/E2E_LOAD_LIFECYCLE_AUDIT.md` | Load-lifecycle E2E audit. |
| `/app/memory/SOP_AUDIT.md` | SOP audit. |
| `/app/memory/TAB_AUDIT_2026_05_03.md` | UI tab consolidation audit. |
| `/app/memory/HARDWARE_MAP.md` | Jason's office equipment + ergonomic setup. |
| `/app/memory/CHL_Technical_Whitepaper_v76.md` | Technical whitepaper for outside parties. |

### Code architecture
```
/app/
├── backend/                       # FastAPI monolith
│   ├── server.py                  # 10K+ lines — DO NOT REFACTOR
│   ├── ops_state.py               # /api/ops/state (canonical readiness)
│   ├── failure_detectors.py       # Iter 135.12 — 4 launch-critical detectors
│   ├── failure_detectors_part2.py # Iter 138.8 — 8 additional detectors (BOL/detention/ghosting/etc.)
│   ├── compliance_watcher.py      # Iter 138.8 — A2P/TFV approval poller
│   ├── tollfree_compliance.py     # TFV resubmit + create-new endpoints
│   ├── notification_service.py    # SMS gating, A2P status cache
│   ├── carrier_prevet.py          # Pre-Vet pipeline (FMCSA-backed)
│   ├── sop_engine.py              # 10-stage SOP pipeline
│   ├── browser_phone.py           # Twilio Voice WebRTC tokens
│   ├── tcr_status.py              # A2P 10DLC status fetch
│   ├── agent_journal.py           # Continuity log
│   ├── cron_scheduler.py          # Centralized crons
│   ├── loadboards.py              # 18 board adapters (2 LIVE, 16 awaiting keys)
│   ├── boot_briefing.py           # /api/ops/briefing (Claude-rendered)
│   └── ... (~140 modules total)
├── frontend/
│   ├── src/
│   │   ├── App.js                 # 11K+ lines monolith — DO NOT REFACTOR
│   │   ├── views/
│   │   │   ├── OperatorConsoleView.jsx   # /ops — Day-1 cockpit
│   │   │   ├── SopFlowView.jsx           # /sop-flow — kanban
│   │   │   ├── CarrierPrevetView.jsx     # /prevet
│   │   │   ├── FactorMatrixView.jsx      # owner-only
│   │   │   ├── FactorAPIWiringView.jsx   # owner-only
│   │   │   ├── LoadBoardAggregator.jsx   # /loads (Find Loads)
│   │   │   └── ... (~115 views total)
│   │   └── ...
│   └── public/sw.js               # Service Worker (v6 cache-bust per Iter 138)
└── memory/                         # All the above docs
```

---

## 🛠️ ENVIRONMENT VARIABLES (sensitive — read from .env, never hardcode)

Configured (DO NOT DELETE):
- `MONGO_URL`, `DB_NAME`, `REACT_APP_BACKEND_URL` — protected
- `ADMIN_PASSWORD=<REDACTED-PASSWORD-2026-04>` — Jason's email-login password (auto-synced on boot)
- PIN: `<REDACTED-PIN>` (preferred login method)
- `TWILIO_ACCOUNT_SID`, `TWILIO_AUTH_TOKEN`, `TWILIO_API_KEY_SID`, `TWILIO_API_KEY_SECRET`, `TWILIO_TWIML_APP_SID`, `TWILIO_MESSAGING_SERVICE_SID`, `TWILIO_BROKER_FORWARDING_NUMBER`
- `RESEND_API_KEY`, `OPERATOR_NOTIFY_EMAIL` (defaults to <REDACTED-EMAIL>)
- `EMERGENT_LLM_KEY` — Universal LLM key (used by boot_briefing for Claude)
- `ZOHO_IMAP_PASSWORD` — for RFQ poller

Missing (keys we DON'T have but adapter is coded):
- `LOADBOARD_123_API_KEY` — pending 123Loadboard reply (Iter 138.9)
- `DAT_API_KEY`, `TRUCKSTOP_USER`/`PASS`/`INTEG_ID`, `LOADSMART_KEY`, `UBER_FREIGHT_KEY` — all coded, all dark
- `HAULPAY_API_TOKEN` — pending HaulPay approval
- `FMCSA_QCMOBILE_WEB_KEY` — falls back to local `fmcsa_carriers` census (452K rows)

---

## 🔐 TEST CREDENTIALS

See `/app/memory/test_credentials.md` if it exists. Today's working set:
- **PIN login:** `<REDACTED-PIN>` ← preferred
- **Email login:** `<REDACTED-EMAIL>` / `<REDACTED-PASSWORD-2026-04>`
- Both are auto-synced from `ADMIN_PASSWORD` env on every backend boot (`seed_admin()`). NEVER edit the user doc by hand — fix the env instead.

---

## 🚦 DAY-1 GATES (current state: 4/8 passed)

The single source of truth: `GET /api/ops/state` → `day1_gates.checks`

| # | Gate | State | What flips it |
|---|---|---|---|
| 1 | `authority_granted` | ❌ pending | FMCSA L&I status flips to "active" on 5/13 |
| 2 | `bond_on_file` | ✅ PASSED | BMC-84 #540381P confirmed in FMCSA |
| 3 | `factor_token_in_vault` | ❌ | HaulPay sends API token → drop in `.env` |
| 4 | `a2p_10dlc_approved` | ❌ in vetting | TCR approves campaign (resubmitted Iter 138.5) |
| 5 | `tfv_approved` | ❌ in vetting | Twilio approves TFV (resubmitted Iter 138.5) |
| 6 | `min_pre_vetted_carriers` (≥25) | ✅ PASSED | 50 carriers in `prevet_targets{status:"preferred"}` after Iter 138.4 |
| 7 | `resend_domain_verified` | ✅ PASSED | continentalhaul.com DKIM/SPF green |
| 8 | `zoho_imap_live` | ✅ PASSED | RFQ poller confirmed within 1h |

---

## 📅 NEXT-AGENT BACKLOG (priority-ordered)

### 🔴 P0 — User-side actions remaining (Jason does these)
1. ~~Verify MC# 1817555 on 123Loadboard~~ — **BLOCKED until 2026-05-13** (FMCSA authority grant)
2. ~~Upload CHL logo on 123Loadboard~~ — ✅ **DONE Iter 139.1** (chl-logo-400.jpg uploaded)
3. ~~Get free DUNS number~~ — ✅ **DONE 2026-05-04 (Iter 139.2)** — DUNS = **145008621** (24h fast-track turnaround)
4. ~~Twilio TFV reply to <REDACTED-THIRD-PARTY-REVIEWER>~~ — ✅ **DONE Iter 139.3-139.4** (SMS consent block built + production redeployed)
5. ~~SOPPlaybookView Cards 5–14 narrative~~ — ✅ **DONE Iter 139.7** (20 cards across 2 models)
6. ~~OPERATING_MANUAL.md RACI gap-fill + GAP-marker resync~~ — ✅ **DONE Iter 139.7**
7. ~~Codebase audit vs OPERATING_MANUAL.md~~ — ✅ **DONE Iter 139.7** (`/app/memory/CODEBASE_AUDIT_2026_05_04.md`)
8. **📞 Call Truckstop 1-800-203-2540** — convert existing account from carrier→broker tier (Iter 139.5)
9. **Resume Hiya Connect permanent registration** at https://hiya.com/registration — was blocked on DUNS, now unblocked. (Note: D&B propagation 3-7 days — try Mon-Fri next week)
10. **Set up D&B business credit profile** — log in to dnb.com, complete company info & financials → unlocks PAYDEX score & creditworthiness signals
11. **Register on SAM.gov** with new DUNS — opens federal & state government freight contracts
12. **Re-add Twilio Emergency Calling Address** for (866) 490-6433 — currently shows "Failed to register" → $75/call risk if 911 dialed (separate from TFV, low priority)

### 🟠 P0/P1 — Code work
1. **Watch for 123Loadboard reply** (`partner-integrations@`) → drop credentials in `.env` → adapter goes live instantly
2. **Watch for HaulPay reply** → drop API token in vault → Gate #3 flips green
3. **Twilio CNAM** — paid 3rd party (Numeracle, NextCaller) or Twilio support ticket. Packet ready at `/app/memory/CNAM_SUPPORT_TICKET.md`
4. ~~SOPPlaybookView Cards 5–14 narrative~~ — ✅ **DONE Iter 139.7**
5. ~~OPERATING_MANUAL.md missing RACI rows + GAP markers~~ — ✅ **DONE Iter 139.7**
6. **Weekly Sunday-evening Day-1 readiness summary email** (~15 min) — ✅ **DONE Iter 139.17** (cron at `backend/day1_readiness_email.py`; fires Sun 19:00 America/Chicago; idempotent per ISO week; preview at `GET /api/admin/day1-readiness/preview`)
7. **Tier-1 GAP quick wins (~3 sessions total)** from `/app/memory/CODEBASE_AUDIT_2026_05_04.md`: `cancellation_reason` field · audit-blocks-invoice wiring · ETA-slip detector · auto-quote draft generator · win/loss field · shipper-block enforcement
8. ~~Tier-2 UI surfaces (~5 sessions total)~~ — ✅ **DONE Iter 139.11–139.15, verified 139.16** (InvoicePreview · CargoClaims · DetentionReview · FraudConsole · AgedAR)
9. **"Trust Footprint Updater"** (~45 min, P2 candidate) — one-click push of EIN/DUNS/MC#/DOT to all loadboards & factor partners as their APIs come online

### 🟢 P2 — Backlog
10. "Find loads for this carrier" wired to `saved_searches` + `carrier_preferences`
11. Mercury Treasury Transfers write-token integration
12. Cross-border (CA/MX) paperwork guard
13. "Generate Posting Blob" tool for manual-only loadboards
14. DAT / Truckstop / Loadsmart / Uber Freight credentials when contracts close

---

## ✅ VERIFICATION JASON OWES (low-pri, his choice)

These are shipped and tested via screenshots/curl/lint. Jason hasn't manually clicked through, but the agent verified each. Listed in case Jason asks "is X really working?":
- PIN login fix (Iter 138)
- Nav cleanup (Iter 138.1)
- Live Map merge (Iter 138.2) ✅ **confirmed by Jason**
- Honesty pass on /api/ops/state (Iter 138.3)
- Bulk Approve + Factor rename (Iter 138.4)
- Compliance auto-resubmit (Iter 138.5)
- Inbound voice routing + Caller-ID trust (Iter 138.6/138.7)
- Failure detectors + Legal Readiness Dashboard + Compliance watcher (Iter 138.8)
- 123Loadboard application (Iter 138.9)

---

## 🤝 HOW TO TALK TO JASON

- **Pacing:** Jason responds well to step-by-step walkthroughs. He often says "I need step by step" — take that literally. Send ONE micro-step, wait for confirmation, send the next. Do NOT batch 5 steps at once.
- **Screenshots:** Encourage them. He'll send screenshots whenever you ask. Use them to confirm exact UI state before guiding clicks.
- **Urgency:** He's running this solo, 9 days from authority grant. Help him close P0 user-side blockers first, code work second.
- **Honesty:** When something's broken or stale (e.g., a doc claims a feature is LIVE when it's actually dark), TELL HIM. He prefers honesty over polish. Iter 138.3 was triggered when the agent noticed `ops_state` lying.
- **No flattery:** Don't say "great question!" or "absolutely!" Just answer. Match his terse, technical style.
- **No emoji-floods.** A few targeted ones (✅/🟢/🔴/🚨) are fine. Don't dress every sentence with them.

---

## 🧠 LESSONS LEARNED (don't repeat these)

1. **Iter 134.x — login loop ate 3 forks.** Root cause was `seed_admin()` re-syncing `ADMIN_PASSWORD` env on every boot, overwriting whatever previous agents had updated in the DB. Fix: edit env, don't edit DB.
2. **Iter 138 — PIN tab disappearing was a UX bug, not a backend bug.** The frontend was hiding the PIN tab on any non-200 from `/auth/pin-status`. Decoupled UI from probe. Don't tie UI rendering to optional probes.
3. **Iter 138.3 — multiple `ops_state` lies were silent.** Three different gates were reading from MongoDB collections that DIDN'T EXIST (typoed names, never populated). Always verify the source-of-truth path actually has data.
4. **Iter 138.4 — FMCSA prevet vetting failed silently.** No webKey configured → every API call returned 404 → every vet failed. Always have a local fallback (we now use the 452K-row `fmcsa_carriers` census).
5. **Iter 138.5 — Twilio TFV "not resubmittable" via Update API.** Solution: delete + recreate via `tollfree_verifications.create()` with `tollfree_phone_number_sid` (NOT the phone string). Same SID won't work for re-creation if the prior verification still occupies the phone — must delete first.

---

## 📦 ITERATION 139 SUMMARY (2026-05-04 — single mega-day)

A productive day, 5 real wins, 2 blockers exposed.

### ✅ Wins shipped
| Iter | What | Outcome |
|---|---|---|
| **139.1** | Enhanced CHL logo, generated 7 sizes, force-download endpoint `/api/logo/download/{filename}` | Logo live on 123Loadboard Business Profile |
| **139.2** | DUNS application granted same-day (D&B fast-track) | DUNS **145008621** issued, vaulted, FACTS.md updated |
| **139.3** | Twilio TFV reviewer <REDACTED-THIRD-PARTY-REVIEWER> flagged opt-in non-compliance → built proper SMS consent block on `/#quote`, server-side audit trail at `db.sms_opt_ins`, broker-only `/api/sms-opt-ins` endpoint | Compliance gap closed |
| **139.4** | Reply email to <REDACTED-THIRD-PARTY-REVIEWER> sent with exact opt-in URL + 6-point checklist | Twilio re-evaluation triggered |
| **139.5** | Production redeploy verified (`main.0a15fb7a.js` carries the new bundle), Truckstop password rotated + vaulted | TFV recheck unblocked + Truckstop credentials safe |
| **139.6** | TFV deleted + recreated via Twilio API with corrected fields (contact phone +14172193856 fix; volume 1000→100/mo fix) | New SID `<REDACTED-TWILIO-TFV-SID>` PENDING_REVIEW |
| **139.7** | SOPPlaybookView Cards 5-14 narrative added (20 cards across 2 models). OPERATING_MANUAL.md re-synced (Chapter 3 RACI +8 rows, Chapter 5 cron table corrected, Chapter 7 summary corrected, Chapter 8 row 8.12 corrected, GAP totals 22→18.5 sessions). Full codebase audit produced at `/app/memory/CODEBASE_AUDIT_2026_05_04.md`. | Doctrine in sync with reality |
| **139.8** | Built `/admin/manual-coverage` dashboard + on-`/ops` widget. New module `backend/manual_coverage.py` parses OPERATING_MANUAL.md tables into ✅/⚠️/🚧 JSON. New views `ManualCoverageDashboard.jsx` (full page) + `ManualCoverageWidget.jsx` (compact tile). Live: 94 doctrine rows surfaced, 53 green / 16 yellow / 25 red. Endpoints `/api/admin/manual-coverage[/raw|/audit]`. Click-through detail modal exposes code refs + RACI + linked source markdown. | Doctrine becomes a living scoreboard |
| **139.9** | Built `backend/gap_close_watcher.py` — polls OPERATING_MANUAL.md every 5 min, sends Resend celebration email to `<REDACTED-EMAIL>` whenever any row flips toward green (red→yellow, red→green, yellow→green). Persists each event in `db.gap_close_events` + writes `agent_journal` row. Endpoints `POST /api/admin/gap-close/tick`, `GET /events`, `GET /snapshot`, `POST /reset-snapshot`. E2E smoke-tested: real email delivered. | Doctrine becomes a habit-forming gamified system |
| **139.10** | Added regression alarm — counterpart to celebration email. `_is_regression()` detects ✅→⚠️, ⚠️→🚧, ✅→🚧 transitions. `_format_regression_email()` renders distinct red-palette HTML with subject `⚠️ DOCTRINE REGRESSION — N row(s) went backwards`. Events tagged `direction: improvement\|regression`. E2E smoke-tested. | Catches quiet feature regressions automatically |
| **139.11** | Tier-2 GAP #1/5: **InvoicePreview** at `/broker-invoices` — lists all broker invoices + NOAs with inline PDF iframe preview, status filters, copy-invoice-# button. Reuses existing backend, no new code. | Closes Card 8 RACI UI gap |
| **139.12** | Tier-2 GAP #2/5: **CargoClaims** at `/cargo-claims` — open/resolved/all filter, resolve modal with note + amount, on-resolve lifts `invoice_send_hold_reason` on linked load. New endpoints `/api/failure-modes/cargo-claims/list\|resolve`. | Closes Mode 7.9 fully |
| **139.13** | Tier-2 GAP #3/5: **DetentionReview** at `/detention-review` — operator-confirm UI for the 64 real disputes already queued by the cron detector. Resolve stamps `detention_billed_amount` + `detention_dispute_resolved_at`. | Closes Mode 7.4 review surface |
| **139.14** | Tier-2 GAP #4/5: **FraudConsole** at `/fraud-console` — 3-outcome resolution (cleared / confirmed_fraud / block_carrier). `block_carrier` outcome marks `db.carriers.permanently_blocked=true` with reason. | Closes Mode 7.7 review + permanent-block field |
| **139.15** | Tier-2 GAP #5/5: **AgedAR** at `/aged-ar` — 4-bucket aged report (current / 31-60 / 61-90 / 91+) with stacked progress bar + drill-down list sorted oldest-first. New module `backend/aged_ar.py`. Real data: 3 unpaid invoices, $4,700 outstanding. | Closes Chapter 6 §6.3 visual gap |

### ⚠️ Blockers exposed today
1. **Truckstop account registered as carrier (not broker).** Previous fork agent (uncached) reportedly advised "sign up as carrier" on initial registration. URL trace `/product/load-board/carrier/` confirms. Re-registration with broker MC# triggered "you already have an account" wall. **Only fix: phone call to 1-800-203-2540** to convert tier. Login + password are vaulted as `Truckstop.com (Pro Load Board)`.
2. **Twilio Emergency Address registration failed** for (866) 490-6433. Address (618 W Greenwood) entered but E911 system rejected it. $75/call exposure if 911 ever dialed. Likely USPS-vs-Google address-format mismatch. Re-submit anytime — separate from TFV.

### 🟡 Twilio TFV final state at end of day
- Status: **"Toll-free verification in progress"** (auto-recheck queue)
- Reviewer (<REDACTED-THIRD-PARTY-REVIEWER>) explicit blessing: *"opt-in flow now looks aligned with the main TFV requirements... if it comes back rejected again, send me the exact rejection reason"*
- Production verified by agent: bundle hash `main.0a15fb7a.js`, all 4 testids present (`widget-sms-consent-block`, `widget-sms-consent-checkbox`, `widget-sms-terms-link`, `widget-tos-line`), all public pages 200
- One residual risk Isa called out: domain `continentalhaul.com` was previously flagged "high risk" — content/compliance is now strong but domain-level reputation may still cause a final rejection. Plan if rejected: paste exact rejection reason back to her for narrowing.

### 🔐 Vault state at end of day
| Service | Login | Password Stored | Notes |
|---|---|---|---|
| Dun & Bradstreet (DUNS) | dispatch@continentalhaul.com | DUNS = 145008621 | Iter 139.2 |
| Truckstop.com (Pro Load Board) | dispatch@continentalhaul.com | ✅ rotated 2026-05-04 | Iter 139.5 — role conversion pending phone call |
| (existing) FMCSA, IRS EIN, MO SoS, Login.gov, Hiya, Westfield, etc | unchanged | unchanged | — |

### 📂 New files created today
- `/app/frontend/public/logo-exports/chl-logo-{150,200,256,400,512,1024,2048}.{jpg,png}` (logo variants)
- `/app/memory/TFV_REPLY_TO_ISA_BELL.md` (compliance reply draft, sent)
- `/app/memory/CODEBASE_AUDIT_2026_05_04.md` (Iter 139.7 — 287-line audit vs OPERATING_MANUAL.md)
- `/app/backend/manual_coverage.py` (Iter 139.8 — manual parser + admin endpoints)
- `/app/backend/gap_close_watcher.py` (Iter 139.9 + 139.10 — 5-min poll cron + Resend celebration AND regression emails)
- `/app/backend/aged_ar.py` (Iter 139.15 — aged AR bucketing endpoints)
- `/app/frontend/src/views/ManualCoverageDashboard.jsx` (Iter 139.8 — `/admin/manual-coverage` page)
- `/app/frontend/src/views/ManualCoverageWidget.jsx` (Iter 139.8 — `/ops` live tile)
- `/app/frontend/src/views/InvoicePreview.jsx` (Iter 139.11 — `/broker-invoices`)
- `/app/frontend/src/views/CargoClaimsView.jsx` (Iter 139.12 — `/cargo-claims`)
- `/app/frontend/src/views/DetentionReviewView.jsx` (Iter 139.13 — `/detention-review`)
- `/app/frontend/src/views/FraudConsoleView.jsx` (Iter 139.14 — `/fraud-console`)
- `/app/frontend/src/views/AgedARView.jsx` (Iter 139.15 — `/aged-ar`)
- `db.sms_opt_ins` (TFV/A2P consent audit trail)
- `db.manual_coverage_snapshot` (Iter 139.9 — last-tick row hash)
- `db.gap_close_events` (Iter 139.9 + 139.10 — append-only ledger of doctrine improvements + regressions)
- `/api/logo/download/{filename}` (public force-download route)
- `/api/sms-opt-ins` (broker-only consent audit endpoint)
- `/api/admin/manual-coverage[/raw|/audit]` (Iter 139.8 — coverage endpoints)
- `/api/admin/gap-close/{tick|events|snapshot|reset-snapshot}` (Iter 139.9 — watcher control)
- `/api/admin/aged-ar/{buckets|list}` (Iter 139.15 — aged AR data)
- `/api/failure-modes/{cargo-claims|detention-disputes|double-broker-flags}/list|resolve` (Iter 139.12-139.14)

---



When you complete a notable iteration, **append to `/app/memory/SESSION_LOG.md`** (one row in the markdown table) AND **bump the changelog in `/app/memory/PRD.md`**. If you add a new compliance packet, file it under `/app/memory/`. Update this AGENT_HANDOFF.md only when:
- A new file is added to the critical file map
- A new "DO NOT DO" rule is established by Jason
- A capability category lights up (LIVE → IN-FLIGHT → DARK transition)
- Day-1 gate passes/fails

Keep the elevator pitch, mission, and protocol section pinned at top. They never change.

---

**End of AGENT_HANDOFF.md.**

If you've read this from top to bottom, you're ready. Output your FACT_CONFIRMATION + SESSION_CONTINUITY blocks per FACTS.md and start the conversation.
