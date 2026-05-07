# Iter 141.2 Launch Operator Runbook (post-FMCSA finalization)

> **Status:** Skeleton draft (Claude Code overnight 2026-05-06). PM Claude can flesh out and refine when they pick up the 5th-deep queue item.
> **Trigger:** Day operator's FMCSA authority MC-1817555 finalizes (~2026-05-13).
> **Goal:** Get from "FMCSA approved" → "iter 141.2 in flight" without the operator having to remember scattered TODOs.

---

## Pre-Flight Checklist (operator does, ~20 minutes total)

### 1. Confirm FMCSA authority is active
- [ ] Log into https://portal.fmcsa.dot.gov
- [ ] Verify MC-1817555 status = "Active"
- [ ] Note authority date (used for some carrier vetting integrations)

### 2. Provision DAT service account + Connexion license **(start in PARALLEL, don't wait)**
- [ ] Email `techsupportteamleads@dat.com`:
  - Subject: "DAT Service Account + API Connexion License Activation Request"
  - Body: Include MC-1817555, Continental Haul Logistics LLC, and explicit request for Load Board + Rating + Capacity REST API access
- [ ] Track: typical activation is 3–5 business days
- [ ] Once received: store credentials in vault under "DAT — Service Account" + "DAT — API Connexion License"

### 3. Operator-side .env preparation
- [ ] Add to `C:\CHL\backend\.env`:
  ```
  DAT_SERVICE_USERNAME=<from DAT activation email>
  DAT_SERVICE_PASSWORD=<from DAT activation email>
  DAT_BASE_URL=https://freight.api.dat.com
  ```
- [ ] Run snapshot ceremony: `& "C:\CHL\scripts\snapshot_to_d.ps1" -Note "iter 141.2 prep — DAT creds populated"`

### 4. Twilio cred verification (separate from DAT, but related)
- [ ] Verify Twilio creds in `.env` (uncomment + populate):
  ```
  TWILIO_ACCOUNT_SID=AC<...>
  TWILIO_AUTH_TOKEN=<...>
  TWILIO_PHONE_NUMBER=+1<...>
  OWNER_SMS_NUMBER=+18165609180   # already saved
  ```
- [ ] Restart CHL-Backend (admin shell): `Restart-Service CHL-Backend`
- [ ] Smoke test: `& "C:\CHL\.venv-local\Scripts\python.exe" -c "from notification_service import ..."` (Claude Code will provide exact incantation when text_operator.py builds)

### 5. Iter 141.2 agenda commit
- [ ] Review `chl-memory/research/iter_141_2_agenda_draft.md` (PM Claude's full draft)
- [ ] Move + rename to `chl-memory/agenda/current_iter.md` and `chl-memory/agenda/stages/stage_1d.md` + `stages/stage_1f.md`
- [ ] `cd chl-memory && git add agenda/ && git commit -m "iter 141.2 agenda + stages 1d+1f committed for execution"`
- [ ] `git push origin main`

### 6. Dispatch Claude Code
- [ ] Open Claude Code in `C:\CHL\`
- [ ] Verify Claude Code session loads the new agenda from chl-memory
- [ ] First instruction: "Execute iter 141.2 stage 1d per agenda. Pre-flight + action steps + smoke."

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

**What Claude Code will do (per agenda; renamed scope per PM Claude DM `79d68e6d`):**

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
- Approve first ~5 bids manually via `/bids` dashboard
- After 48h smooth running: set `AUTO_BID_ENABLED=true` in `.env`, restart backend

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

### "DAT 401s on first poll"
- **Cause:** auth token expired or wrong base URL
- **Fix:** Check DAT activation email for confirmed base URL; may differ from `freight.api.dat.com` for sandbox vs prod

### "Lane composite_scores all 0.0 after iter 141.2 ships"
- **Cause:** `lane_scoring_cron` runs every 6h; if iter 141.2 ships in the middle of a window, scores aren't refreshed yet
- **Fix:** Manually trigger via `& "C:\CHL\.venv-local\Scripts\python.exe" -c "..."` (Claude Code will provide), OR wait 6h for next tick

### "/bids returns empty even though auto-bid generated 5"
- **Cause:** `AUTO_BID_ENABLED=false` (the safety gate). Auto-bid runs but stores results without sending; `/api/bids/pending` likely filters by `status='pending_operator_approval'`
- **Fix:** This is the intended first-48h behavior. Operator approves manually via dashboard. Set env to `true` after 48h.

### "Threshold tuner save shows 'lanes_passing: 0'"
- **Cause:** New thresholds too aggressive (e.g., profitability=30%) and no lanes pass
- **Fix:** Move sliders back; the live preview shows `change_from_current` which makes this obvious before the operator clicks Save. Reset-to-defaults button is right there.

---

## What to skip / NOT do during iter 141.2 launch

- ❌ Don't merge branch `conflict_060526_1955` wholesale — it has destructive deletes (already cherry-picked to `iter_141_2_scaffolding/`; rest of branch can be deleted after iter 141.2 close)
- ❌ Don't touch `auto_dispatch.try_auto_accept` — LoadEvaluator shadow mode is still informative; iter 141.2 doesn't migrate yet
- ❌ Don't change FractalEvaluator base class — Phase 8 (iter 146.x) extends it for MarketEvaluator; signature stability matters

---

**Status:** Skeleton draft, ready for PM Claude refinement.
**Cross-reference:** `chl-memory/research/iter_141_2_agenda_draft.md` for full stage 1d/1f specs; `chl-memory/research/emergent_dashboard_review.md` for frontend merge sequence; `memory/handoff_iter_141_1_close.md` for what shipped before iter 141.2 opens.

**Source:** Drafted overnight by Claude Code (`@dev-engineer`) 2026-05-06 while operator slept. PM Claude flesh-out + refinement queued for whichever timezone fires first.
