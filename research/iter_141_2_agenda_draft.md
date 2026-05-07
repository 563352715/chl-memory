# Iter 141.2 Agenda Draft (PM Claude)

> **Source:** PM Claude (`@pm-lead`) via AgentDM messages on 2026-05-06. Three-part draft transmitted across DMs `677ce10d-4721-4412-a56e-f8cb9fe840f7` (Part 1/3), `8b0ef98c-7e72-404f-b256-49a9cf19f671` (Part 2/3), and Part 3/3 (pending).
> **Status:** ✅ COMPLETE — all 3 parts received via DMs `677ce10d`, `8b0ef98c`, `2e70b1fe`. Stage 1D + 1F summaries also received (DMs `6642a6e8`, `615d7257`); full 257-line stage_1d spec available on request.
> **Not yet committed to `agenda/current_iter.md`.** Operator reviews this draft, then either commits as-is or revises before iter 141.2 opens.

---

## Iter 141.2 — DAT Live Integration & Auto-Bidding

**Goal:** Wire live DAT API + autonomous bidding. Transform CHL from historical-only lane evaluation to live-market-aware with autonomous load acquisition.

**Prerequisites:**
- FMCSA authority MC-1817555 finalized (~2026-05-13)
- DAT service account (org-level, separate from FMCSA — provision in parallel)
- Connexion license activated

**Stages (2 total):**
- **1d:** DAT live wiring — poll every 5–15 min business hours, populate `db.dat_opportunities`, flip `opportunities_24h` from stub-zero to real counts
- **1f:** Auto-bid on top lanes — `composite_score >= 0.75`, max 5–10 bids per cycle, 24h per-lane cooldown

---

## In Scope

### Stage 1d — DAT Live Wiring
- New `backend/dat_client.py` — auth, search, parse, exponential backoff with 0–20% jitter
- New `db.dat_opportunities` collection (schema per `research/dat_api_notes.md`)
- New `backend/dat_polling_cron.py` — 5–15 min business hours, 30–60 min off-hours, throttle-gated
- Off-hours config in `business_settings.dat_poll_schedule` (operator-tunable, not hardcoded)
- `LaneEvaluator` integration — replace stub query with real `db.dat_opportunities` lookup
- Flip `dat_status` field in `/api/lanes/top/{N}` response from `"deferred_to_iter_141_2"` to `"live"`

### Stage 1f — Auto-Bid on Top Lanes
- New `backend/auto_bid.py` module
- Bid thresholds in `business_settings` (default `composite_score >= 0.75`)
- Max bids per cycle (5–10) + per-lane 24h cooldown enforcement
- Throttle integration: poll skip if `!= GREEN`; bid fire only if `== GREEN`

### Cross-cutting
- SLA instrumentation: `@track_sla("dat_poll", 900_000)` + `@track_sla("auto_bid", 300_000)`
- Health check self-registration for both modules
- Observability: API call count, success/error ratio, loads retrieved per poll, bids sent per cycle

---

## Out of Scope (queued for later iters)
- Other load boards (Truckstop, 123Loadboard) — separate iter, Phase 3 territory
- `MarketEvaluator` (macro layer) — Phase 8, iter 146.x+
- Multi-carrier outreach — Phase 3
- Load posting (reverse direction) — different feature
- Threshold auto-tuning (operator currently tunes manually via `business_settings`)
- Cold-start lanes (lanes with zero historical data — separate evaluation pathway)

---

## Standing Rules

- DAT credentials in env vars only — never hardcoded, never echoed in chat/logs
- Rate limit safety: stay under 60/hr; abort polling cycle if approaching the cap
- Backoff: exponential with 0–20% jitter, per-endpoint tracking (search vs post)
- Graceful degradation: `opportunities_24h = 0` when DAT is down; `LaneEvaluator` falls back to historical
- Off-hours config in `business_settings.dat_poll_schedule`, not hardcoded
- Bid thresholds conservative — `composite_score >= 0.75`, max 5–10 bids per cycle
- Throttle gates enforced on both polling and bidding
- 2 commits total for the iter (1d + 1f), one per stage
- No `--no-verify` on any commit
- Regression check `/api/ops/briefing` after each stage

---

## References
- `chl-memory/research/dat_api_notes.md` — DAT API research notes (PM Claude's review of Claude Code's feedback incorporated: Connexion parallel provisioning, off-hours config, dat_status flip)
- `chl-memory/CHL_STRATEGIC_PLAYBOOK.md` — Phase 2 alignment
- `chl-memory/FRACTAL_DECISION_FRAMEWORK.md` — composite scoring rationale
- `chl-memory/THROTTLE_PLAYBOOK.md` — state machine semantics

---

## Why Critical

> "Load Discovery & Evaluation enables autonomous load intake. System scans load boards, evaluates profitability/risk, and surfaces opportunities without operator manual board-watching."

This is the moment CHL stops requiring the operator to be on DAT manually.

## Operational Transform

| Phase | Daily volume | Operator role |
|---|---|---|
| **Before iter 141.2** | 5–10 loads/day | Manual DAT scanning + booking |
| **After stage 1d** | 20–30 loads/day | Operator reviews top-20 dashboard, books selected |
| **After stage 1f** | 50–100 loads/day | System bids autonomously; operator approves wins |

## Business Impact
- Current state: $0 (pre-revenue)
- Milestone target: $1,200+ / 2-week operator pay
- DAT integration = the unlock (can't scale without live market data)
- Auto-bid = force multiplier (operator can't manually bid 50+/day; this is what gets us past the human-bandwidth ceiling)

---

## Risk Mitigation
- Graceful DAT-down → no operational halt (LaneEvaluator falls back to historical)
- Conservative bids (`0.75` composite threshold, max 5–10/cycle) → no overcommitment
- Throttle gates → no cascade on volume spike
- 24h per-lane cooldown → no bid spam

## Operator Approval Checklist
- [ ] Review `agenda/stages/stage_1d.md` + `agenda/stages/stage_1f.md` (drafts pending PM Claude full-spec send)
- [ ] Review scope; confirm DAT creds will be ready before iter start
- [ ] Kick off DAT service account + Connexion provisioning **in parallel** with FMCSA (saves ~1 week)
- [ ] Commit final agenda + stage docs to `chl-memory/agenda/`
- [ ] Verify FMCSA MC-1817555 finalized (gate for iter start)
- [ ] Dispatch Claude Code to execute

## Future Iters (queued)
- **141.3:** Truckstop, 123Loadboard (additional load board sources)
- **142.1:** Phase 3 — Carrier Network (multi-carrier outreach)
- **143.1:** Bid refinement (threshold tuning, ML signals)

— END ITER 141.2 AGENDA DRAFT —

---

## Stage 1D Summary (PM Claude high-level — full 257-line spec on request)

| Topic | Detail |
|---|---|
| Schema | `db.dat_opportunities` with 17 fields; `dat_load_id` unique index, `lane_key` for join, `posted_at`/`expires_at` for freshness, `raw_response` for debug |
| Auth flow | Service account + Connexion → email `techsupportteamleads@dat.com` for activation → ENV vars `DAT_SERVICE_USERNAME` / `DAT_SERVICE_PASSWORD` → token cache with TTL refresh |
| Polling | Business hours (8am–6pm CT): 5–15 min configurable. Off-hours: 30–60 min. Config in `business_settings.dat_poll_schedule = {business_start, business_end, business_interval, offhours_interval}`. Throttle gate: skip if `!= GREEN`. Rate-limit guard: track calls/hr; abort cycle if `>50/hr` |
| Backoff | Exponential 2 → 30 min with 0–20% jitter, per-endpoint, circuit-break on 3+ 5xx |
| `dat_status` flip | In `/api/lanes/top/{N}` response, change `"deferred_to_iter_141_2"` → `"live"` once DAT is connected |
| Smoke battery (6 tests) | Poll success, DAT-down graceful fallback, rate-limit cycle abort, equipment-type mapping correctness, auth refresh, observability (SLA + metrics emit) |

## Stage 1F Summary (PM Claude high-level — UPDATED 2026-05-06 with threshold-tuner backend)

**Renamed:** "Auto-bid on top-tier lanes" → **"Auto-bid + threshold management UI backend"**

| Topic | Detail |
|---|---|
| Objective | Autonomous bid generation for high-composite-score lanes + threshold-tuning UI backend |
| Deliverables | `backend/auto_bid.py` with `bid_lanes()` function; `business_settings.bid_thresholds` (default `composite_score >= 0.75`); `max_bids_per_cycle` (5–10); `lane_cooldown_hours` (24); integration with `carrier_outreach.py` (reuse existing `draft_bid` logic); throttle gate (fire only if `== GREEN`); per-lane tracking in `db.bid_history` (`lane_key`, `bid_at`, `bid_id`, `status`); **3 new endpoints supporting threshold-tuner UI (see below)** |
| Logic | Fetch `/api/lanes/top/50` (internal call) → filter `composite_score >= threshold AND no bid in last 24h` → sort desc → take top `max_bids_per_cycle` → for each: `draft_bid` via `carrier_outreach`, send, log to `db.bid_history` |
| SLA | `@track_sla("auto_bid", 300_000)` = 5-min target |
| Smoke battery (9 tests) | Bid generation, threshold enforcement, cooldown logic, throttle gate, max-bids cap, carrier_outreach integration, **GET /api/settings/thresholds, POST /api/settings/thresholds (with valid bounds), GET /api/lanes/preview (query params)** |
| Safety | Conservative start (0.75 threshold, max 5–10/cycle); operator reviews first 48 hours; ENV kill-switch `AUTO_BID_ENABLED=false` |

### Threshold-tuner backend endpoints (added 2026-05-06 per PM Claude DM `ec1c18e8`)

#### 1. `GET /api/settings/thresholds` — broker auth
**Response:**
```json
{
  "lane_thresholds": {
    "profitability_min": 0.18,
    "reliability_min": 0.85,
    "risk_max": 0.08,
    "capacity_min": 20
  },
  "defaults": {
    "profitability_min": 0.18,
    "reliability_min": 0.85,
    "risk_max": 0.08,
    "capacity_min": 20
  }
}
```
**Logic:** Read `business_settings.lane_thresholds` (seeded in stage 1b). Defaults are hard-coded constants.

#### 2. `POST /api/settings/thresholds` — broker auth
**Request:**
```json
{
  "lane_thresholds": {
    "profitability_min": 0.20,
    "reliability_min": 0.85,
    "risk_max": 0.08,
    "capacity_min": 20
  }
}
```
**Response:**
```json
{
  "status": "updated",
  "lanes_passing": 15
}
```
**Logic:** Update `business_settings.lane_thresholds` with new values. Run the preview logic (#3) to get `lanes_passing` count. **Does NOT recompute `db.lane_scores` immediately** — next `lane_scoring_cron` tick (every 6h) picks up new thresholds.

#### 3. `GET /api/lanes/preview?profitability_min=…&reliability_min=…&risk_max=…&capacity_min=…` — broker auth
**Response:**
```json
{
  "lanes_passing": 15,
  "change_from_current": -3,
  "top_lanes": [
    {"lane": "Chicago, IL → Dallas, TX", "composite_score": 0.847},
    {"lane": "Atlanta, GA → Memphis, TN", "composite_score": 0.812}
  ]
}
```
**Logic:** Query `db.lane_scores` with hypothetical thresholds (from query params). Filter where `passes_gates=True` with NEW thresholds. Return count + delta from current `business_settings` count + top 5 lanes. Same composite formula as `/api/lanes/top/{N}`.

**Timing note for the threshold-tuner UI:** adjusting thresholds via POST does NOT recompute `db.lane_scores` immediately. `lane_scoring_cron` picks up the new thresholds on its next 6h tick. The UI should display: "Changes apply to next lane scoring run (within 6 hours)."

## Resolved Questions (PM Claude DM `c893ff46`)

### 1. Cooldown enforcement → `db.bid_history`
Schema: `{lane_key, bid_at, bid_id, bid_amount, status, carrier_id}`. Check before bidding:
```python
await db.bid_history.find_one({"lane_key": lk, "bid_at": {"$gte": now - timedelta(hours=24)}})
```
Cleaner than polluting `db.lane_scores` with bid state.

### 2. Bid amount logic → hybrid (margin floor + lane profitability)
```python
margin_floor = business_settings.bid_thresholds.margin_min   # e.g. 0.18
lane_margin = lane_data["profitability"]                      # e.g. 0.22
bid_margin = max(margin_floor, lane_margin * 0.9)             # 90% of lane's proven margin
bid_amount = estimated_cost * (1 + bid_margin)
```

### 3. Smoke test data → synthetic DAT fixture
```python
MOCK_DAT_RESPONSE = {
    "loads": [
        {"id": "DAT123", "origin": "Chicago, IL", "dest": "Dallas, TX", "rate": 2500, ...},
        # 5 mock loads covering different equipment types
    ]
}
```
Test against the mock fixture, NOT live DAT API. Preserves rate limit and gives deterministic test outcomes.

### 4. Rollback plan
- Env flag: `AUTO_BID_ENABLED=false` is the global kill-switch (operator-only)
- Operator dashboard shows `db.bid_history` so operator can spot bad bids
- Manual cancellation: `carrier_outreach.cancel_bid(bid_id)`
- First 48 hours of stage 1f deployment: operator-approval-required gate before any bid actually sends

### 5. Stage ordering — **1f REQUIRES 1d shipped first** (sequential)
Auto-bid logic depends on `opportunities_24h` being populated by the live DAT poll. No way to test or run 1f meaningfully before 1d lands. Iter 141.2 must execute 1d → 1f in order.

---

**All open items resolved as of DM `c893ff46`. Iter 141.2 agenda + stage summaries are now full-spec. Operator can review and commit to `chl-memory/agenda/` whenever ready.**

