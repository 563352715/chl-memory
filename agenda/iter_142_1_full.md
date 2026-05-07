# Iter 142.1 Full Agenda — Phase 3 Carrier Network (Outreach + Assignment + Dispatch)

> **Iteration goal:** Build multi-carrier outreach, assignment, and dispatch automation. Operator (or auto-bid from iter 141.2) books load → system selects optimal carrier → dispatch packet auto-generated and delivered.
> **Timeline:** ~3 weeks dev (sequential 1a→1f, ~800 LOC total) + FMCSA-blocked go-live windows (see "FMCSA Blockers" section).
> **Phase boundary (from `CHL_STRATEGIC_PLAYBOOK.md`):** Phase 3 milestone = "System can book carrier and dispatch without operator." Out-of-scope: GPS tracking (Phase 4), POD/invoicing (Phase 5), factoring (Phase 6).
> **Source sketch:** `chl-memory/research/iter_142_1_phase_3_sketch.md` (4 stages, here expanded to 6 by splitting Carrier DB into schema + ingestion + scoring).

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

## Stage 1b: FMCSA SAFER Ingestion + CSV Import

**Goal:** Pull carrier records from FMCSA SAFER API (authority + insurance) and import operator-curated CSV batches.

**What operator does (~30 min):**
1. Provision FMCSA SAFER API key (free; sign up at safer.fmcsa.dot.gov developer portal).
2. Vault entry: "FMCSA SAFER — API Key" (~5 min).
3. Hand-curate first batch CSV (~20 carriers operator already trusts) — `data/carriers_seed_batch1.csv` with `mc_number, legal_name, contact_phone, contact_email, lanes_served, sms_opt_in` (~20 min).
4. Run import: `& "C:\CHL\.venv-local\Scripts\python.exe" -m backend.carrier_ingestion --csv data/carriers_seed_batch1.csv` (~3 min wall, 2 min wait).
5. Review log + spot-check 3 carriers in db (~2 min).

**What Dev Engineer does (~4h):**
1. `backend/carrier_ingestion.py`: `ingest_from_safer(mc_number)` — pulls authority + insurance, writes to `db.carriers` (upsert).
2. CSV importer: `ingest_from_csv(path)` — parses, validates, calls `ingest_from_safer` per row to enrich, then upserts.
3. Dedup logic on `mc_number` (upsert with merge — CSV operator-supplied fields override SAFER for `contact_phone`/`contact_email`/`sms_opt_in`/`lanes_served`).
4. Insurance-expiry date parser (SAFER returns YYYYMMDD strings).
5. Endpoint: `POST /api/carriers/import` (admin-only; takes CSV path or single mc_number).
6. CLI: `python -m backend.carrier_ingestion --csv <path>` and `--mc <number>`.

**Code dependencies:**
- Stage 1a (schema)
- New: FMCSA SAFER API client (raw `requests` calls — minimal SDK exists; ~50 LOC client)
- Existing: `backend/throttle_system.py` integration (rate-limit FMCSA calls — they're free but rate-capped)

**Smoke battery (6 tests):**
1. SAFER API client initializes with valid key (401 on bad key)
2. Single-mc import end-to-end (`ingest_from_safer("1817555")` → record in `db.carriers`)
3. CSV parser handles 20-row batch without crash
4. Dedup correctness (re-import same CSV → no duplicate inserts, fields merged correctly)
5. Insurance-expiry parsed correctly across edge dates (leap year, month boundaries)
6. Throttle integration (rapid-fire 50 calls → no FMCSA 429s, calls space at throttle cadence)

**Success criteria:**
- ✅ All 6 smoke tests PASS
- ✅ Operator's seed batch (20 carriers) in `db.carriers`
- ✅ Each ingested carrier has SAFER-enriched authority + insurance fields populated

**Estimated time:** 4.5h (4h dev + 0.5h operator)
**Handoff doc:** `memory/handoff_iter_142_1_stage_1b.md`
**FMCSA-blocked?** **Yes for go-live; No for development.** Dev can build + smoke against test mc_numbers (e.g., MC-1817555 itself once authority lands, OR public broker MCs for read-only enrichment) before May 13+. Operator-batch-seeded production carriers are the go-live trigger.

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

## Stage 1d: Multi-Carrier Outreach Orchestration

**Goal:** Send rate offers to top-3 carriers per lane in parallel (SMS + email, with voice fallback if no response in 30 min); track responses; first-accept wins.

**What operator does (~10 min):**
1. Review outreach copy templates (`memory/handoff_iter_142_1_stage_1d.md`):
   - SMS (GSM-7-safe, per `chl-memory/research/plivo_white_glove_packet_FINAL.md` §4 conventions): "CHL: Load Opportunity {load_id}, {origin} to {dest}, ${rate}, {equip}. Reply ACCEPT or DECLINE. MC-1817555. HELP for help, STOP to opt out."
   - Email subject + body
   - Voice TTS script
2. Compliance check: opt-in language matches signed broker-carrier agreements + GSM-7 encoding-safe (no em-dash, arrow, smart quotes) per FINAL packet (~5 min).
3. Sign-off.

**What Dev Engineer does (~6h):**
1. `backend/carrier_outreach.py`: `select_and_send(load_id)`:
   - Calls `CarrierEvaluator.select_top_n_for_lane(load.lane, n=3)`
   - For each carrier: enqueues SMS (if `sms_opt_in`) + email in parallel
   - Records `db.carrier_offers` doc per carrier: `{load_id, mc_number, offered_rate, sent_at, response_at: null, status: "pending", channels_used: [...]}`
2. Voice fallback scheduler: if no `db.carrier_offers.response_at` set within 30 min → enqueue voice call via current SMS provider's voice API (Twilio or Plivo, whichever active).
3. Response handler: webhook on incoming SMS/email reply → parse "ACCEPT" / "DECLINE" / "STOP" → update `db.carrier_offers`, fire callback.
4. First-accept-wins: when one offer hits `status: "accepted"`, others auto-cancel (status: `"superseded"`), no further notifications.
5. Endpoint: `POST /api/loads/{load_id}/outreach` (manual trigger for testing + ops override).

**Code dependencies:**
- Stage 1c (CarrierEvaluator)
- Existing: `backend/notification_service.py` (SMS — Twilio OR Plivo depending on iter 141.3 status)
- Existing: Resend client for email (assume already exists; if not, +30 min to add)
- New: `db.carrier_offers` collection + indexes

**Smoke battery (7 tests):**
1. `select_and_send(load_id)` against mocked load → 3 `db.carrier_offers` rows created
2. Channel selection: carrier with `sms_opt_in: false` → email only, no SMS attempted
3. Parallel send timing: 3 carriers × 2 channels = 6 sends complete within 5s wall-clock (parallelism works)
4. Response webhook: "ACCEPT" reply → offer status flips to "accepted", correct mc_number resolved
5. First-accept-wins: 2nd accept arrives after 1st → 2nd marked "superseded", no booking conflict
6. Voice fallback: simulate no-response 30-min window elapsed → voice call enqueued exactly once per carrier
7. STOP handling: "STOP" reply → carrier `sms_opt_in: false`, no future SMS attempts

**Success criteria:**
- ✅ All 7 smoke tests PASS
- ✅ End-to-end: synthetic load → 3 offers sent → 1 accepts → status reflects in db
- ✅ Operator-approved copy templates in production

**Estimated time:** 6.5h (6h dev + 0.5h operator)
**Handoff doc:** `memory/handoff_iter_142_1_stage_1d.md`
**FMCSA-blocked?** No (works against test/seed carriers; SMS provider already live from 141.2/141.3).

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

### "Operator's seed batch carriers don't accept any offers"
- **Cause:** Stale phone/email; carrier no longer brokering CHL's lanes.
- **Fix:** Stage 1b CSV import is incremental — operator can refresh batch CSV and re-import. Reliability score auto-decays for non-responsive carriers (stage 1c parameter), pushing them down the ranking.

### "Auto-bid books load before carriers exist for that lane"
- **Cause:** Iter 141.2 auto-bid runs before stage 1b ingestion populates carriers for the lane.
- **Fix:** Stage 1d `select_and_send` returns empty list → fallback path: load enters `dispatch_status: "no_carriers_available"`, operator alerted to manual handle. Future iter (142.2+) can extend FMCSA SAFER ingestion to dynamically find new carriers per-lane on demand.

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

**Aggregate stats:**
- 6 stages (1a–1f), ~800 LOC dev, **33 smoke tests** total
- Operator hands-on: ~70 min across iter
- Dev hours: ~28h (~3.5 wks at 8h/wk solo cadence)
- Wall-clock to milestone: 3 wks dev + FMCSA wait window for go-live

**Cross-reference:**
- `chl-memory/research/iter_142_1_phase_3_sketch.md` (**SUPERSEDED** by this agenda — kept for history) — preliminary 4-stage sketch (this agenda expands to 6)
- `chl-memory/CHL_STRATEGIC_PLAYBOOK.md` Phase 3 boundary (lines 273–277)
- `chl-memory/research/iter_141_2_agenda_draft.md` — auto-bid module (1f integration target)
- `chl-memory/research/iter_141_3_agenda_draft.md` — SMS provider migration (1d outreach module dependency)
- `chl-memory/research/iter_142_1_pre_flight_brief.md` — operator gotchas pre-kickoff (companion doc, dev-authored)

**Source:** Drafted by @pm-lead 2026-05-07 from sketch `iter_142_1_phase_3_sketch.md` + CHL_STRATEGIC_PLAYBOOK.md Phase 3 boundary + iter_141_3_agenda_draft.md pattern.
