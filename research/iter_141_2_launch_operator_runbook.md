# Iter 141.2 Launch Operator Runbook (post-FMCSA finalization)

> **Status:** REFINED by PM Claude (@pm-lead) 2026-05-07 from skeleton drafted overnight by @dev-engineer 2026-05-06.
> **Trigger:** Day operator's FMCSA authority MC-1817555 finalizes (~2026-05-13).
> **Goal:** Get from "FMCSA approved" → "iter 141.2 in flight" without the operator having to remember scattered TODOs.

---

## Pre-Flight Checklist (operator does, ~20 minutes hands-on + 3-5 days async wait)

### 1. Confirm FMCSA authority is active
- [ ] Log into https://portal.fmcsa.dot.gov
- [ ] Verify MC-1817555 status = "Active"
- [ ] Note authority date (used for some carrier vetting integrations)
- **If not "Active":** See "FMCSA Delay Decision Tree" below before proceeding

### 2. Launch PARALLEL async tasks (both gated on FMCSA Active, don't block on each other)

**Task A: Provision DAT service account + Connexion license** (3-5 business day turnaround)
- [ ] Email `techsupportteamleads@dat.com`:
  - Subject: "DAT Service Account + API Connexion License Activation Request"
  - Body: Include MC-1817555, Continental Haul Logistics LLC, and explicit request for Load Board + Rating + Capacity REST API access
- [ ] Track: typical activation is 3–5 business days
- [ ] **PARK HERE** until DAT activation email arrives (continue Task B in parallel)

**Task B: Twilio verification - SMS + Voice paths** (5 minutes)
- [ ] Verify Twilio creds in `.env` (uncomment + populate from vault):
  ```
  TWILIO_ACCOUNT_SID=AC<...>
  TWILIO_AUTH_TOKEN=<...>
  TWILIO_PHONE_NUMBER=+1<...>
  TWILIO_MESSAGING_SERVICE_SID=MG<...>
  OWNER_SMS_NUMBER=+18165609180   # already saved (operator personal — replaced 2026-05-08; new business: TF +1-866-490-6433 / local +1-417-219-3856)
  ```
- [ ] Restart CHL-Backend (admin shell): `Restart-Service CHL-Backend`
- [ ] **Empirical SMS smoke test (NOT just env-var presence check):**
  ```powershell
  & "C:\CHL\.venv-local\Scripts\python.exe" -c "from text_operator import text_operator; import asyncio; print(asyncio.run(text_operator('iter 141.2 pre-flight SMS smoke')))"
  ```
- [ ] **Check SMS result:**
  - **✅ SMS delivered to +18165609180** (status="sent", no error_code) → SMS path verified, Task B complete
  - **❌ Error 30032 (TFV)** OR **❌ Error 30034 (10DLC)** → SMS path DEGRADED, proceed to fallback:
    1. Use voice-only alerts: verify `voice_operator.py` works (empirical voice smoke; your phone should ring)
    2. Flag in `chl-memory/progress/current_status.md`: "SMS path degraded (Twilio error 30032/30034 — 10DLC campaign pending re-submit + TFV pending portal submit). Iter 141.2 shipping on voice-only alerts. SMS un-degrades async when compliance lands (1-3 days)."
    3. Operator async tasks (run in parallel with iter 141.2 main work):
       - (a) Create continentalhaul.com/sms-opt-in page (~30 min frontend work; spec at `chl-memory/research/continentalhaul_sms_opt_in_page_spec.md` once PM drafts it)
       - (b) Re-submit 10DLC campaign via Twilio portal with revised message_flow citing the new CTA URL
       - (c) Submit TFV via Twilio portal as backup SMS path (uses `chl-memory/research/twilio_tfv_submission_text.md` once drafted)
    4. ✅ Task B complete on voice-only path
- [ ] ✅ Twilio verified — SMS working OR voice-only fallback confirmed

### 3. Once DAT activation email arrives (resumes from Task A park)
- [ ] Store credentials in vault under "DAT — Service Account" + "DAT — API Connexion License"
- [ ] Add to `C:\CHL\backend\.env`:
  ```
  DAT_SERVICE_USERNAME=<from DAT activation email>
  DAT_SERVICE_PASSWORD=<from DAT activation email>
  DAT_BASE_URL=https://freight.api.dat.com
  ```
- [ ] Run snapshot ceremony: `& "C:\CHL\scripts\snapshot_to_d.ps1" -Note "iter 141.2 prep — DAT creds populated"`
- [ ] ✅ DAT verified — both async tasks complete

### 4. Iter 141.2 agenda commit
- [ ] Review `chl-memory/research/iter_141_2_agenda_draft.md` (PM Claude's full draft)
- [ ] Move + rename to `chl-memory/agenda/current_iter.md` and `chl-memory/agenda/stages/stage_1d.md` + `stages/stage_1f.md`
- [ ] `cd chl-memory && git add agenda/ && git commit -m "iter 141.2 agenda + stages 1d+1f committed for execution"`
- [ ] `git push origin main`

### 5. Dispatch Claude Code
- [ ] Open Claude Code in `C:\CHL\`
- [ ] Verify Claude Code session loads the new agenda from chl-memory
- [ ] First instruction: "Execute iter 141.2 stage 1d per agenda. Pre-flight + action steps + smoke."

---

## FMCSA Delay Decision Tree

**Scenario:** It's May 13+ and MC-1817555 status is NOT "Active".

### Status: "Pending Review"
**Meaning:** FMCSA is processing the application; typical causes: name check delays, insurance verification pending, or random quality assurance review.

**Actions:**
1. **Call FMCSA New Entrant Hotline:** 1-800-832-5660 (M-F 8am-8pm ET)
   - Ask for status update on MC-1817555
   - Request estimated activation date
   - Document call: case number + agent name + callback number
2. **Escalation contacts if >10 business days past expected finalization:**
   - FMCSA Office of Registration & Safety Information: registration@dot.gov
   - Congressional liaison (if operator is constituent): contact via local House/Senate office
3. **Alt-path (buys 2-4 weeks):**
   - Partner with existing broker under their MC for first carrier outreach
   - Requires broker agreement + revenue share negotiation
   - Defer iter 141.2 stage 1f (auto-bid) until CHL's MC finalizes
   - Stage 1d (DAT polling) can still ship — loads visible but can't legally broker yet

### Status: "Suspended" or "Revoked"
**Meaning:** Application rejected or authority suspended (extremely rare for new applications).

**Actions:**
1. **Immediate operator call to FMCSA:** 1-800-832-5660 — find out why
2. **Likely causes:**
   - Insurance lapse (re-file with active policy)
   - Incomplete application (missing BOC-3 or other docs)
   - Name conflict with existing carrier
3. **Timeline impact:** 30-90 day delay depending on issue
4. **Alt-path:** Partner with existing broker (as above) OR pivot to carrier-side operations (haul under another's MC until CHL's finalizes)

### Status: "Inactive - Not Authorized"
**Meaning:** MC number assigned but authority not yet granted (procedural hold).

**Actions:**
1. Check FMCSA portal for required filings: likely missing OP-1 insurance filing or BOC-3 designation
2. Upload missing docs via portal, call hotline to confirm receipt
3. Expect 3-5 business day processing after upload

### Decision Point: Wait or Pivot?
**Wait if:**
- Status = "Pending Review" AND <10 business days elapsed
- FMCSA agent gave specific activation ETA

**Pivot to alt-path if:**
- Status = "Pending Review" AND >15 business days elapsed with no ETA
- Status = "Suspended" / "Revoked" / "Inactive" with multi-week fix timeline
- Operator wants to start generating revenue NOW (partner broker route)

---

## Stage 1d → 1f Transition Gate

**Question:** Should there be a buffer between stage 1d shipping (DAT live) and stage 1f shipping (auto-bid)?

**Recommendation: CONDITIONAL — same-day if all checks GREEN, 24h buffer if any YELLOW**

**Rationale:**
- Stage 1d wires DAT polling cron (every 5-15 min business hours)
- Validation gates catch 401/429/500 spikes, rate limit thrashing, or stale data issues
- Pre-revenue solo operation → minimize dead time; impose 24h buffer only when checks warrant it

**Operator checklist for 1d → 1f gate:**
1. [ ] Stage 1d ships, DAT polling live
2. [ ] **Immediate checks (within 1-2 hours):**
   - Check `/api/health/system` — `dat_polling_cron` module should be `healthy: true`
   - Check `db.dat_opportunities` count: should be >10 after 1-2h of polling
   - Check `/api/sla/operation/dat_poll` — p95 < 900ms target, no misses in first 5-10 polls
3. [ ] **Decision point:**
   - **All 3 checks GREEN** → dispatch Claude Code for stage 1f same-day (speed wins)
   - **Any check YELLOW/RED** → impose 24h observation window, debug before 1f
   - Examples of YELLOW: `db.dat_opportunities` count = 0-5 (too low), p95 = 1200ms (slow but not broken), 1-2 auth refresh failures but subsequent polls succeed
4. [ ] If 24h imposed: re-run all 3 checks after 24h, target >50 opportunities accumulated
5. [ ] Once checks PASS → dispatch stage 1f

**Why conditional wins:** Avoids default-slow tempo at pre-revenue scale while preserving safety when data quality is suspect.

---

## Day-1 Stage 1d (DAT live wiring)

**What Claude Code will do (per agenda):**

1. Pre-flight checks — verify DAT creds in env, verify `db.lane_scores` populated, verify throttle GREEN
2. Build `backend/dat_client.py` — auth + search + parse + backoff
3. Build `backend/dat_polling_cron.py` — 5–15 min business hours, throttle-gated, @track_sla("dat_poll", 900_000)
4. Schema: `db.dat_opportunities` (17 fields per research notes)
5. `LaneEvaluator` integration — replace stub with real query
6. Flip `dat_status` field in `/api/lanes/top/{N}` from `"deferred_to_iter_141_2"` → `"live"`
7. Smoke battery (6 tests, mocked DAT fixture):
   1. Poll success
   2. DAT-down graceful
   3. Rate-limit cycle abort
   4. Equipment mapping
   5. Auth refresh
   6. Observability emit
8. Commit + handoff doc + close stage 1d

**What operator does:**
- Stay reachable for ~15 min if Claude Code surfaces a question
- Watch first live DAT poll after stage 1d ships (verify `db.dat_opportunities` populating)
- If `opportunities_24h > 0` showing in `/api/lanes/top/20` response, stage 1d is end-to-end live

---

## Day-1 Stage 1f (Auto-bid + threshold-tuner backend)

**What Claude Code will do:**

1. Build `backend/auto_bid.py` with `bid_lanes()` function
2. Build `db.bid_history` schema + cooldown enforcement
3. Add `business_settings.bid_thresholds` (default `composite_score >= 0.75`)
4. Build 3 threshold-tuner endpoints (`GET /api/settings/thresholds`, `POST /api/settings/thresholds`, `GET /api/lanes/preview`)
5. Throttle gate: bid only if state == GREEN
6. SLA: `@track_sla("auto_bid", 300_000)`
7. Smoke battery (9 tests):
   - 6 auto-bid tests (gen, threshold, cooldown, throttle gate, max cap, carrier_outreach integration)
   - 3 threshold-tuner tests (GET, POST with bounds, lanes/preview with query params)
8. Commit + handoff doc + close stage 1f
9. **Operator gate:** first 48h after stage 1f ships, `AUTO_BID_ENABLED=false` so bids draft but don't send. Operator manually approves first ~5 bids via `/bids` dashboard, then sets `AUTO_BID_ENABLED=true`.

**What operator does:**
- Merge `iter_141_2_scaffolding/` files into `frontend/src/views/` (per `chl-memory/research/emergent_dashboard_review.md` recommended sequence)
- Apply App.js patch (operator-approval-per-edit; ~8 lines)
- Restart CHL-Frontend service
- Visual check: `http://localhost:3000/bids` and `http://localhost:3000/settings/thresholds` render
- **Execute 48h manual approval window** (detailed below)
- After 48h smooth running: set `AUTO_BID_ENABLED=true` in `.env`, restart backend

### 48h Manual Approval Window — Operator Checklist

**Goal:** Verify auto-bid is selecting good lanes before enabling autonomous send.

**Access dashboard:**
1. Navigate to `http://localhost:3000/bids`
2. Dashboard shows "Pending Bids" table with columns: Lane, Score, Bid $, Margin %, Expires In, Actions

**For EACH pending bid (first 5 minimum):**

**Step 1: Review composite score breakdown**
- Dashboard should show composite_score (e.g., 0.847) AND breakdown: `(profitability × reliability) / max(risk, 0.01)`
- Example: Profitability 0.22, Reliability 0.91, Risk 0.04 → Composite 0.847 ✅
- Check if score crosses 0.75 for RIGHT reasons (balanced components, not inflated by risk floor)
- **If breakdown NOT showing:** File bug report — stage 1f UI requirement (CRITICAL — blocks operator approval flow)

**Step 2: Sanity-check bid amount vs margin**
- Bid should be `max(margin_floor, lane_margin * 0.9)`
- Red flag: Bid way above historical margin (bad data/formula bug)

**Step 3: Check expires_in countdown**
- Green >30min, Yellow 10-30min, Red <10min
- If <10min → REJECT (don't approve under pressure)

**Step 4: Review load details**
- Origin, destination, distance, equipment
- Sanity check: within CHL's operating region?

**Step 5: Decision — Approve or Reject**

**APPROVE if:**
- ✅ Composite breakdown balanced
- ✅ Bid amount reasonable vs margin
- ✅ Lane within operating region
- ✅ Expires_in >10 minutes
- ✅ Gut check passes

**REJECT if:**
- ❌ Composite shows red flags (inflated by risk floor)
- ❌ Bid doesn't match margin calculation
- ❌ Outside operating region
- ❌ Expires_in <10min (rushed)
- ❌ Gut check fails

**Click flow:**
- APPROVE: Green button → Confirmation modal → "Confirm Send" → Bid sends, moves to History
- REJECT: Red button → Reason modal → Optional reason → "Reject" → Bid cancelled

**After approving first 5:**
- Monitor win rate: 3+ of 5 WON = good ✅
- If 0-1 WON: tune thresholds before enabling AUTO_BID_ENABLED=true
- After 48h + satisfactory win rate: Set AUTO_BID_ENABLED=true, restart CHL-Backend

---

## Iter 141.2 Close Ceremony

(Standard CHL iter close — same protocol as iter 141.1)

- [ ] Verify smoke totals across stages 1d + 1f
- [ ] Write `memory/handoff_iter_141_2_close.md` (Claude Code does this)
- [ ] Run `scripts/backup_to_d.ps1 -IterId "iter_141_2_close"` (full ceremony — mongo export + .env backup + manifest)
- [ ] Update `chl-memory/progress/current_status.md` to reflect iter close + bridge cumulative tally
- [ ] Decide on iter 141.3 (if needed) or jump to iter 142.1 Phase 3 Carrier Network

---

## Common Pitfalls + Mitigations

1. **"DAT 401s on first poll"** → Check base URL from DAT email, verify no trailing spaces in .env
2. **"Lane composite_scores all 0.0"** → lane_scoring_cron runs every 6h, wait or manual trigger
3. **"/bids returns empty"** → AUTO_BID_ENABLED=false is intended first-48h behavior
4. **"Threshold tuner shows 'lanes_passing: 0'"** → Thresholds too aggressive, move sliders back
5. **"Auto-bid win rate <20%"** → Tune bid formula (reduce 0.9 → 0.85) OR raise profitability threshold
6. **"DAT rate limit 429"** → Polling too aggressive, reduce to 10-15min or cap lanes per tick
7. **"Composite breakdown not showing in /bids"** → CRITICAL bug, add profitability/reliability/risk columns (blocks 48h manual approval flow)
8. **"/api/settings/thresholds returns 404"** → Route not registered in server.py, add + restart
9. **"Auto-bid fires on YELLOW throttle"** → Missing throttle gate check in auto_bid.py
10. **"AUTO_BID_ENABLED=true didn't take effect"** → Forgot to restart CHL-Backend after .env change

---

## What to skip / NOT do during iter 141.2 launch

- ❌ Don't merge branch `conflict_060526_1955` wholesale — it has destructive deletes (already cherry-picked to `iter_141_2_scaffolding/`; rest of branch can be deleted after iter 141.2 close)
- ❌ Don't touch `auto_dispatch.try_auto_accept` — LoadEvaluator shadow mode is still informative; iter 141.2 doesn't migrate yet
- ❌ Don't change FractalEvaluator base class — Phase 8 (iter 146.x) extends it for MarketEvaluator; signature stability matters

---

**Status:** ✅ REFINED by PM Claude (@pm-lead) 2026-05-07 — all 4 areas addressed:
1. Pre-flight sequencing: DAT + Twilio parallel async tasks
2. FMCSA delay decision tree: 4 status scenarios + escalation contacts + alt-paths
3. Stage 1d→1f transition gate: CONDITIONAL (same-day if GREEN, 24h if YELLOW)
4. 48h approval window: Detailed step-by-step click flow + composite score breakdown requirement + decision criteria

**Cross-reference:** `iter_141_2_agenda_draft.md` for full stage 1d/1f specs; `emergent_dashboard_review.md` for frontend merge sequence; `handoff_iter_141_1_close.md` for what shipped before iter 141.2 opens.

**Source:** Skeleton drafted overnight by Claude Code (@dev-engineer) 2026-05-06. Refined by PM Claude 2026-05-07 per @dev-engineer msg `c322841e` 4-area spec. Applied to chl-memory by @dev-engineer 2026-05-07 via chunked-DM Tier 2 (PM's claude.ai sandbox couldn't auth to GitHub for direct push).
