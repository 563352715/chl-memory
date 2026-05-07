# Iter 142.1 Full Agenda — Phase 3 Carrier Network (Outreach + Assignment + Dispatch)

> **Iteration goal:** Build multi-carrier outreach, assignment, and dispatch automation. Operator (or auto-bid from iter 141.2) books load → system selects optimal carrier → dispatch packet auto-generated and delivered.
> **Timeline:** ~3 weeks dev (sequential 1a→1f, ~800 LOC total) + FMCSA-blocked go-live windows (see "FMCSA Blockers" section).
> **Phase boundary (from `CHL_STRATEGIC_PLAYBOOK.md`):** Phase 3 milestone = "System can book carrier and dispatch without operator." Out-of-scope: GPS tracking (Phase 4), POD/invoicing (Phase 5), factoring (Phase 6).
> **Source sketch:** `chl-memory/research/iter_142_1_phase_3_sketch.md` (4 stages, here expanded to 6 by splitting Carrier DB into schema + ingestion + scoring).

---

## ⚡ AMENDMENT 2026-05-07 — Load-Board-First Pivot (Option A)

**Operator decision 2026-05-07:** CHL has zero pre-vetted carriers. Original stage 1b assumed operator already had ~20 trusted carriers ready for CSV seed batch. That assumption is incorrect. Pivot to **load-board-first acquisition model** (operator-confirmed Option A from pre-flight diagnostic conversation):

**What changes:**
- **Stage 1b:** REPLACED CSV seed batch with FMCSA SAFER **on-demand ingestion**. Operator types MC number (from a load-board responder), system auto-pulls authority + insurance + SMS scores, presents vetting summary to operator. Operator signs off → carrier added to roster after BCA + COI received. Roster grows organically as load flow generates contacts.
- **Stage 1d:** ADDED load-board posting fallback. When `select_top_n_for_lane` returns empty (roster has no qualifying carrier for the load's lane), system auto-posts to DAT/Truckstop, captures carrier responders, routes responders into the on-demand vetting flow before booking. Roster size grows asymmetrically with load flow.
- **Stage 1c, 1e, 1f:** unchanged conceptually; just operate on smaller initial roster (1-5 carriers vs 20).
- **Pre-flight prerequisites:** REMOVED "20-carrier seed batch CSV" (~30 min operator time). REPLACED with "1-2 hand-vetted carriers if any exist; otherwise system seeds from first load-board responses." Operator hands-on across iter compresses from 70 min → ~40 min.

**Vetting workflow** (operator-facing step-by-step) is captured in `chl-memory/research/iter_142_1_carrier_vetting_checklist.md` (~30 min - 2 hours per carrier depending on responsiveness; required before legal dispatch per FMCSA regs).

**Cross-references:**
- `iter_142_1_pre_flight_brief.md` — pre-kickoff gotchas (commit `d567596`)
- `iter_142_1_carrier_vetting_checklist.md` — operator vetting playbook (this amendment)
- Operator decision context: chat 2026-05-07 — "I have not vetted a single carrier... Perform A and put together a vetting checklist"

**Open Questions Resolved (updated):**
- Q1 (Paid carrier data sources) — UNCHANGED: FMCSA SAFER only at v1; defer paid services until post-revenue
- Q2 (Self-serve carrier registration) — UNCHANGED: broker-controlled vetting only
- Q3 (Rate negotiation) — UNCHANGED: fixed offer at v1
- Q4 (Carrier blacklist scope) — UNCHANGED: global blacklist only
- **NEW Q5 (Load-board posting integration):** DAT or Truckstop or both at v1? Default: DAT only (industry-largest reach, pre-existing CHL integration in iter 141.2 stage 1d). Operator overrides if Truckstop relationships exist.
- **NEW Q6 (BCA template):** Operator-authored or attorney-drafted or industry-standard template? Default: industry-standard (e.g., TIA-published template at ~$0). Attorney review optional but not blocking. Operator confirms pre-stage-1b.

**STATUS UPDATE 2026-05-07 EOD:** Stages 1b and 1d body text below has been **PROPAGATED IN-PLACE** to reflect this amendment (commit pending). Stage 1b now shows on-demand vetting workflow + state machine; stage 1d now shows load-board-posting fallback path with smoke #8 added. This top-of-doc amendment block kept as the canonical context-and-rationale; stage bodies are the executable spec. Stages 1a, 1c, 1e, 1f unchanged. Aggregate smoke-test count: **34** (was 33 — stage 1d gained +1 for load-board fallback). Operator hands-on: **~40 min** core + per-carrier vetting (~15 min hands-on per carrier, async wait for BCA/COI per `iter_142_1_carrier_vetting_checklist.md`).

---

## Pre-Flight (Operator, before stage 1a)

**Prerequisites:**
- [ ] Iter 141.2 shipped (DAT live wiring + auto-bid backend live OR explicitly deferred — auto-bid handoff is 1f integration target)
- [ ] Iter 141.3 shipped OR pinned (SMS provider determines stage 1d outreach module — Twilio if 141.3 deferred, Plivo if completed). Stage 1d adapts to whichever is active.
- [ ] FMCSA authority MC-1817555 finalized (~2026-05-13+) — gates stage 1b real-carrier ingestion and stage 1f end-to-end smoke
- [ ] Operator decisions locked (all four "Open Questions" from sketch — see "Open Questions Resolved" section)
- [ ] FMCSA SAFER API key provisioned (free, no rate-limit concerns at v1 volume)

**Estimated total operator hands-on time across iter:** ~70 min (spread across stages, not consecutive)

---

## Open Questions Resolved (operator must decide before 1a opens)

These four sketch-level open questions block 1a kickoff. Stages assume the noted defaults; operator overrides via reply.

1. **Paid carrier data sources** → **Default: FMCSA SAFER only at v1.** Highway/RMIS/CarrierAssure deferred to Phase 6 (factoring) or later — adds ~$200–500/mo at v1 scale, exceeds the $20/wk solo-pre-revenue threshold.
2. **Self-serve carrier registration portal** → **Default: broker-controlled vetting only at v1.** Self-serve = additional 10DLC + opt-in compliance surface. Defer to post-revenue.
3. **Rate negotiation logic** → **Default: fixed offer at v1, no counter-offer rounds.** Counter-offer = additional state machine + per-carrier negotiation history. Track rejections, learn for v2.
4. **Carrier blacklist/whitelist per shipper requirements** → **Default: global blacklist only at v1** (carrier MC excluded from all outreach). Per-shipper whitelist/blacklist deferred until first shipper actually requests it.

---

## Stage 1a: Carrier Database Schema + CRUD Endpoints

**Goal:** Stand up `db.carriers` collection with documented shape + indexes + read/write endpoints. No data ingestion yet — schema-only foundation.

**What operator does (~5 min):**
1. Review schema doc (`memory/handoff_iter_142_1_stage_1a.md`) — confirm fields match operational needs.
2. Sign-off comment on `current_status.md`.

**What Dev Engineer does (~3h):**
1. Create `db.carriers` Mongo collection per sketch shape: `mc_number, legal_name, lanes_served, reliability_score, insurance_expiry, contact_phone, contact_email, sms_opt_in, last_haul_date, blacklisted (bool, default false)`.
2. Add indexes: unique on `mc_number`, sparse on `lanes_served`, compound on `(blacklisted, reliability_score desc)` for outreach selection.
3. Endpoints: `GET /api/carriers/{mc_number}`, `POST /api/carriers` (create), `PATCH /api/carriers/{mc_number}` (partial update), `GET /api/carriers?lane=X` (list-by-lane).
4. Pydantic models in `backend/models/carrier.py`.
5. Wire into existing health-check framework (`/api/health/system` reports `carriers` module).

**Code dependencies:**
- Existing FastAPI infrastructure (no new framework deps)
- Mongo driver already in use
- No external API calls in this stage

**Smoke battery (4 tests):**
1. Schema validation (valid carrier doc inserts; invalid mc_number format rejects 422)
2. Indexes created (compound index visible in `db.carriers.getIndexes()`)
3. CRUD endpoints round-trip (POST → GET → PATCH → GET)
4. Health check reports `carriers` module green

**Success criteria:**
- ✅ All 4 smoke tests PASS
- ✅ Schema doc operator-approved
- ✅ Health endpoint returns `carriers: green`

**Estimated time:** 3.5h (3h dev + 0.5h operator)
**Handoff doc:** `memory/handoff_iter_142_1_stage_1a.md`
**FMCSA-blocked?** No.

---

## Stage 1b: FMCSA SAFER On-Demand Ingestion + Vetting Workflow

> **AMENDED 2026-05-07 (Load-Board-First Pivot):** Original CSV-seed-batch model REPLACED with on-demand single-MC ingestion. Operator types MC number (from a load-board responder, referral, or hand-research) → system auto-pulls authority + insurance + SMS scores → presents vetting summary → operator signs off → carrier moves to "vetted, awaiting BCA + COI" → on receipt of signed BCA + valid COI → roster-active. Roster grows organically with load flow per `iter_142_1_carrier_vetting_checklist.md` operator playbook.

**Goal:** Stand up FMCSA SAFER on-demand ingestion + per-carrier vetting workflow. Roster grows one carrier at a time as load flow generates contacts (no upfront batch curation).

**What operator does (per carrier, ~15 min hands-on + 30min–2h async wait for BCA/COI receipt):**
1. (One-time, ~5 min) Provision FMCSA SAFER API key (free; sign up at safer.fmcsa.dot.gov developer portal). Vault entry: "FMCSA SAFER — API Key".
2. (One-time, ~5 min) Decide BCA template source per Q6: **Default: industry-standard (e.g., TIA-published template ~$0); attorney review optional, not blocking.**
3. (Per carrier) Receive MC number from load-board responder OR hand-research. Type into vetting CLI / endpoint: `python -m backend.carrier_ingestion --mc <number>`.
4. (Per carrier) Review system-generated vetting summary (authority status, insurance expiry, SMS safety score, red flags) per `iter_142_1_carrier_vetting_checklist.md` 10-step playbook + 27 red-flag list.
5. (Per carrier) Decide: VETTED / DECLINE / NEEDS-MORE-INFO. If DECLINE, send template decline reply (in checklist). If VETTED, send BCA + COI request.
6. (Per carrier, async) Track BCA + COI receipt in `iter_142_1_carrier_vetting_tracker_TEMPLATE.csv` (33-col spreadsheet). On receipt: flip carrier `vetting_status: "vetted_awaiting_docs"` → `"roster_active"`.

**What Dev Engineer does (~4h):**
1. `backend/carrier_ingestion.py`: `ingest_from_safer(mc_number)` — pulls authority + insurance, writes to `db.carriers` with `vetting_status: "pending_review"`.
2. `vetting_summary(mc_number)` — composes the operator-facing review packet: authority + insurance + SMS scores + red flags from `iter_142_1_carrier_vetting_checklist.md` rule set. Returns JSON for CLI display + frontend dashboard.
3. Carrier vetting state machine: `pending_review` → `vetted_awaiting_docs` (operator signs off) → `roster_active` (BCA + COI on file) → `blacklisted` (any future violation). State transitions via PATCH endpoint.
4. Insurance-expiry date parser (SAFER returns YYYYMMDD strings).
5. CLI: `python -m backend.carrier_ingestion --mc <number>` (single-MC ingestion + vetting summary) and `--mc-status <number>` (state-transition flag for vetted/declined/active).
6. Endpoint: `POST /api/carriers/vet` (admin-only; takes mc_number, returns vetting summary JSON), `PATCH /api/carriers/{mc_number}/vetting_status`.

**Code dependencies:**
- Stage 1a (schema — note: `vetting_status` field added to schema; if not in 1a's original spec, append to 1a list)
- New: FMCSA SAFER API client (raw `requests` calls — minimal SDK exists; ~50 LOC client)
- Existing: `backend/throttle_system.py` integration (rate-limit FMCSA calls — they're free but rate-capped)
- Cross-doc: `chl-memory/research/iter_142_1_carrier_vetting_checklist.md` for the 10-step + 27-red-flag rule set

**Smoke battery (6 tests):**
1. SAFER API client initializes with valid key (401 on bad key)
2. Single-mc import end-to-end (`ingest_from_safer("1817555")` → record in `db.carriers` with `vetting_status: "pending_review"`)
3. **Vetting summary correctness:** known-MC test cases trigger expected red flags (e.g., expired insurance MC → red flag fires; ACTIVE-authority MC → no red flag)
4. State machine transitions: PATCH valid transitions succeed, invalid transitions (e.g., `pending_review` → `roster_active` skipping `vetted_awaiting_docs`) reject 422
5. Insurance-expiry parsed correctly across edge dates (leap year, month boundaries)
6. Throttle integration (rapid-fire 50 calls → no FMCSA 429s, calls space at throttle cadence)

**Success criteria:**
- ✅ All 6 smoke tests PASS
- ✅ Operator-vetted seed batch in `db.carriers`: **1–2 carriers if any pre-existing relationships, OR zero (system stands ready for first load-board response)**
- ✅ Vetting CLI returns operator-readable summary in <30s per MC (FMCSA SAFER + L&I lookup combined)

**Estimated time:** 4.5h (4h dev + 0.5h operator setup; per-carrier vetting tracked separately at ~15 min hands-on + async wait per carrier)
**Handoff doc:** `memory/handoff_iter_142_1_stage_1b.md`
**FMCSA-blocked?** **Yes for go-live; No for development.** Dev can build + smoke against test mc_numbers (e.g., MC-1817555 itself once authority lands, OR public broker MCs for read-only enrichment) before May 13+. Operator's first vetted carrier (could be hand-research or load-board responder) is the go-live trigger.

---

## Stage 1c: CarrierEvaluator (Fractal Meso-Scale Scoring)

**Goal:** Score carriers on (profitability, reliability, risk, capacity) per FractalEvaluator architecture; produce composite ranking for outreach selection.

**What operator does (~5 min):**
1. Review composite-score weights (default: profitability 0.35, reliability 0.30, risk 0.20, capacity 0.15) in `memory/handoff_iter_142_1_stage_1c.md`.
2. Sign-off or override weights.

**What Dev Engineer does (~5h):**
1. `backend/evaluators/carrier_evaluator.py`: `class CarrierEvaluator(FractalEvaluator)` at meso scale.
2. Implement four parameters:
   - `profitability` = avg margin on past hauls (from `db.loads` lookup by `assigned_carrier_mc` — for v1, default 0.5 if no history)
   - `reliability` = on-time delivery % (from `db.loads.delivered_at` vs `delivery_due_at` — default 0.7 if no history)
   - `risk` = claim rate (from `db.claims` collection if exists, else derived from insurance expiry proximity — default 0.2)
   - `capacity` = hauls/month rolling avg (default 0.5 if no history)
3. Composite score: weighted sum, normalized to [0, 1].
4. `select_top_n_for_lane(lane, n=3)` — returns top-3 carriers by composite score where `lanes_served` includes `lane` AND `not blacklisted` AND `insurance_expiry > now`.
5. Endpoint: `GET /api/carriers/{mc_number}/score` and `GET /api/lanes/{o}/{s}/{d}/{s}/carriers/top/{N}`.

**Code dependencies:**
- Stage 1a + 1b (data exists)
- Existing: `backend/evaluators/fractal_evaluator.py` base class (shipped iter 141.1 stage 1a)
- Existing: `db.loads` for history lookups

**Smoke battery (5 tests):**
1. CarrierEvaluator instantiates without crash on empty `db.loads`
2. Composite scoring math (mocked carrier with known param values → expected composite within tolerance)
3. `select_top_n_for_lane` returns 3 carriers ordered by composite score
4. Edge case: lane with 0 carriers → returns empty list, no exception
5. Blacklisted + expired-insurance exclusions both filter correctly

**Success criteria:**
- ✅ All 5 smoke tests PASS
- ✅ `/api/lanes/.../carriers/top/3` returns ranked list against operator's seed batch
- ✅ Operator approves default weights (or override applied)

**Estimated time:** 5.5h (5h dev + 0.5h operator)
**Handoff doc:** `memory/handoff_iter_142_1_stage_1c.md`
**FMCSA-blocked?** No (uses already-ingested data; works against test fixtures pre-FMCSA).

---

## Stage 1d: Multi-Carrier Outreach Orchestration + Load-Board-Posting Fallback

> **AMENDED 2026-05-07 (Load-Board-First Pivot):** Original outreach-only model AUGMENTED with load-board-posting fallback. When `select_top_n_for_lane(load.lane, n=3)` returns empty (roster has no qualifying carrier for the load's lane — common at v1 with thin roster), system auto-posts to load board (DAT default per Q5; Truckstop optional), captures responder MCs, routes them through stage 1b on-demand vetting flow before booking. Roster grows asymmetrically with load flow.

**Goal:** Send rate offers to top-N carriers per lane in parallel (SMS + email, with voice fallback if no response in 30 min); track responses; first-accept wins. **Plus** auto-post to load board when roster has no qualifying carrier for the load's lane → capture responders → route through stage 1b vetting before booking.

**What operator does (~10 min):**
1. Review outreach copy templates (`memory/handoff_iter_142_1_stage_1d.md`):
   - SMS (GSM-7-safe, per `chl-memory/research/plivo_white_glove_packet_FINAL.md` §4 conventions): "CHL: Load Opportunity {load_id}, {origin} to {dest}, ${rate}, {equip}. Reply ACCEPT or DECLINE. MC-1817555. HELP for help, STOP to opt out."
   - Email subject + body
   - Voice TTS script
   - **Load-board posting copy:** title + description + rate + equipment + pickup/delivery dates (DAT post format default per Q5)
2. Compliance check: opt-in language matches signed broker-carrier agreements + GSM-7 encoding-safe (no em-dash, arrow, smart quotes) per FINAL packet (~5 min).
3. Sign-off.

**What Dev Engineer does (~6h):**
1. `backend/carrier_outreach.py`: `select_and_send(load_id)`:
   - Calls `CarrierEvaluator.select_top_n_for_lane(load.lane, n=3)`
   - **If non-empty:** for each carrier, enqueues SMS (if `sms_opt_in`) + email in parallel; records `db.carrier_offers` doc per carrier: `{load_id, mc_number, offered_rate, sent_at, response_at: null, status: "pending", channels_used: [...]}`.
   - **If empty (load-board fallback):** call `post_to_load_board(load_id)` instead → load enters `dispatch_status: "posted_awaiting_responders"`, no carrier offers created until first responder is vetted.
2. **NEW — `post_to_load_board(load_id)`:** posts load to DAT (reusing iter 141.2 stage 1d DAT integration; Truckstop optional per Q5). Captures inbound responders via webhook. Each responder MC routed to stage 1b vetting CLI/endpoint with `source: "load_board_responder"` flag.
3. **NEW — Vetted-responder booking flow:** when a load-board responder completes stage 1b vetting → `roster_active`, system auto-fires `select_and_send(load_id)` for that load (now has at least 1 qualifying carrier in lane) → outreach proceeds normally.
4. Voice fallback scheduler: if no `db.carrier_offers.response_at` set within 30 min → enqueue voice call via current SMS provider's voice API (Twilio or Plivo, whichever active).
5. Response handler: webhook on incoming SMS/email reply → parse "ACCEPT" / "DECLINE" / "STOP" → update `db.carrier_offers`, fire callback.
6. First-accept-wins: when one offer hits `status: "accepted"`, others auto-cancel (status: `"superseded"`), no further notifications.
7. Endpoint: `POST /api/loads/{load_id}/outreach` (manual trigger for testing + ops override). `POST /api/loads/{load_id}/post_to_board` (manual fallback trigger).

**Code dependencies:**
- Stage 1c (CarrierEvaluator)
- Stage 1b (vetting flow — load-board responders routed through it before booking)
- Existing: `backend/notification_service.py` (SMS — Twilio OR Plivo depending on iter 141.3 status)
- Existing: Resend client for email (assume already exists; if not, +30 min to add)
- **Existing (iter 141.2 stage 1d):** DAT integration (read-only currently; outbound posting needs additional ~50 LOC + DAT post-API auth)
- New: `db.carrier_offers` collection + indexes
- New: `db.loads.dispatch_status: "posted_awaiting_responders"` state added to existing dispatch_status enum

**Smoke battery (7 tests, originally; **+1 for load-board fallback = 8 total**):**
1. `select_and_send(load_id)` against mocked load with non-empty roster → 3 `db.carrier_offers` rows created
2. Channel selection: carrier with `sms_opt_in: false` → email only, no SMS attempted
3. Parallel send timing: 3 carriers × 2 channels = 6 sends complete within 5s wall-clock (parallelism works)
4. Response webhook: "ACCEPT" reply → offer status flips to "accepted", correct mc_number resolved
5. First-accept-wins: 2nd accept arrives after 1st → 2nd marked "superseded", no booking conflict
6. Voice fallback: simulate no-response 30-min window elapsed → voice call enqueued exactly once per carrier
7. STOP handling: "STOP" reply → carrier `sms_opt_in: false`, no future SMS attempts
8. **NEW — Load-board fallback:** mocked load with empty-roster lane → `select_and_send` triggers `post_to_load_board`, load `dispatch_status` flips to `"posted_awaiting_responders"`, no carrier offers created. Mocked DAT-responder webhook → responder MC routed to vetting endpoint with `source: "load_board_responder"`. After mocked vet-pass, `select_and_send` re-fires and creates 1 offer.

**Success criteria:**
- ✅ All 8 smoke tests PASS
- ✅ End-to-end (with roster): synthetic load → 3 offers sent → 1 accepts → status reflects in db
- ✅ End-to-end (empty roster): synthetic load → load-board post → mocked responder → vetting pass → outreach → accept
- ✅ Operator-approved copy templates (outreach + load-board post) in production

**Estimated time:** 7.0h (6.5h dev + 0.5h operator) — adds ~30 min for load-board-fallback wiring + smoke #8
**Handoff doc:** `memory/handoff_iter_142_1_stage_1d.md`
**FMCSA-blocked?** No for outreach side; load-board posting may be FMCSA-gated if DAT requires authority verification on post (operator confirms with DAT account during stage 1d kickoff).

---

## Stage 1e: Carrier Assignment + Booking Confirmation + Failure Detector

**Goal:** Lock-in accepted carrier on a load, send booking confirmation, and re-outreach if confirmation fails within 2h.

**What operator does (~5 min):**
1. Review assignment-lock criteria + 2h failure-detector threshold (`memory/handoff_iter_142_1_stage_1e.md`).
2. Sign-off.

**What Dev Engineer does (~4h):**
1. `auto_assign_carrier(load_id, carrier_mc)`:
   - Triggered when `db.carrier_offers` row flips to `"accepted"`
   - Updates `db.loads`: `{assigned_carrier_mc, carrier_rate, dispatch_status: "assigned", assigned_at: now}`
   - Sends booking confirmation (SMS + email): "Load {load_id} confirmed: pickup {pickup_at} at {pickup_addr}, rate ${rate}. Reply CONFIRM to lock."
2. Failure detector: scheduled task every 15 min checks `db.loads` where `dispatch_status: "assigned"` AND `confirmed_at IS NULL` AND `assigned_at < now - 2h` → triggers re-outreach to next-best carrier (call back into `select_and_send` excluding the unconfirmed mc).
3. CONFIRM reply handler: updates `db.loads.confirmed_at = now`, status → `"confirmed"`.
4. Endpoint: `POST /api/loads/{load_id}/assign_carrier` (manual override).

**Code dependencies:**
- Stage 1d (outreach module + db.carrier_offers)
- Existing: `db.loads`, throttle system, scheduled-task infrastructure
- New: failure-detector hook in scheduled task list

**Smoke battery (6 tests):**
1. `auto_assign_carrier` updates `db.loads` correctly (assigned_carrier_mc, dispatch_status, assigned_at)
2. Booking confirmation SMS + email sent with correct templated values
3. Failure detector triggers exactly at 2h boundary (test with time-warp: assigned_at = now - 2h1min)
4. Re-outreach excludes the unconfirmed carrier from new top-3 selection
5. CONFIRM reply flips `dispatch_status` to "confirmed", `confirmed_at` set
6. End-to-end: outreach → accept → assign → confirm flow completes in <60s wall-clock

**Success criteria:**
- ✅ All 6 smoke tests PASS
- ✅ Failure detector verified via time-warp test
- ✅ Re-outreach path verified end-to-end

**Estimated time:** 4.5h (4h dev + 0.5h operator)
**Handoff doc:** `memory/handoff_iter_142_1_stage_1e.md`
**FMCSA-blocked?** No.

---

## Stage 1f: Dispatch Packet Auto-Generation + Auto-Bid Integration Handoff

**Goal:** Generate rate-con + BCA + insurance-cert request as PDFs, deliver to confirmed carrier, AND wire `auto_dispatch.try_auto_accept` (iter 141.2) to call `carrier_outreach.select_and_send` post-booking.

**What operator does (~15 min):**
1. Review rate-con + BCA PDF templates (`memory/handoff_iter_142_1_stage_1f.md`) — fill legal-entity defaults, signature block, MC/USDOT cite (~10 min).
2. Send first synthetic load through end-to-end: auto-bid wins → outreach → assign → confirm → dispatch packet delivered → operator inspects PDFs (~5 min).
3. Sign-off.

**What Dev Engineer does (~6h):**
1. `backend/dispatch_packet.py`:
   - PDF templates (rate-con, BCA, insurance-cert request) using `reportlab` or similar lightweight lib
   - `generate_packet(load_id)` — fills templates with load + carrier + rate + dates → writes PDFs to local storage (or S3-equivalent), returns URLs
   - Delivery: email (Resend) with PDF attachments + SMS link
2. `db.dispatch_packets` collection: `{load_id, rate_con_url, bca_url, insurance_cert_url, sent_at, carrier_confirmed_at}`
3. Wire trigger: when `db.loads.dispatch_status` flips to "confirmed" → call `generate_packet(load_id)`.
4. **Auto-bid handoff (integration with iter 141.2):**
   - Modify `auto_dispatch.try_auto_accept` (existing) → after successful book, call `carrier_outreach.select_and_send(load_id)`
   - Single line of integration code; the heavy lifting is end-to-end smoke.
5. Endpoint: `GET /api/loads/{load_id}/dispatch_packet` (returns packet URLs).

**Code dependencies:**
- Stage 1e (assignment + confirmation flow)
- Existing: `backend/auto_dispatch.py` (iter 141.2 — integration target)
- New: PDF lib (`reportlab` ~2MB, no concerns)
- New: storage layer for PDFs (local disk acceptable at v1; ~50 packets/day expected)

**Smoke battery (5 tests):**
1. Rate-con PDF renders without crash, contains all required fields
2. BCA + insurance-cert PDFs render with correct entity + MC info
3. Packet delivery: email with attachments lands; SMS link returns valid URL
4. `db.dispatch_packets` row created with all URLs populated
5. **End-to-end (FMCSA-gated):** auto-bid wins synthetic load → outreach → assign → confirm → packet delivered → operator inspects PDFs visually

**Success criteria:**
- ✅ All 5 smoke tests PASS (test 5 may run in pre-FMCSA mode against mocked auto-bid)
- ✅ PDFs operator-approved
- ✅ Auto-bid → outreach handoff exercised in end-to-end test

**Estimated time:** 6.5h (6h dev + 0.5h operator)
**Handoff doc:** `memory/handoff_iter_142_1_stage_1f.md`
**FMCSA-blocked?** **Partially.** Smoke tests 1–4 work pre-FMCSA. Test 5 (full end-to-end with real auto-bid firing) gates on iter 141.2 auto-bid being live (post-FMCSA May 13+). Pre-FMCSA, simulate with `auto_dispatch.try_auto_accept` called manually against a synthetic load.

---

## FMCSA Blockers (consolidated)

| Stage | FMCSA-blocked for development? | FMCSA-blocked for go-live? | Workaround |
|-------|-------------------------------|---------------------------|------------|
| 1a | No | No | — |
| 1b | No | **Yes** (operator's seed batch is the go-live trigger; pre-auth, dev uses public broker MCs for read-only enrichment) | Test with MC-1817555 once authority lands; before that, use any public MC for SAFER read-only |
| 1c | No | No (works against test data) | — |
| 1d | No | No (SMS provider already live) | — |
| 1e | No | No | — |
| 1f | Smoke 1–4: No. Smoke 5 (e2e with auto-bid): **Yes** | **Yes** (real auto-bid firing requires iter 141.2 live) | Manually invoke `try_auto_accept` against synthetic load pre-FMCSA |

**Bottom line:** Iter 142.1 dev work proceeds **immediately after iter 141.3 ships, in parallel with FMCSA wait.** Go-live (production traffic) gates on FMCSA authority + iter 141.2 auto-bid live + iter 141.3 SMS migration complete.

---

## Iter 142.1 Close Ceremony

(Standard CHL iter close protocol — same as iter 141.1/141.2/141.3.)

- [ ] Verify smoke totals across stages 1a–1f (**33 tests** total: 4+6+5+7+6+5)
- [ ] Write `memory/handoff_iter_142_1_close.md` (Dev Engineer)
- [ ] Run `scripts/backup_to_d.ps1 -IterId "iter_142_1_close"` (full ceremony)
- [ ] Update `chl-memory/progress/current_status.md`: Phase 3 "Carrier Network & Dispatch" → IN PROGRESS or COMPLETE based on FMCSA-gated smoke 5 status
- [ ] Operator visual confirmation of dispatch packet PDFs against real (or final-synthetic) load
- [ ] Decide on iter 142.2 (Phase 4 Tracking) or other priority

**Iter 142.1 milestone met when:** System books carrier and delivers dispatch packet without operator intervention on at least one real load (or final-synthetic if FMCSA still pending).

---

## Common Pitfalls + Mitigations

### "FMCSA SAFER API rate-limited or down"
- **Cause:** Free API has no SLA; occasional outages.
- **Fix:** Throttle integration (already wired in stage 1b) — cap to 1 req/2s. Cache responses in `db.carriers` for 7 days. On API down, fall back to last-known authority/insurance data, flag carrier for re-verification when API returns.

### "Carrier accepts but never confirms"
- **Cause:** Carrier dispatcher gets distracted; common.
- **Fix:** Stage 1e failure detector — 2h timeout → auto re-outreach to next-best carrier. Operator alerted via SMS if 2 consecutive carriers fail to confirm.

### "Dispatch packet PDFs fail to render"
- **Cause:** Missing optional fields (e.g., carrier-specific notes), template encoding issues.
- **Fix:** Defensive defaults in template fill; render errors logged + operator-alerted, packet generation retries once before flagging load `dispatch_status: "packet_failed"` for manual handling.

### "Vetted carriers don't accept any offers"
- **Cause:** Stale phone/email; carrier no longer brokering CHL's lanes; rate offer below market.
- **Fix:** Reliability score auto-decays for non-responsive carriers (stage 1c parameter), pushing them down the ranking. Stage 1d load-board fallback fires when no qualifying carrier accepts → captures fresh responder pool. Operator can manually re-vet existing carriers via stage 1b vetting CLI to refresh their contact info.

### "Auto-bid books load before carriers exist for that lane (empty roster)"
- **Cause:** Iter 141.2 auto-bid runs before stage 1b on-demand vetting populates carriers for the lane. Common at v1 (thin roster).
- **Fix:** Stage 1d `select_and_send` returns empty list → triggers load-board-posting fallback (DAT default per Q5; Truckstop optional) → load enters `dispatch_status: "posted_awaiting_responders"`. Inbound responders auto-routed to stage 1b vetting; on first vet-pass, `select_and_send` re-fires and outreach proceeds. Operator-alert fires only if no responders within configurable timeout (default ~4h on the load-board post).

---

## What to skip / NOT do during iter 142.1

- ❌ Don't integrate paid carrier data sources (Highway/RMIS/CarrierAssure) — defer to post-revenue.
- ❌ Don't build self-serve carrier registration — defer to post-revenue (10DLC compliance surface too costly).
- ❌ Don't implement counter-offer rate negotiation — fixed offer at v1; track rejections for v2 ML.
- ❌ Don't add per-shipper carrier whitelist/blacklist — global blacklist only at v1.
- ❌ Don't build GPS tracking integration — Phase 4 (iter 142.2+).
- ❌ Don't build POD/invoicing — Phase 5 (iter 143.x).
- ❌ Don't ship stage 1f end-to-end smoke 5 before FMCSA + iter 141.2 auto-bid live.

---

**Status:** ✅ AGENDA COMPLETE. Ready for operator/dev-engineer review before iter 142.1 kickoff.

**Aggregate stats (post-Load-Board-First Pivot amendment):**
- 6 stages (1a–1f), ~850 LOC dev (was ~800; +50 for load-board-fallback wiring + vetting state machine), **34 smoke tests** total (was 33; +1 in stage 1d for load-board fallback)
- Operator hands-on: **~40 min core + per-carrier vetting (~15 min hands-on per carrier, async wait for BCA/COI)** — was ~70 min upfront in CSV-batch model
- Dev hours: ~28.5h (~3.5 wks at 8h/wk solo cadence)
- Wall-clock to milestone: 3 wks dev + FMCSA wait window for go-live + roster-grows-with-load-flow (asymmetric)

**Cross-reference:**
- `chl-memory/research/iter_142_1_phase_3_sketch.md` (**SUPERSEDED** by this agenda — kept for history) — preliminary 4-stage sketch (this agenda expands to 6)
- `chl-memory/CHL_STRATEGIC_PLAYBOOK.md` Phase 3 boundary (lines 273–277)
- `chl-memory/research/iter_141_2_agenda_draft.md` — auto-bid module (1f integration target)
- `chl-memory/research/iter_141_3_agenda_draft.md` — SMS provider migration (1d outreach module dependency)
- `chl-memory/research/iter_142_1_pre_flight_brief.md` — operator gotchas pre-kickoff (companion doc, dev-authored)
- `chl-memory/research/iter_142_1_carrier_vetting_checklist.md` — operator vetting playbook (10 steps + 27 red flags + decline template + time budgets) — **load-bearing for stage 1b on-demand vetting**
- `chl-memory/research/iter_142_1_carrier_vetting_tracker_TEMPLATE.csv` — 33-column tracking spreadsheet operator can use immediately pre-iter-142.1-stage-1b ship

**Source:** Drafted by @pm-lead 2026-05-07 from sketch `iter_142_1_phase_3_sketch.md` + CHL_STRATEGIC_PLAYBOOK.md Phase 3 boundary + iter_141_3_agenda_draft.md pattern.
