# Freight Broker Workflow — Research Synthesis for Tomorrow's Discussion

**Date prepared:** 2026-05-10 (overnight research)
**Purpose:** Pre-read for operator + dev-engineer Claude workflow-alignment discussion tomorrow AM. Compares 4 source categories. Validates operator's Step 1 and Step 2 instincts. Surfaces decision points.

**Source documents (read each for full detail):**
- `01_fmcsa_regulatory_workflow.md` — 49 CFR + FMCSA-published baseline
- `02_industry_broker_workflow.md` — TIA / DAT / Truckstop / FreightWaves / SONAR / broker schools
- `03_factoring_credit_workflow.md` — Denim / Apex / RTS / OTR / TriumphPay / Ansonia
- `04_carrier_vetting_workflow.md` — Highway / MCP / RMIS / Carrier411 / FMCSA SAFER

---

## TL;DR — answers to operator's specific questions

### Q1: "We first find a load right?"

**Partially right — but it depends on your business model.** Industry is split on what "step 1" actually is:

| Model | Step 1 | Source bias |
|---|---|---|
| **Load-board-first** | Find a load on DAT / Truckstop / 123 / SAM.gov | DAT, Truckstop (vendor bias toward selling load-board subscriptions) |
| **Shipper-book-first** | Sign 5-10 shippers, get inbound RFQs | SONAR, FreightWaves, broker training schools (TIA-leaning) |
| **Carrier-relationship-first** | Build a curated carrier list, then prospect shippers | DAT 2025 pivot, Highway-era thinking |

**Reality for a NEW broker (you):** load-board-first is the fastest path to first revenue because shipper relationships take 90+ days to build and the load board has loads available right now. **DAT's bias is real but their conclusion is also right for your stage.**

**However**, "find a load" is itself a multi-step:
1. SEE the load (board API or scrape)
2. EVALUATE the load (margin / lane / equipment / shipper credit / regulatory fit)
3. DECIDE whether to bid
4. POST a rate to the shipper / book it
5. MOVE to next step

Saying "step 1 = find a load" is correct shorthand for steps 1-4 of the real workflow.

### Q2: "Then we send it to a factoring company for approval of credit for the carrier"

**Partially right — but you're conflating three different "factoring" concepts.** This is the most important clarification for tomorrow:

| What you said | What you may mean | When it really happens |
|---|---|---|
| "Send to factoring for approval of credit for the carrier" | Three possible interpretations follow ↓ | |
| (a) Check the SHIPPER's credit before booking | **Yes — this is real and is Step 2 in most workflows.** Your factor's credit team approves/denies the receivable + sets a credit limit. | Pre-booking (before locking in carrier) |
| (b) Check the CARRIER's credit before dispatching | **Not really — this is BACKWARDS.** It's the CARRIER's factor that checks YOUR (CHL's) credit when the carrier accepts the load. Not your factor checking the carrier. | At carrier acceptance — and you don't drive it |
| (c) Submit the invoice to factor after delivery | **This is also "factoring" — but it's Step ~9, not Step 2.** Post-POD, you sell the receivable to your factor for cash. | After POD delivery |

**So your Step 2 is RIGHT in spirit but needs reframing.** Step 2 is: "Pre-clear the SHIPPER's credit with your factor — confirm they'll buy this receivable." NOT "submit invoice" and NOT "check carrier credit."

### Q3: "Where do we determine if there is margin enough for us? In what part of the process?"

**Margin gate fires BEFORE factor engagement, not after. CHL already has this:**

- `backend/pnl_monitor.compute_load_pnl` — your absolute loss-floor
- `backend/rate_calc_engine.calculate_with_config` — your pricing pipeline
- Aggregate RED state auto-blocks new quotes if 24h net < -1%
- **None of this requires factor approval to fire** — margin is determined from your own cost model + the carrier rate you can secure

**Industry sources don't publish a hard loss-floor like CHL has** — this is actually a CHL competitive advantage. Your margin gate is more rigorous than what industry guides describe.

**Order of margin / factor decisions:**
1. Quote-time margin check (`pnl_monitor` — pre-quote)
2. Carrier-rate-vs-quote margin check (post-acceptance)
3. Factor credit-decision on the shipper (parallel to carrier sourcing — is this shipper factorable?)
4. Both must clear before booking

### Q4: "How will we vet the carriers, and where to put that information?"

**Two-stage vetting is non-negotiable** (per Q1 2026 fraud data):

| Stage | When | What | Where in CHL |
|---|---|---|---|
| **Onboarding-time** | Once when carrier first interacts | Authority age >180d / SMS BASIC <65th percentile / BMC-91 / W-9 / signed MCSA / COI / driver list | `backend/carrier_vetting_workflow.py` (already exists) → writes to `db.carriers` |
| **Per-load** | At every load assignment | Authority still active / OOS check / change-of-ownership scan / cargo insurance current / SMS scores fresh | `vet_for_load(mc, load)` gate (to build — see `04_carrier_vetting_workflow.md`) |

**Data destinations:**
- `db.carriers.*` — main metadata (already populated for 484,700 carriers from Census)
- `db.carriers.sms.*` — FMCSA SMS BASIC scores (cron exists, gated off by default)
- `db.carrier_vet_events` — append-only audit (to build)
- `db.carrier_documents` — refs to Vault-stored files (Vault already exists; can extend pattern)
- `db.vault_files` — actual COI / W-9 / MCSA PDFs (Vault Documents shipped today)

Schema already drafted in `04_carrier_vetting_workflow.md`.

---

## The proposed canonical workflow (DRAFT — for tomorrow's review)

Based on cross-source triangulation. Numbered for discussion. **All of this is open for your edits tomorrow.**

### Phase 0 — Setup (one-time, BEFORE first load)

| Step | What | Status for CHL |
|---|---|---|
| 0.1 | FMCSA broker authority application (Form OP-1) | Pending — ~May 13 |
| 0.2 | BMC-84 surety bond filed ($75K) | ✅ Done (May 4) |
| 0.3 | BOC-3 process agents designated | ✅ Done (May 4) |
| 0.4 | UCR registration | ⚠️ Verify per renewals tab |
| 0.5 | EIN + business bank account (Mercury) | ✅ Done (May 4) |
| 0.6 | Cargo / general liability / E&O insurance | ⚠️ Verify policies + filings |
| 0.7 | Factor relationship established (operator's factor company picked) | ⚠️ Pending — operator action |
| 0.8 | Shipper / customer book (or load-board credentials) | ⚠️ Pending — load-board API access |

### Phase 1 — Find / Source a Load

| Step | What | CHL surface |
|---|---|---|
| 1.1 | See available loads | **Load Board** tab (external) or **Find Loads** (Aggregator) or shipper RFQ inbox |
| 1.2 | Evaluate lane: origin/dest, equipment fit, deadhead, miles | Per-load detail view |
| 1.3 | Run dry-run quote | **Ops Console v2 → Dry-Run Quote** or Rate Calculator |
| 1.4 | Check margin: does `pnl_monitor` say GREEN? | Auto-fired by rate_calc_engine |
| 1.5 | Check shipper credit: does our factor approve this shipper? | **TODO — not currently in CHL.** See Phase 1.X below |

### Phase 2 — Shipper Credit Check (operator's "Step 2")

| Step | What | CHL surface |
|---|---|---|
| 2.1 | Submit shipper to factor's credit team (or Ansonia / Compass automated query) | **TODO — needs operator's factor company selection + API integration** |
| 2.2 | Receive approval + credit limit OR denial | TBD |
| 2.3 | If denied: pass on the load (don't book). If approved: proceed | Operator decision |

### Phase 3 — Carrier Sourcing & Vetting

| Step | What | CHL surface |
|---|---|---|
| 3.1 | Search vetted carriers in `db.carriers` | **Carriers DB** tab |
| 3.2 | Cold outreach to new carriers via SMS magic-link | **Load Detail Modal → Send SMS Blast** (per audit) |
| 3.3 | Carrier responds; per-load vetting fires: authority / SMS / insurance / OOS scan | `vet_for_load()` — to be built per `04_*.md` |
| 3.4 | Carrier signs MCSA (one-time onboarding doc); rate confirmation generated | Existing rate_con.py |
| 3.5 | Load locked: status transitions to `booked`, assigned_carrier_id set | Existing flow |

### Phase 4 — Dispatch & Track

| Step | What | CHL surface |
|---|---|---|
| 4.1 | Carrier picks up, GPS pings flow to `db.gps_pings` | PWA + carrier-side magic link |
| 4.2 | Load transitions to `in_transit` | Auto on first GPS in-transit ping |
| 4.3 | Operator monitors via **Live Map** + ETA-slip detector | Existing |

### Phase 5 — Delivery

| Step | What | CHL surface |
|---|---|---|
| 5.1 | Carrier delivers, uploads POD via carrier-side magic link | Existing |
| 5.2 | Load transitions to `delivered` | Existing |
| 5.3 | If `delivered_autofire_enabled=true` (currently OFF by default): auto-fire invoice + NOA | Operator opt-in |
| 5.4 | If autofire off: operator manually fires from **Ops Console v2** | Existing |

### Phase 6 — Invoice & Factor

| Step | What | CHL surface |
|---|---|---|
| 6.1 | Generate broker invoice (post-POD) | Existing |
| 6.2 | Generate Notice of Assignment (NOA) — tells shipper to pay factor not broker | Existing |
| 6.3 | Submit invoice + POD + rate-con + NOA to factor | **TODO — needs factor's API or email template** |
| 6.4 | Factor advances cash (typically 90-97% of invoice, less factor fee) | Operator-side cash flow |
| 6.5 | Shipper pays factor 30/60/90 days later (per net terms) | Outside CHL |

### Phase 7 — Carrier Payout

| Step | What | CHL surface |
|---|---|---|
| 7.1 | Wait for factor's cash advance to arrive in Mercury | Operator-side |
| 7.2 | Pay the carrier (less broker margin) via Mercury ACH | **Accounting tab — currently view-only; no manual pay button** (audit finding) |
| 7.3 | If carrier is on TriumphPay network: payment routes through TriumphPay | TriumphPay integration pending |
| 7.4 | Carrier's factor receives the payment (if carrier factors) | Outside CHL |

### Phase 8 — Close & Audit

| Step | What | CHL surface |
|---|---|---|
| 8.1 | All documents linked to BOL via `documents/by_bol/{bol}` query | **BOL Search** tab (shipped today) |
| 8.2 | §371.3 record retained for 3 years (statutory) | DB retention — verify cron |
| 8.3 | Audit trail in `db.api_audit_log` + `db.document_search_audit` | Existing |

---

## Critical disagreements / open questions for tomorrow

1. **What's our entry model?** Load-board-first (fastest revenue) vs shipper-book-first (better margins, slower start) vs carrier-relationship-first (modern + safest)? You can mix, but the dominant pattern shapes the platform.

2. **Which factor company are we using?** Affects Step 2 (credit check) integration. Common picks: Denim, OTR, Triumph Business Capital, eCapital. Each has different API + workflow. **This needs to be picked before Step 2 can be built.**

3. **Pre-clear shipper credit — automated or manual?** If automated, we need factor API integration. If manual, we add a "Submit to Factor" button + the operator emails/calls the factor manually.

4. **TriumphPay opt-in?** TriumphPay is the dominant broker-to-carrier payment network. Bypassing it = friction with TriumphPay-onboarded carriers. Joining = standardized payments but TriumphPay fees + Triumph relationship.

5. **Per-load vetting depth?** Industry consensus: minimum re-check of authority + insurance every load. Maximum re-check: full revet every load. CHL has the infrastructure for both — what's our default?

6. **Margin floor in real $?** `pnl_monitor` uses formulas; you should pick a specific floor (e.g., "$300 minimum gross per load" or "10% minimum margin"). Industry doesn't publish a number; this is operator judgment.

7. **Cradle-to-grave model vs split-team model?** Cradle-to-grave = one operator handles load from quote to settlement. Split-team = sales / dispatch / payments handed off. You're solo, so cradle-to-grave is the only option short-term, but the platform supports both.

---

## What the platform has vs needs

| Feature | Status | Gap |
|---|---|---|
| FMCSA authority verification | ✅ Live (Census + QCMobile + SMS BASIC scores) | — |
| Carrier onboarding workflow | ✅ Mostly built | Schema cleanup per `04_*.md` |
| Per-load vetting gate | ⚠️ Partial (vetting_checks.py exists) | Wire `vet_for_load()` into dispatch path |
| Load board observation | ⚠️ Adapters scaffolded for 18 sources | Need API credentials per source |
| Rate calc + P&L guard | ✅ Live + verified today | — |
| Quote → load creation | ✅ Live | — |
| Shipper credit check (factor pre-clear) | ❌ **NOT BUILT** | Step 2 from operator's question. Needs factor selection + API |
| Carrier sourcing + cold outreach | ✅ Live | — |
| Rate confirmation generation | ✅ Live | — |
| Dispatch + GPS tracking | ✅ Live | — |
| Delivery + POD | ✅ Live | — |
| Invoice + NOA generation | ✅ Live | — |
| Submit to factor (post-POD) | ❌ **NOT BUILT** | Email template or factor API integration |
| Carrier payout | ⚠️ Auto-pay disabled (Stripe cleanup); Mercury manual | "Pay Carrier" button on Accounting tab (audit finding) |
| BOL document search | ✅ Live (shipped today) | — |
| Operator queue review | ✅ Live (Queues tab) | Acknowledge button on Alerts panel (audit finding) |
| 49 CFR §371.3 record retention | ⚠️ Records exist; 3-year retention cron not verified | Verify retention guarantee |

---

## My recommended discussion agenda for tomorrow

1. **Confirm the workflow phase list** (Phase 0-8 above) — what's wrong / missing / out-of-order?
2. **Resolve the 7 open questions** above (entry model / factor company / vetting depth / margin floor / etc.)
3. **Pick the top 3 gaps to close** (shipper credit check / pay-carrier button / acknowledge button / per-load vetting gate / 3-year retention cron — pick 3)
4. **Decide: fix the duplicate tabs first** (Ops Console v2 + BOL Search) or build the missing gaps first
5. **Decide:** rewrite the operator SOP after workflow alignment, OR phase-by-phase as each gap closes

**End of synthesis. Ready for tomorrow's discussion.**
