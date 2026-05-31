# CHL Platform — Where We Are vs Where We're Going

**Generated**: 2026-05-30 (late session, post-shipper_master build)
**Purpose**: Single readable artifact for operator + future Claude sessions to understand the full platform shape in one place — what works, what's missing, what's the ultimate vision, what reaching it requires.

---

## 🎯 THE ULTIMATE VISION

Continental Haul Logistics is **an autonomous freight brokerage SaaS, run solo by Jason Meyer with Claude as engineering partner. Self-healing. Self-correcting. AI-embedded for monitor-and-write-code while running.**

Three goalposts:
- **$200K floor (Year 1)** — revenue that lets Jason leave the Amazon warehouse
- **$5-20M (5-year milestone)** — established regional ag-focused freight broker
- **Every freight load in the country (eventual vision)** — operator-stated 2026-05-24, NOT a ceiling

The substrate the operator wants:
- Score new work on 3 axes: *removes operator-touch* / *adds observability* / *substrate for future self-healing*
- Run indefinitely if Claude shuts down for 30 days
- Build for next 90 days, architect for the 5-year trajectory

---

## ✅ WHAT THE PLATFORM DOES TODAY

### Outbound sales pipeline (cold → warm → booked)
- Overnight cold-email drafter using Ollama with per-shipper personalization
- Outreach Monk (3/7/14-day followup sequencer)
- LinkedIn People bookmarklet for alternative-contact discovery
- Per-shipper call-script generator
- Top-50 printable call sheet + Wednesday call list w/ brief
- Email blocklist + STOP detection + 30-day undo
- **NEW (tonight)**: Top-50 Thursday Targets shortlist generator
- **NEW (tonight)**: Unified Thursday Call Sheet across all sources (13,810 dial-ready records)

### Inbound capture
- Zoho IMAP poller w/ Tier 1/Tier 2 email classification
- **NEW (tonight)**: Sent-folder ingestion — captures operator's manual replies into the same pipeline
- Shipper reply matcher + 11-bucket classifier (incl. vendor_pitch)
- AI Voice Handler (Telnyx + Whisper + Ollama answers calls)
- Web visitor tracker + enricher
- Inbound voice calls view

### Pricing + rate engine
- Rate calc engine + guards
- Cold-lane rate oracle
- Grain rate calculator + EIA diesel oracle
- Hopper pricing
- PnL monitor with never-book-at-a-loss floor
- Auto-quote drafter
- Lane economics heuristic

### Carrier operations
- FMCSA carrier DB (486K records)
- Carrier domain guesser + email enricher + reply classifier
- Carrier-shipper proximity matcher
- Dutch-auction load dispatch
- Carrier vetting
- Carrier outreach drafter

### Shipper data substrate
- 752K shipper_targets across multiple sources
- **NEW (tonight)**: 633,327 canonical `shipper_master` records — unified per-shipper view joining ALL touches (calls + emails + vault docs)
- ImportYeti substrate (730K records) + bookmarklets + Playwright walker + aggregate parser
- **NEW (tonight)**: Bucket-3 substrate ingested from federal/state registries (~22K NEW records)
  - 191 EIA Ethanol plants + phones + emails
  - 7,176 USDA FSIS meat/poultry plants + 6,911 phones
  - 12,282 MSHA active mines + 6,523 phones + 5,323 emails
  - 4,314 EPA FRS ag facilities (phones pending June 1)
  - 572 SEC EDGAR Exhibit 21 subsidiaries (phones pending June 1)
- Multi-source shipper deduper (1,471 clusters)
- Phone validator + contamination quarantine + geocoder + legitimacy investigator
- **NEW (tonight)**: 512K records with inferred emails via deterministic_v1 (+220K added tonight)

### Operator workflow surfaces
- Next-action daemon ("what to do RIGHT NOW")
- Email drafts review UI (J/K/Y/N/E shortcuts)
- Shipper targets admin view
- **NEW (tonight)**: shipper_master search + per-shipper expand-all view at `http://127.0.0.1:8765/shipper/search` + `/shipper/<id>`
- Inbound replies view with bucket chips
- Morning briefing aggregator (Desktop, daily 05:00 CDT)
- Call binder (HTML w/ outcome buttons + offline retry queue)
- **NEW (tonight)**: `session_boot_brief.py` — generates last-48h activity brief at session boot
- **NEW (tonight)**: WIP/ thread files in chl-memory for in-flight context

### Financial + compliance
- PnL monitor + factor matrix + Smart Freight Funding integration
- FMCSA SAFER lookup
- Email blocklist + STOP detection
- Strategic-privacy posture for partner calls
- Vault for credentials + documents (Fernet-encrypted, audit-logged)

### Self-awareness layer
- `reference-code-inventory` (auto-generated weekly)
- `reference-desired-functionality-manifest` (this living spec)
- `PLATFORM_BIOS.md` — 1,356-file module index
- Platform Steward (15-min meta-aware reasoning loop)
- BOOT_BIOS.md + BOOT_SCHEMATIC.md (5-min refresh)
- **NEW (tonight)**: Boot brief queries DB directly (closes saved≠remembered gap)
- **NEW (tonight)**: Forward-write hooks auto-link new touches to shipper_master

### Infrastructure
- MongoDB on N:\mongodb-data\ (NVMe-CHL drive)
- Local Ollama (24 models incl llama3.1:70b)
- Backblaze B2 conversation backups (mirror to N:\CHL\backups\)
- D-drive 5-copy floor backups
- Cloudflared tunnel for backend exposure
- 5 NSSM services: CHL-Backend / CHL-Frontend / CHL-BackgroundLoops / CHL-FleetSupervisor / Cloudflared

---

## ❌ WHAT THE PLATFORM DOES NOT DO

### Operations (after the sale) — biggest gap
- **Customer onboarding pipeline** — broker-carrier agreement, W-9, COI, credit terms auto-generated for first booked customer. Not built. **CRITICAL for first booking.**
- **load_brain.py** — 24-state per-load state machine with AI watcher. Spec approved 2026-05-20. Not built.
- Auto-RC + BOL + POD-chase + invoice + settlement chain (pre-launch backlog)
- Shipper self-service portal
- Recurring revenue auto-billing trigger (recurring_revenue_watch exists; action not wired)

### Outbound — refinements
- Voicemail script generator (per-shipper 20-sec scripted with BOL hook)
- Pre-call objection drill trainer (LLM-graded practice)
- 5-touch email sequence (intro → case study → question → close-file) — partial via Outreach Monk

### Inbound — refinements
- Voicemail-to-CRM pipeline (outbound voicemail recording + Whisper + auto-log)
- Inbound SMS reply classifier (currently inbound SMS goes to operator phone)

### Pricing — UI gap
- Live-quote surface in call binder (operator types lane → instant quote on screen). Engine exists; UI doesn't.
- DAT RateView API integration (blocked on paid upgrade post-revenue)
- A/B pricing experiment tracker

### Carrier — broadcast layer
- Carrier broadcast on shipper booking (instant load offer to 50 nearest qualified). Not built.
- Carrier self-onboarding portal (web form)
- Carrier reverse-edge data (scrape carrier websites for shipper-name mentions) — partial

### Shipper data — finishing touches
- Facility-phones parser fix (yeti_unlocked_facilities truncation bug)
- Cross-source consistency checker (e.g., Yeti says Houston, BOL says NYC → flag)
- **Andersons emails currently mislinked** to Toledo master vs operator-curated Kearney/Kansas/Dunkirk — WIP_andersons_master_relink.md captures the fix

### Financial — accounting
- Tax-deductible expense tracker (Schedule C / 1120-S, auto-pulled from inbox)
- Customer profitability report (per-shipper LTV minus operator-hours-spent)
- Wife-as-bookkeeper integration (auto-feed weekly P&L via accounting@continentalhaul.com)

### Workflow surfaces — visualizations
- Sales pipeline visualization (Sankey/funnel)
- Feedback-learning loop (operator skips/edits → system rules tighten) — codified discipline, not built

### Compliance + risk
- Per-shipper NDA / data-handling tier
- Audit log for vault access (partial via vault_audit)

### Self-awareness — final polish
- Desired-minus-actual gap report (auto-diff manifest vs PLATFORM_BIOS)
- Boot-time loader that reads ALL files into Claude's context (partial)
- **Forward-write integration for outbound_email senders** — periodic linker catches it but no real-time write-hook. The 20 `.send_*.py` scripts each insert directly to outbound_messages without a central chokepoint.

### Self-correction — operator-feedback loop
- Operator-feedback loop (skip/done/edit signals retrain source rules) — codified, not built

---

## 🛣️ WHAT THE ULTIMATE VISION REQUIRES

### Tier 1 — Required for first booked load (NEXT 30 DAYS)
1. **Insurance binder secured** — Stefan/Tru reply pending; backup brokers (Roanoke / Beacon Hill / Western World) drafts ready. THIS IS THE CHOKEPOINT.
2. **Customer onboarding pipeline** — when COI lands, the 4 committed Andersons + Bunge prospects need automated packet generation (W-9, MC Agreement countersignature workflow, COI distribution).
3. **load_brain.py** — every booked load needs a state machine to track from tender → at-shipper → loaded → at-consignee → delivered → POD-received → invoiced → factor-paid.
4. **First booked load workflow** — RC generation, dispatch carrier broadcast, BOL handling, POD chase.

### Tier 2 — Required for 10 booked loads/month (NEXT 90 DAYS)
1. **Forward-write integration for outbound senders** — close the master_id-on-send gap (currently periodic linker catches 5-min later).
2. **Carrier broadcast on shipper booking** — instant offer to 50 nearest qualified carriers when a load tenders.
3. **Auto-quote in call binder** — operator types lane mid-call, sees instant quote. Engine exists; needs UI surface.
4. **Email-factor adapter pattern** built out for SFF (currently manual to Billing@/Megan@).
5. **Recurring revenue auto-billing trigger** wired.
6. **Andersons-master-relink + improved fuzzy resolver** — handle multi-facility shippers cleanly.
7. **Voicemail-to-CRM pipeline** — operator can't always pick up; voicemails need to flow into the same record.

### Tier 3 — Required for $200K/year (NEXT 12 MONTHS)
1. **DAT RateView API** (paid; ~$300/mo) — replaces lane-economics heuristic with market truth.
2. **Customer profitability report** — operator knows which shippers are worth the time.
3. **Tax-deductible expense tracker** — Schedule C automation, IRS-defensible.
4. **Wife-as-bookkeeper (Pugee) integration** — accounting@continentalhaul.com pipeline.
5. **Operator-feedback loop active** — every skip/edit feeds nightly learning cron.
6. **Per-shipper unified view enhanced** — beyond shipper_master's current join, add SLA tracking + on-time-payment scoring + dispute history.
7. **Sales pipeline visualization** — Sankey/funnel surfaces the conversion shape.
8. **Cloud replica** active (Hetzner CX22 + B2 nightly mongodump + CloudFlare).

### Tier 4 — Required for $5-20M / 5-year milestone
1. **Multi-broker model** — CHL becomes a brokerage that ALSO does autonomous dispatch for other small brokers. Same platform, multi-tenant.
2. **Driver-side mobile** — PWA + native app for the carrier-facing flow. Driver preference triad live + tracking + comms.
3. **Real ML pricing** — replace heuristic + DAT RateView with trained models on CHL's own historical loads.
4. **Carrier self-onboarding** — automated FMCSA verification + insurance pull + COI binder check + agreement signing.
5. **Shipper self-service** — login + view available capacity + post loads + see quotes.
6. **Cloud-only operations** — no more single-Brightspeed-ISP SPOF. Multi-region replicas. CDN-fronted.
7. **Compliance + audit** — SOC2 Type II ready. NDA tiering. PII handling. GDPR-equivalent.

### Tier 5 — Required for "every freight load in the country"
1. **OR-Tools VRP at scale** — vehicle routing optimization across the network.
2. **Real-time market-making layer** — observes ALL load boards + all available capacity + auctions matches.
3. **Shadow-mode autonomous routing** validated at 85%+ agreement → promoted to autonomous (per [[project-shadow-mode-routing-autonomy]]).
4. **Network effects** — carriers prefer CHL because we offer the best matches; shippers prefer CHL because we have the most carriers.
5. **Fleet of AI agents** — beyond Claude/Ollama, dedicated models for matching, fraud detection, route optimization, customer service.
6. **Distributed infrastructure** — multi-cloud, multi-region. The platform IS the moat.

---

## 📊 GAP ANALYSIS

| Status | Count | What it means |
|---|---|---|
| ✅ Built | ~75 features | Per the manifest's `[x]` items + tonight's adds |
| ⏳ Partial | ~15 features | Engine exists, UI gap; OR codified but not coded |
| ❌ Not built (Tier 1) | ~4 critical | Insurance binder, onboarding pipeline, load_brain, first-load workflow |
| ❌ Not built (Tier 2-5) | ~50+ | Pipeline for 10+ loads, $200K, $5-20M, every-load vision |

**The honest read**: CHL is **substrate-rich, operationally-thin**. Most of what's built supports the path TO the first booked load. The path AFTER the first booked load (load_brain, RC generation, dispatch broadcast, settlement chain) is mostly not yet built. This is fine because building forward of "no booked loads yet" is speculative; volume forces refinement.

**Tonight's contribution** specifically: closed the substrate gap on Bucket-3 cohort (~22K new shippers) + unified per-shipper records (633K masters) + memory-discipline scaffolding (boot brief, WIP files, forward-write hooks). The platform now SEES itself better than 8 hours ago.

**What remains chokepointing**: insurance binder. Without it, the 4 committed prospects can't transact, and none of the Tier 2+ items matter yet.

---

## 🔄 HOW TO UPDATE THIS DOC

This is a synthesis snapshot. Source of truth lives in:
- [[reference-desired-functionality-manifest]] — line-by-line spec, A-L categorized
- `PLATFORM_BIOS.md` — auto-generated module index
- `chl-memory/operations/MASTER_BACKLOG_2026_05_11.md` — operator-prioritized backlog
- `chl-memory/wip/` — in-flight thread state files

When this snapshot diverges from those, the manifest wins. Regenerate this doc when operator asks for "where are we" or when a major capability ships.

Related: [[project-platform-north-star]], [[project-5-year-target-regional-ag-freight-broker]], [[reference-master-backlog]], [[feedback-saved-not-remembered-fix]]
