# Iter 142.1 Stages 1c-1f Refresh — Tightened Executable Spec

> **Source:** PM Claude (`@pm-lead`) drafted 2026-05-07 EOD-3 per @dev-engineer task brief. **Refreshes** the iter 142.1 stages 1c-1f bodies in `iter_142_1_full.md` against today's CHL-repo reality (Phases 1+2+4+5 SHIPPED, observability layer LIVE, Plivo BLOCKED, FMCSA gate ~May 13). Intended as a **tightening overlay**, not a replacement — the original agenda remains authoritative on goal/structure; this refresh tightens code-surface specifics, adds real-data smoke assertions, hooks outcome-feedback per `phase_6_self_healing_roadmap.md` Layer 5, and tags trust-gate input failure modes per the iter 145.1 self-healing pipeline.
> **Scoring rubric:** every stage scored on operator's 2026-05-07 north-star pillars: **(R)** removes operator-touch, **(O)** adds observability surface, **(S)** substrate for self-healing.

---

## Context Reset (the "today" picture)

The dependency math has shifted significantly since the original 1c-1f stage bodies were written:

1. **Phase 1+2+4+5 SHIPPED** — 480,912 active for-hire carriers in production `db.carriers` (cold-start solved). Vetting workflow + UI + SMS scores fetcher live.
2. **Auto-refresh crons LIVE** (CHL `501268a`) — `sms.basics.*`, `equipment_types[]`, `cargo_classifications[]`, `insurance.*` sub-docs on `db.carriers` populating from monthly bulk + per-carrier cycles.
3. **Observability layer LIVE** (CHL `cb2f15f` + `d24b979`) — frontend `client_error_logger` + `client_error_analyzer` cron + `db.client_error_fingerprints` + `/api/_client_errors/digest` + `/api/carriers/freshness` endpoints.
4. **Stage 1c CarrierEvaluator** is now load-bearing for first dispatch (per `097e580` audit) — without it, no `select_top_n_for_lane` exists and the 480K-carrier roster has no scoring/selection logic.
5. **Iter 141.3 Plivo BLOCKED** at vendor signup — outreach module in 1d needs Twilio-Voice fallback path until 141.3 unblocks.
6. **FMCSA broker authority lands ~May 13** — unblocks DAT subscription, real-load discovery, autonomous booking (gates 1f end-to-end smoke).
7. **iter 145.1 Self-Healing Foundation roadmap FILED** (commit `7d658cc`) — Layer 5 outcome-feedback schema (`db.evaluator_outcomes`) is the integration target for stages 1c-1f outcome hooks. Trust-gate matrix (LOW/MED/HIGH) is the framework for surfacing patchable failure modes per stage.

**Synthetic-fixture era is over** — every stage's smoke battery includes at least one real-data assertion against `db.carriers`.

---

## Stage 1c — CarrierEvaluator (Fractal Meso-Scale Scoring)

**Goal restatement:**
- **Original:** build composite-scoring of carriers + `select_top_n_for_lane` for outreach selection.
- **Revised:** same goal, but now operates against 480K real carriers with sub-doc enrichments populating from auto-refresh crons (`sms.basics.*`, `insurance.*`, `equipment_types[]`, `cargo_classifications[]`). Mirrors `LoadEvaluator` (micro) + `LaneEvaluator` (meso) FractalEvaluator class shape per `FRACTAL_DECISION_FRAMEWORK.md`.

**North-star scoring:** **R=HIGH** (no operator-touch in selection — system picks top-N), **O=MED** (per-call score logging via `@track_sla` + explicit `db.carrier_scores` collection adds observability), **S=HIGH** (CarrierEvaluator outcomes feed iter 145.1 Layer 5 re-tuning loop)

**What operator does (~5 min):**
1. Review composite-score weights (default: profitability 0.35, reliability 0.30, risk 0.20, capacity 0.15) in `memory/handoff_iter_142_1_stage_1c.md`
2. Sign-off or override (operator may want reliability-heavier weighting given pre-revenue carrier-quality sensitivity)

**What dev does (~5h):**

1. **`backend/evaluators/carrier_evaluator.py`** — class mirroring `lane_evaluator.py` + `load_evaluator.py`:

```python
DEFAULT_CARRIER_WEIGHTS = {
    "profitability": 0.35,
    "reliability": 0.30,
    "risk": 0.20,         # inverted in composite (1 - risk)
    "capacity": 0.15,
}

class CarrierEvaluator(FractalEvaluator):
    scale = "meso"
    parameters = ["profitability", "reliability", "risk", "capacity"]
    
    def __init__(self, weights: dict | None = None, weights_version: str = "v1.0"):
        self.weights = weights or DEFAULT_CARRIER_WEIGHTS
        self.weights_version = weights_version
    
    async def evaluate(self, mc_number: str, origin_state: str, dest_state: str, equipment: str) -> CarrierScore:
        carrier = await db.carriers.find_one({"mc_number": mc_number})
        if not carrier:
            return CarrierScore(composite=0.0, reason="not_found")
        if carrier.get("vetting_status") not in ("vetted", "active", "roster_active"):
            return CarrierScore(composite=0.0, reason="not_vetted")
        if carrier.get("blacklisted"):
            return CarrierScore(composite=0.0, reason="blacklisted")
        
        prof = await self._compute_profitability(carrier, origin_state, dest_state)
        rel = self._compute_reliability(carrier)
        risk = self._compute_risk(carrier)
        cap = self._compute_capacity(carrier)
        
        composite = (
            prof * self.weights["profitability"]
            + rel * self.weights["reliability"]
            + (1 - risk) * self.weights["risk"]
            + cap * self.weights["capacity"]
        )
        
        # Outcome-feedback hook (iter 145.1 Layer 5)
        await db.evaluator_outcomes.insert_one({
            "evaluator_class": "carrier",
            "scored_entity_id": mc_number,
            "predicted_score_at": now_utc(),
            "predicted_composite": composite,
            "predicted_params": {"profitability": prof, "reliability": rel, "risk": risk, "capacity": cap},
            "weights_version": self.weights_version,
            "lane": f"{origin_state}->{dest_state}",
            "equipment": equipment,
            "actual_outcome_observed_at": None,  # backfilled by 1d/1e/1f hooks
        })
        
        return CarrierScore(composite=composite, params={...}, reason="computed")
    
    async def _compute_profitability(self, carrier, o, d):
        # Pulls db.loads where assigned_carrier_mc == carrier.mc_number, near-lane filter
        # Returns avg margin on past hauls (default 0.5 if no history)
        ...
    
    def _compute_reliability(self, carrier):
        # Pulls carrier.sms.basics.unsafe_driving + hours_of_service + vehicle_maintenance scores (when available)
        # Plus carrier.last_haul_date recency
        # Returns 0.0-1.0 (default 0.7 if no SMS data — most CHL carriers will be SMS-data-rich post-Phase-2 batch)
        ...
    
    def _compute_risk(self, carrier):
        # Pulls carrier.insurance.bipd.cancellation_date proximity (closer = riskier)
        # Plus carrier.sms.basics.crash_indicator (when available)
        # Plus carrier.fmcsa.authority_status (out-of-service = max risk)
        # Returns 0.0-1.0 (higher = riskier; default 0.2 if no insurance data — pessimistic given missing data is itself a yellow flag)
        ...
    
    def _compute_capacity(self, carrier):
        # Pulls carrier.fleet.power_units + carrier.drivers.total
        # Returns 0.0-1.0 (single-truck owner-operator = 0.1, mega-fleet = 1.0)
        ...
```

2. **`db.carrier_scores` collection** — for operator-visible scoring + audit trail (separate from per-call `db.evaluator_outcomes`):
```
{
  score_id: str,
  mc_number: str,
  origin_state: str,
  dest_state: str,
  equipment: str (e.g., "VAN", "REEFER", "FLATBED", "TANK", "STEPDECK"),
  predicted_at: datetime,
  predicted_composite: float (0.0-1.0),
  predicted_params: {profitability, reliability, risk, capacity},
  weights_version: str,
  ttl_at: datetime (1h auto-expire to avoid stale scores; can be re-evaluated cheaply)
}
```
Indexes: compound on `(mc_number, origin_state, dest_state, equipment)`, TTL on `ttl_at`.

3. **`select_top_n_for_lane(origin: str, dest: str, equipment: str, n: int = 5) -> List[CarrierScore]`** — server-side function:
```python
async def select_top_n_for_lane(origin, dest, equipment, n=5):
    candidates = db.carriers.find({
        "vetting_status": {"$in": ["vetted", "active", "roster_active"]},
        "blacklisted": {"$ne": True},
        "$or": [
            {"insurance.bipd.cancellation_date": {"$gt": now_utc()}},
            {"insurance.bipd.cancellation_date": None},  # null = uncancelled
        ],
        "equipment_types": equipment,  # populated by SAFER cargo-classifications fetcher
    }).limit(500)  # rough first-pass filter, then score top 500
    
    evaluator = CarrierEvaluator()
    scored = []
    async for c in candidates:
        score = await evaluator.evaluate(c["mc_number"], origin, dest, equipment)
        if score.composite > 0:
            scored.append(score)
    
    scored.sort(key=lambda s: s.composite, reverse=True)
    return scored[:n]
```

4. **Endpoints:**
   - `GET /api/carriers/{mc_number}/score?o={state}&d={state}&equipment={type}` — single-carrier score
   - `GET /api/lanes/{o}/{d}/carriers/top/{N}?equipment={type}` — top-N for lane
5. **SLA target:** `carrier_eval` registered with `target_ms=2_000` (per-carrier eval should be <2s).
6. **Wire `db.evaluator_outcomes` hook** from day one (per iter 145.1 Layer 5) — every `evaluate()` call writes outcome row for later actual-vs-predicted feedback.
7. **Health-check registration** — `carrier_evaluator` module appears in `/api/health/system` (extends today's 14-module count to 15+).

**Smoke battery (6 tests, 3 real-data):**

1. **REAL-DATA:** `select_top_n_for_lane("CA", "TX", "VAN", n=5)` against real db.carriers returns ≤5 results, all with `vetting_status in ("vetted", "active", "roster_active")` AND `blacklisted=False` AND `insurance.bipd.cancellation_date > now OR null` AND `"VAN" in equipment_types`
2. **REAL-DATA:** `select_top_n_for_lane("AK", "PR", "TANK", n=5)` (sparse-lane edge case) returns empty list, no exception (caller handles → load-board fallback in 1d)
3. Composite scoring math: mocked carrier with known param values → expected composite within 0.01 tolerance
4. Insurance-expiry exclusion: carrier with `insurance.bipd.cancellation_date < now` filtered out
5. Equipment filter: carrier with `equipment_types: ["VAN"]` excluded from FLATBED query
6. **REAL-DATA:** Score every carrier in a sample lane (CA→AZ VAN), write all results to `db.carrier_scores` with correct TTL; verify `db.evaluator_outcomes` has matching rows (one per evaluate call)

**Dependency graph:**
- **Depends on:** Phases 1-5 ✅ (carriers + vetting workflow) + auto-refresh crons ✅ (sub-doc enrichment populating in real-time) + observability ✅ (SLA tracking)
- **Blocks:** 1d (outreach `select_and_send` calls this), 1e (re-outreach next-best calls this), 1f (auto-bid integration uses this for first-pass selection)

**Can ship in parallel during FMCSA + Plivo wait?** **YES** — no external blockers. 1c can be built + smoke-tested entirely against existing 480K-carrier db.carriers data. **Recommend ship-first** of the 1c-1f sequence since it's the only stage with zero external blockers AND it's load-bearing for 1d/1e/1f.

**Outcome-feedback integration (per iter 145.1 Layer 5):**
- On every `evaluate()` call → `db.evaluator_outcomes` row inserted (predicted side)
- On load completion (later, when 1f + iter 143.x ship): `actual_composite` field backfilled based on realized margin/reliability/etc.
- On 1d offer accept/decline → updates outcome row's `actual_outcome_observed_at` + `actual_params.reliability`
- Re-tuning cron (iter 145.x) reads outcome rows + computes per-evaluator MAE; proposes weight changes through trust-gate framework

**Trust-gate inputs (most patchable failure modes for iter 145.1):**
1. **Composite-weight tuning** — if outcome variance shows weights are off (e.g., reliability over/underweighted), self-healing proposes weight adjustment within ±10%. **LOW-risk auto-merge** per trust-gate matrix (threshold tuning within ±10% of current value, in a non-auth/billing file).
2. **Cold-start default tuning** — if many carriers return `default 0.5` for profitability (no haul history), self-healing proposes adjusting cold-start defaults based on actual cold-start carrier outcomes. **LOW-risk** (defensive default in evaluator helper).

---

## Stage 1d — Multi-Carrier Outreach Orchestration + Load-Board-Posting Fallback

**Goal restatement:**
- **Original:** send rate offers to top-N carriers in parallel (SMS + email), with voice fallback if no response in 30 min; load-board-posting fallback per Load-Board-First amendment.
- **Revised:** same goal, but now stage 1c gives real top-N candidates from 480K-carrier roster (vs. the original 20-carrier-CSV-batch assumption). **Critical addition:** iter 141.3 Plivo SMS path BLOCKED → **Twilio Voice TTS-call fallback is the v1 outreach channel until Plivo cutover**. When 141.3 ships, swap to Plivo SMS as primary + voice as 30-min fallback per original spec. Plus the load-board-posting fallback per Load-Board-First amendment (still relevant for thin-lane/equipment scenarios).

**North-star scoring:** **R=HIGH** (auto-fires offers + load-board posting without operator), **O=MED** (`db.carrier_offers` + offer-channel telemetry), **S=MED** (failure modes around bad rate or stale phone are agent-fixable in iter 145.x)

**What operator does (~10 min):**
1. Review outreach copy templates (SMS + email + voice TTS script + load-board posting copy) in `memory/handoff_iter_142_1_stage_1d.md`
2. Compliance check: opt-in language matches signed broker-carrier agreements + GSM-7 encoding-safe per `plivo_white_glove_packet_FINAL.md` §4
3. Sign-off

**What dev does (~6.5h, +30 min for Plivo-wait Twilio-Voice path over original 6h):**

1. **`backend/carrier_outreach.py`: `select_and_send(load_id)`** — reads load lane + equipment, calls `CarrierEvaluator.select_top_n_for_lane(origin, dest, equipment, n=5)`. If non-empty: enqueue per-carrier outreach. If empty: trigger `post_to_load_board(load_id)`.

2. **Twilio Voice fallback (NEW, for iter 141.3 wait period):** if `notification_service.sms_provider != "plivo"` (i.e., 141.3 cutover not yet shipped), fall back to **Twilio Voice TTS call as primary outreach channel** (operator already has Twilio Voice working per iter 141.2 baseline). When 141.3 cutover happens, swap to Plivo SMS as primary + voice as 30-min fallback per original spec. Include `db.carrier_offers.sms_provider_used` field tracking which provider delivered each offer (audit trail for pre-vs-post-cutover comparison).

3. **Email path** via existing Resend wrapper (no new code).

4. **`db.carrier_offers` schema** (per carrier per load):
```
{
  offer_id: str,
  load_id: str,
  mc_number: str,
  offered_rate: float,
  sent_at: datetime,
  response_at: datetime | None,
  status: "pending" | "accepted" | "declined" | "superseded" | "no_response_30min",
  channels_used: ["sms", "email", "voice"],
  sms_provider_used: "twilio" | "twilio_voice_fallback" | "plivo" | None,
}
```

5. **Load-board posting fallback (`post_to_load_board(load_id)`):** posts load to DAT (default per Q5; reuses iter 141.2 stage 1d DAT integration). Captures inbound responders via webhook → routes responder MC to stage 1b vetting CLI/endpoint with `source: "load_board_responder"` flag.

6. **Vetted-responder auto-routing:** when a load-board responder completes stage 1b vetting → `vetting_status: "vetted_awaiting_docs"` (or beyond) → system auto-fires `select_and_send(load_id)` for that load (now has at least 1 qualifying carrier in lane) → outreach proceeds.

7. **First-accept-wins:** when one offer hits `status: "accepted"`, others auto-cancel (`status: "superseded"`), no further notifications.

8. **Voice 30-min fallback (post-141.3-cutover):** if no `response_at` within 30 min on SMS → enqueue voice call via current SMS provider's voice API.

9. **Endpoints:** `POST /api/loads/{load_id}/outreach` (manual trigger), `POST /api/loads/{load_id}/post_to_board` (manual fallback trigger).

10. **SLA target:** `carrier_offer_send` registered with `target_ms=5_000` (parallel send to N carriers should complete <5s).

11. **Outcome-feedback hook:** on offer `accepted` → update the predicted CarrierEvaluator outcome row with `actual_outcome_observed_at` + `actual_params.reliability_signal: 1.0`. On `declined` or `no_response_30min` → `reliability_signal: 0.0`. On `superseded` → no signal (carrier was a runner-up, not selected).

**Smoke battery (8 tests, 2 real-data; +1 for Twilio-Voice fallback path over original 7):**

1. **REAL-DATA:** `select_and_send(synthetic_load_id, lane="IL→TX", equipment="VAN")` against real roster → 3-5 db.carrier_offers rows created with `mc_number` resolving to real db.carriers entries
2. **Pre-141.3-cutover path:** synthetic load → outreach via Twilio Voice (mocked TTS), `sms_provider_used: "twilio_voice_fallback"`
3. **Post-141.3-cutover path:** synthetic load → outreach via Plivo SMS (mocked), `sms_provider_used: "plivo"`
4. Channel selection: carrier with `sms_opt_in: false` → email + voice only, no SMS attempted
5. Response webhook: "ACCEPT" reply → `status` flip to `accepted`, correct mc_number resolved, outcome-feedback row updated
6. First-accept-wins: 2nd accept arrives after 1st → 2nd marked `superseded`, no booking conflict
7. **REAL-DATA:** Empty-roster lane (sparse equipment, e.g., MT→VT TANK) → `post_to_load_board(load_id)` fires, `dispatch_status: "posted_awaiting_responders"`
8. Vetted-responder routing: mocked DAT responder MC → vetting endpoint with `source: "load_board_responder"` → after mocked vet-pass → `select_and_send` re-fires + creates 1 offer

**Dependency graph:**
- **Depends on:** Stage 1c (`select_top_n_for_lane`) + iter 141.2 stage 1d DAT integration (for load-board posting; may need outbound-posting addition ~50 LOC + DAT post-API auth) + Twilio Voice (already live; for iter 141.3 wait period) OR Plivo SMS (post-141.3 cutover)
- **Blocks:** 1e (assignment needs offer state)

**Can ship in parallel during FMCSA + Plivo wait?** **PARTIAL** — outreach core + Twilio-Voice fallback path can ship + smoke-test today (no Plivo dep, no FMCSA dep). DAT load-board posting may be FMCSA-gated if DAT requires authority verification on post (operator confirms with DAT account at stage 1d kickoff). Plivo-cutover swap (smoke #3) ships when iter 141.3 ships.

**Outcome-feedback (per iter 145.1 Layer 5):**
- On accept → update CarrierEvaluator outcome with actual reliability signal +1
- On decline → update with reliability signal 0
- On no-response-30min → update with reliability signal -0.5 (penalize ghosting)
- On superseded → no update (carrier was runner-up, not the choice — no signal in either direction)

**Trust-gate inputs:**
1. **Outreach-copy tuning** — if accept rate degrades (rolling 7-day metric), self-healing proposes copy tweaks. **LOW-risk auto-merge** if just message text in non-auth/billing file. **MED-risk dev-DM** if changes opt-in language (touches compliance).
2. **Channel-selection weight tuning** — if voice has higher accept rate than SMS, self-healing can shift channel preference order. **LOW-risk** (threshold tuning).

---

## Stage 1e — Carrier Assignment + Booking Confirmation + Failure Detector

**Goal restatement:**
- **Original:** lock-in accepted carrier on a load, send booking confirmation, re-outreach if confirmation fails within 2h.
- **Revised:** same goal, **plus integration with the iter 145.1 outcome-feedback loop** (every assigned-carrier outcome feeds CarrierEvaluator reliability re-tuning per Layer 5).

**North-star scoring:** **R=HIGH** (auto-assignment + auto re-outreach without operator), **O=HIGH** (`db.loads.dispatch_status` state machine + 2h failure detector telemetry + outcome-feedback writes), **S=MED** (failure detector threshold + re-outreach exclusion logic are patchable)

**What operator does (~5 min):**
1. Review 2h failure-detector threshold + assignment-lock criteria in `memory/handoff_iter_142_1_stage_1e.md`
2. Sign-off (operator may want shorter threshold for time-critical lanes — 90 min for hot loads)

**What dev does (~4h):**

1. **`auto_assign_carrier(load_id, carrier_mc)`** — triggered when `db.carrier_offers` row flips to `accepted`:
   - Updates `db.loads`: `{assigned_carrier_mc, carrier_rate, dispatch_status: "assigned", assigned_at: now}`
   - Sends booking confirmation via current SMS provider (Plivo if cutover, else Twilio Voice fallback)
   - Confirmation copy: "Load {load_id} confirmed: pickup {pickup_at} at {pickup_addr}, rate ${rate}. Reply CONFIRM to lock."

2. **Failure detector** — scheduled task every 15 min:
```python
async def scan_assigned_unconfirmed():
    cutoff = now_utc() - timedelta(hours=2)
    candidates = db.loads.find({
        "dispatch_status": "assigned",
        "confirmed_at": None,
        "assigned_at": {"$lt": cutoff},
    })
    async for load in candidates:
        # Mark as ghosted, exclude this carrier from re-outreach
        await db.failure_events.insert_one({
            "load_id": load["_id"],
            "carrier_mc": load["assigned_carrier_mc"],
            "failure_mode": "carrier_ghost_2h",
            "detected_at": now_utc(),
        })
        # Re-outreach excluding ghoster
        await carrier_outreach.select_and_send(
            load["_id"],
            exclude_mcs=[load["assigned_carrier_mc"]]
        )
        # Outcome-feedback: penalize ghoster's reliability prediction
        await db.evaluator_outcomes.update_many(
            {"evaluator_class": "carrier", "scored_entity_id": load["assigned_carrier_mc"], "actual_outcome_observed_at": None},
            {"$set": {"actual_outcome_observed_at": now_utc(), "actual_params.reliability_signal": -0.5}},
        )
```

3. **CONFIRM reply handler:** updates `db.loads.confirmed_at = now`, status → `"confirmed"`. Outcome-feedback: update CarrierEvaluator outcome with `reliability_signal: +1.0`.

4. **Endpoint:** `POST /api/loads/{load_id}/assign_carrier` (manual override — operator can force-assign if needed).

5. **SLA target:** `carrier_assign` registered with `target_ms=10_000`.

6. **Health-check registration** — failure-detector cron registers `carrier_assign_failure_detector` module in `/api/health/system`.

**Smoke battery (6 tests, 2 real-data):**

1. **REAL-DATA:** Mocked accepted offer for real db.carriers MC → `auto_assign_carrier` updates db.loads correctly (assigned_carrier_mc resolves; dispatch_status flips; assigned_at timestamp set)
2. Booking confirmation SMS/voice sent with correct templated values (load_id, pickup_at, pickup_addr, rate)
3. Failure detector triggers exactly at 2h boundary (time-warp test: insert load with `assigned_at = now - 2h1min`)
4. Re-outreach excludes the unconfirmed carrier from new top-N selection (caller passes `exclude_mcs` to `select_and_send`)
5. CONFIRM reply flips `dispatch_status` to `"confirmed"`, `confirmed_at` set, outcome-feedback row updated with `reliability_signal: 1.0`
6. **REAL-DATA:** End-to-end real-roster outreach → mocked accept → `auto_assign_carrier` → confirm flow completes in <60s wall-clock; `db.evaluator_outcomes` row written

**Dependency graph:**
- **Depends on:** Stage 1d (offer state machine + db.carrier_offers)
- **Blocks:** 1f (dispatch packet needs `confirmed` status as trigger)

**Can ship in parallel during FMCSA + Plivo wait?** **YES (post-1d ship)** — uses real carrier data + mocked offers; no external blockers if 1d is built with Twilio Voice fallback.

**Outcome-feedback (per iter 145.1 Layer 5):**
- On `confirmed`: write CarrierEvaluator outcome with `reliability_signal: +1.0`
- On 2h-failure-detector fire: write CarrierEvaluator outcome with `reliability_signal: -0.5`

**Trust-gate inputs:**
1. **2h failure-detector threshold** — if 2h is too short (carriers need more time to confirm) or too long (ghosting goes undetected), self-healing proposes adjustment within ±30 min. **LOW-risk** auto-merge (threshold tuning).
2. **Re-outreach exclusion logic** — if the same carrier keeps getting re-selected after ghosting (because their composite score is still high), self-healing proposes a "ghosted-in-last-7-days demotion factor". **MED-risk** dev-DM (touches scoring logic indirectly via demotion factor wiring; would touch CarrierEvaluator).

---

## Stage 1f — Dispatch Packet Auto-Generation + Auto-Bid Integration Handoff

**Goal restatement:**
- **Original:** generate rate-con + BCA + insurance-cert PDFs, deliver to confirmed carrier; AND wire `auto_dispatch.try_auto_accept` (iter 141.2) → `carrier_outreach.select_and_send` (post-book).
- **Revised:** same goal; auto-bid integration depends on iter 141.2 (FMCSA-gated ~May 13). Pre-FMCSA, manual `try_auto_accept` invocation against synthetic load is the workaround for end-to-end smoke.

**North-star scoring:** **R=HIGH** (full dispatch packet generation + delivery without operator), **O=MED** (`db.dispatch_packets` + delivery telemetry), **S=MED** (template-render failures + email-bounce handling are patchable)

**What operator does (~15 min):**
1. Review rate-con + BCA PDF templates in `memory/handoff_iter_142_1_stage_1f.md` — fill legal-entity defaults + signature block + MC/USDOT cite (~10 min)
2. Send first synthetic load through end-to-end (auto-bid wins → outreach → assign → confirm → dispatch packet delivered) → operator inspects PDFs visually (~5 min)
3. Sign-off

**What dev does (~6h):**

1. **`backend/dispatch_packet.py`** — PDF templates (rate-con, BCA, insurance-cert request) using `reportlab` (already in use per `automations.py` per `capability_inventory.md`).

2. **`generate_packet(load_id)`** — fills templates with load + carrier + rate + dates → writes PDFs to local storage (`C:/CHL/data/dispatch_packets/{load_id}/...`; ~50 packets/day expected at v1, local disk acceptable per `iter_142_1_full.md` stage 1f Q3 default).

3. **Delivery:** email (Resend) with PDF attachments + SMS link (current SMS provider: Plivo if cutover, else Twilio Voice TTS fallback with URL spelled-out).

4. **`db.dispatch_packets` schema:**
```
{
  packet_id: str,
  load_id: str,
  rate_con_url: str (local file path or S3 URL),
  bca_url: str,
  insurance_cert_url: str,
  sent_at: datetime,
  carrier_confirmed_at: datetime | None,
  delivery_status: "sent" | "delivered" | "failed_render" | "failed_email" | "failed_sms",
  failure_reason: str | None,
}
```

5. **Trigger wiring:** when `db.loads.dispatch_status` flips to `"confirmed"` → call `generate_packet(load_id)`.

6. **Auto-bid handoff (iter 141.2 integration):** modify `auto_dispatch.try_auto_accept` (existing) → after successful book, call `carrier_outreach.select_and_send(load_id)`. Single line of integration code in the existing function; the heavy lifting is end-to-end smoke verification.

7. **Endpoint:** `GET /api/loads/{load_id}/dispatch_packet` (returns packet URLs).

8. **Outcome-feedback hook:** on packet delivered + carrier_confirmed → write `db.evaluator_outcomes` for **LoadEvaluator** (load successfully exited the dispatch funnel — feeds LoadEvaluator's outcome learning per iter 145.1 Layer 5).

9. **SLA target:** `dispatch_packet_render` registered with `target_ms=8_000`.

10. **Health-check registration** — `dispatch_packet_generator` module appears in `/api/health/system`.

**Smoke battery (5 tests, 1 real-data + FMCSA-gated):**

1. Rate-con PDF renders without crash, contains all required fields (load_id, carrier MC, rate, pickup/delivery dates, signature block)
2. BCA + insurance-cert PDFs render with correct entity (Continental Haul Logistics LLC) + MC info (MC-1817555)
3. Packet delivery: email with attachments lands in test mailbox; SMS link returns valid URL
4. `db.dispatch_packets` row created with all URLs populated; `delivery_status: "sent"`
5. **REAL-DATA (FMCSA-gated):** auto-bid wins synthetic load → outreach (real roster, mocked accept) → `auto_assign_carrier` → confirm → packet delivered → operator inspects PDFs visually + `db.evaluator_outcomes` row written for LoadEvaluator. **Pre-FMCSA workaround:** invoke `try_auto_accept` manually against synthetic load (skip the auto-bid trigger; the rest of the flow exercises identically).

**Dependency graph:**
- **Depends on:** Stage 1e (assignment + confirmation flow) + iter 141.2 auto-bid live (FMCSA-gated ~May 13+) for end-to-end smoke #5; smoke #1-4 work standalone
- **Blocks:** nothing (terminal stage in dispatch flow; iter 142.1 close ceremony gates on this stage shipping)

**Can ship in parallel during FMCSA + Plivo wait?** **MOSTLY YES** — packet rendering + delivery + `db.dispatch_packets` all build/smoke pre-FMCSA (smoke #1-4 cover this). End-to-end smoke #5 requires iter 141.2 auto-bid live → FMCSA-gated. Manual `try_auto_accept` invocation is the pre-FMCSA workaround.

**Outcome-feedback (per iter 145.1 Layer 5):**
- On packet delivered + carrier_confirmed → write LoadEvaluator outcome (load went out the door successfully; `actual_composite` reflects realized margin once 143.x POD/invoicing ships)
- On packet failure (template render crash, email bounce, SMS failure) → write outcome with `score_delta` indicating dispatch-flow degradation; trigger anomaly dispatcher per iter 145.1 Layer 1 (Detection)

**Trust-gate inputs:**
1. **PDF template rendering edge cases** — if certain carrier-name formats (apostrophes, non-ASCII) or lane-name formats trigger template crashes, self-healing proposes defensive escapes / encoding fixes. **LOW-risk auto-merge** (defensive null/empty checks in non-auth/billing file).
2. **Email-bounce handling** — if specific carrier email patterns bounce, self-healing proposes adding to a known-bad-email list + flagging carrier for manual contact-info refresh. **LOW-risk** (data-only addition; no logic change).

---

## Aggregate Stats (post-refresh)

| Stage | Operator hands-on | Dev hours | Smoke tests | Real-data tests | North-star (R/O/S) | Parallel-shippable during FMCSA+Plivo wait? |
|---|---|---|---|---|---|---|
| 1c | 5 min | 5h | 6 | 3 | H/M/H | **YES** (no external blocker) |
| 1d | 10 min | 6.5h | 8 | 2 | H/M/M | **PARTIAL** (Twilio Voice fallback path is v1; Plivo cutover later) |
| 1e | 5 min | 4h | 6 | 2 | H/H/M | **YES** (post-1d ship) |
| 1f | 15 min | 6h | 5 | 1 (FMCSA-gated for end-to-end) | H/M/M | **MOSTLY YES** (manual `try_auto_accept` pre-FMCSA covers smoke #1-4) |
| **Totals** | **~35 min** | **~21.5h** | **25** | **8** | — | — |

---

## Sequencing Recommendation

Given the FMCSA + Plivo wait + autonomous-continue mode + dev's 5-resource parallel pattern (per `current_status.md` Resources Available section):

1. **Today/tomorrow (zero blockers):** ship **stage 1c** — load-bearing for everything downstream; immediate value (first dispatch will need scoring); zero external blockers.
2. **Next:** ship **stage 1d outreach core + Twilio Voice fallback** — zero blockers (Twilio Voice is iter 141.2 baseline); primary outreach channel until Plivo cutover. DAT load-board posting may be FMCSA-gated; can stub at v1 if needed.
3. **After 1d:** ship **stage 1e assignment + failure detector** — no blockers post-1d; uses real carrier data + mocked offers.
4. **Pre-FMCSA:** ship **stage 1f packet rendering + delivery** — smoke #1-4 work; smoke #5 needs FMCSA + iter 141.2 (use manual `try_auto_accept` workaround).
5. **Post-FMCSA + iter 141.2 ship:** end-to-end smoke #5 + iter 142.1 close ceremony (cumulative smoke battery from Phases 1+2+4+5 + stages 1c-1f = ~50+ tests; real-data assertions across all stages).

---

## Outcome-Feedback Integration Summary (per iter 145.1 Layer 5)

| Stage | Outcome write hook | What it teaches the evaluator |
|---|---|---|
| 1c | On every `evaluate()` call | Predicted composite + 4-param breakdown stored for later actual-vs-predicted comparison |
| 1d | On offer accepted/declined/no-response-30min | Actual carrier acceptance signal per lane/equipment refines reliability prediction |
| 1e | On `confirmed` / on 2h-ghost detector fire | Realized "accepts + confirms" vs "ghosts" updates CarrierEvaluator reliability |
| 1f | On packet delivered / on render failure | LoadEvaluator outcome reflects dispatch-flow success/failure |

All four stages integrate with `db.evaluator_outcomes` collection (schema per `phase_6_self_healing_roadmap.md` Layer 5). Re-tuning cron (iter 145.x) reads these outcomes weekly + proposes weight changes through trust-gate framework.

---

## Trust-Gate Surface Summary (patchable failure modes per stage, for iter 145.1 self-healing)

| Stage | Failure mode | Risk class | Why it's a good agent-fixable surface |
|---|---|---|---|
| 1c | Composite-weight tuning | LOW | Threshold tuning within ±10%; clear MAE-driven signal; reversible via git revert |
| 1c | Cold-start default tuning | LOW | Defensive default in evaluator helper; no business-logic risk |
| 1d | Outreach-copy tuning (non-compliance text) | LOW | Message text in non-auth/billing file; clear accept-rate signal |
| 1d | Outreach-copy tuning (opt-in language) | MED | Touches compliance; dev-DM review prevents regulatory regression |
| 1d | Channel-selection weight tuning | LOW | Threshold tuning; clear signal from per-channel accept rates |
| 1e | 2h failure-detector threshold | LOW | Threshold tuning ±30 min; clear ghosting-rate signal |
| 1e | Ghoster-demotion factor wiring | MED | Touches scoring logic indirectly; dev-DM review prevents over-penalization |
| 1f | PDF template defensive escapes | LOW | Defensive null/empty checks in non-auth/billing file |
| 1f | Bad-email-pattern list addition | LOW | Data-only addition; no logic change |

**Total trust-gate inputs across 1c-1f: 9 (7 LOW + 2 MED).** Net: stages 1c-1f provide a rich surface for the iter 145.1 self-healing pipeline to exercise on real production failure modes once dispatch flow is live.

---

## Cross-references

- `chl-memory/agenda/iter_142_1_full.md` — current authoritative agenda (stages 1c-1f bodies); this refresh is a **tightening overlay**, not a replacement. When operator authorizes 1c-1f kickoff, **promote relevant sections from this refresh into the agenda body in-place**.
- `chl-memory/research/phase_6_self_healing_roadmap.md` (commit `7d658cc`) — Layer 5 outcome-feedback schema (`db.evaluator_outcomes`) this refresh hooks into; trust-gate matrix from §3 referenced for failure-mode classification per stage.
- `chl-memory/capability_inventory.md` — existing primitives (FractalEvaluator base, `automations.py` reportlab PDF generation, `lane_evaluator.py` + `load_evaluator.py` patterns).
- `chl-memory/FRACTAL_DECISION_FRAMEWORK.md` — 4-parameter evaluator architecture; CarrierEvaluator (1c) mirrors this verbatim at meso scale.
- CHL backend `lane_evaluator.py` + `load_evaluator.py` — concrete FractalEvaluator implementations to mirror in `carrier_evaluator.py`.
- `chl-memory/research/iter_141_3_agenda_draft.md` — Plivo migration; informs 1d Twilio-Voice fallback path until 141.3 cutover ships.
- `chl-memory/research/iter_142_1_carrier_vetting_checklist.md` — vetting workflow; informs 1c filter on `vetting_status in ("vetted", "active", "roster_active")`.
- `chl-memory/research/iter_142_1_pre_flight_brief.md` — pre-kickoff gotchas (dev-authored).
- `chl-memory/research/fmcsa_li_insurance_parsing_research.md` Tier 1.5 — SAFER cargo-classifications populating `equipment_types[]` (used by 1c equipment filter).

---

**Status:** ✅ REFRESH COMPLETE. Operator/dev review before stage 1c kickoff. Stages 1c-1f remain the dispatch-milestone gate per `iter_142_1_full.md` audit (commit `097e580`); this refresh tightens the executable spec given today's reality. Recommended next dev action: **ship stage 1c** (zero blockers, load-bearing for downstream).

**Source:** Drafted by @pm-lead 2026-05-07 EOD-3 per @dev-engineer task brief. North-star pillars: operator's "autonomous + self-healing + learning + AI-embedded" directive. Forward-looking; specific code surfaces will adjust at dev-time per actual FractalEvaluator base class signatures (this refresh approximates from capability_inventory).
