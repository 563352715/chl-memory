# Iter 141.1 Close Ceremony Runbook

> **Purpose:** Operator-facing ~5-min runbook for closing iter 141.1 (Phase 2 Load Discovery & Evaluation foundation). Deferred ~24h pending admin shell availability.
> **Read-time:** ~30 sec (single-screen reference; scroll while executing).
> **Prerequisite skill:** familiarity with admin PowerShell + git + the CHL repo layout.

---

## Pre-conditions (verify before starting)

Run a 5-second pre-flight scan. All must be true:

- [ ] **Admin PowerShell available.** Right-click PowerShell → "Run as Administrator". UAC will prompt — accept.
- [ ] **CHL codebase at expected commit.** From `C:\CHL`: `git log -1 --oneline` should show stage 1e shipped (`808e373` or later). If you see uncommitted changes, decide whether to commit them in a separate "wip during iter 141.1 wait" commit BEFORE the close ceremony so iter close commit is clean.
- [ ] **No in-flight smoke tests.** Check `C:\CHL\.tmp\` — no half-written smoke output files. If a prior smoke crashed mid-run, clear stragglers first.
- [ ] **MongoDB service running.** From admin shell: `Get-Service MongoDB` → Status `Running`. If `Stopped`: `Start-Service MongoDB` and wait ~5s.
- [ ] **D: drive mounted + writable.** `Get-Volume D` → returns valid drive object. If D: is BitLocker-locked: `Unlock-BitLocker -MountPoint D:` first.
- [ ] **Operator has ~10 min uninterrupted.** Don't start mid-call.

---

## Step-by-step (5–7 min wall-clock)

### Step 1 — Restart CHL-Backend service (~30 sec)

```powershell
Restart-Service CHL-Backend
Start-Sleep -Seconds 5
Get-Service CHL-Backend  # confirm Status: Running
```

If `Restart-Service` errors with "service not found", check service name: `Get-Service *CHL*`. The service name may be `CHL-Backend` or `chl-backend` depending on install path.

### Step 2 — HTTP smoke (~30 sec)

> **CORRECTED 2026-05-07 (post-iter-141.1-close):** Original draft of this runbook prescribed `Invoke-WebRequest http://localhost:8000/api/lanes/top/20` expecting HTTP 200 + JSON body. Two surprises during actual close: (a) backend runs on **port 8001**, not 8000; (b) `/api/lanes/top/{N}` is behind `require_broker` JWT auth and returns 401 to unauthenticated curl (operator-facing surface is the frontend dashboard, NOT direct curl). **Use the health-endpoint smoke below as the canonical iter-close validation.**

```powershell
# Primary smoke: health endpoint (no auth required)
$resp = Invoke-WebRequest -Uri "http://localhost:8001/api/health/system" -UseBasicParsing
$resp.StatusCode  # expect: 200
$health = $resp.Content | ConvertFrom-Json
$health | Select-Object total, healthy, degraded, unhealthy
$health.modules | Where-Object { $_.name -in @("lane_evaluator", "lane_scoring_cron") }
```

**PASS criteria for stage 1e close:**
- StatusCode = `200` on `/api/health/system`
- `lane_evaluator` module present + status `healthy`
- `lane_scoring_cron` module present + status `healthy` + `loop_running: true` + non-null `last_run_success_at` within last 6h
- `total` ≥ 14 modules; `unhealthy` = 0 (degraded modules acceptable IF unrelated to stage 1e — e.g., `bmc84_watcher` may report degraded due to FMCSA web key not configured; pre-existing, unrelated to lane work)

**Secondary smoke (route-registration confirmation):**
```powershell
# Confirm the endpoint route is registered (expect 401, NOT 404)
$ep = Invoke-WebRequest -Uri "http://localhost:8001/api/lanes/top/20" -UseBasicParsing -ErrorAction SilentlyContinue
$ep.StatusCode  # expect: 401 (route registered, JWT auth gating works) — NOT 404 (route missing)
```

A 401 response confirms the route is registered + auth-protected. A 404 means the backend didn't load the lanes module — halt close.

**FAIL → halt close ceremony.** See "Rollback / blocker" section below.

### Step 3 — Iter close backup to D: drive (~2 min)

```powershell
& "C:\CHL\scripts\backup_to_d.ps1" -IterId "iter_141_1_close" -Note "Phase 2 foundation - lane evaluation + dashboard endpoint"
```

The script: dumps Mongo collections, snapshots `C:\CHL\` (excluding .venv + node_modules), writes to `D:\CHL_backups\rolling\iter_141_1_close\<timestamp>\`, generates checksums.

**PASS criteria:** script exits 0; final line of output reads `Backup complete:` followed by the `D:\` path.

### Step 4 — Update `current_status.md` (~1 min)

Edit `C:\CHL\chl-memory\progress\current_status.md`:
- Header line: change `**Active iter:** 141.1 — NEAR-COMPLETE` → `**Active iter:** 141.2 — DISPATCH-PENDING (FMCSA-gated)` (or whichever iter is next-up at close time).
- "Active stage" line: change `1e SHIPPED... pending operator admin Restart-Service` → `1e CLOSED — HTTP 200 verified, backup snapshotted to D:\CHL_backups\rolling\iter_141_1_close\<timestamp>\`.
- Bridge cumulative: `6 iters / 18 stages / 87 of 89 smoke / 0 STOP CONDITIONS` → bump iters to `7` and stages to `19` (1e shipped counts as the 19th stage).
- Final-stage-status table line for stage 1e: change smoke column from `5/6 PASS + 1 SKIP` to `5/6 PASS + 1 SKIP, CLOSED <YYYY-MM-DD>`.

### Step 5 — Commit + push close ceremony (~1 min)

From `C:\CHL\chl-memory\`:

```powershell
git add progress/current_status.md
git commit -m "iter 141.1 CLOSED: stage 1e HTTP smoke green, D: backup snapshotted"
git push origin main
```

From `C:\CHL\` (CHL repo):

```powershell
# If close handoff doc exists at memory/handoff_iter_141_1_close.md:
git add memory/handoff_iter_141_1_close.md
git commit -m "iter 141.1 CLOSED: handoff doc finalized"
git push origin main
```

---

## Smoke validation criteria (consolidated)

Stage 1e is considered CLOSED when:

| Check | Expected |
|---|---|
| Health endpoint `/api/health/system` (PRIMARY) | 200 OK; `lane_evaluator` healthy; `lane_scoring_cron` healthy + `loop_running: true` + recent `last_run_success_at` |
| Module count + status | `total` ≥ 14; `unhealthy` = 0 (degraded acceptable if unrelated to stage 1e, e.g., `bmc84_watcher` pre-existing) |
| `/api/lanes/top/20` route registration | **401** (route registered + auth-protected) — NOT 404 |
| Backup to D: | Exit 0, checksums match |
| ~~Direct curl to `/api/lanes/top/20`~~ | ~~Not applicable~~ — endpoint behind broker JWT auth; operator-facing surface is the frontend dashboard, not curl |

If any row fails: halt + see Rollback.

---

## Close-ceremony checklist (final pass)

- [ ] CHL-Backend service running cleanly (no error logs in last 5 min)
- [ ] HTTP smoke 200 + JSON shape verified
- [ ] D: backup folder exists with timestamp + checksums
- [ ] `current_status.md` updated (active iter, active stage, bridge cumulative, stage 1e row)
- [ ] chl-memory commit pushed to main
- [ ] CHL repo close handoff doc committed + pushed (if not already)
- [ ] Auto-memory entry (if relevant for future-Claude reference): note that iter 141.1 closed on `<YYYY-MM-DD>` and current Phase 2 foundation is COMPLETE
- [ ] Ping @dev-engineer in AgentDM with: "Iter 141.1 CLOSED. Bridge cumulative now 7/19/87+/0. Iter 141.2 dispatch when FMCSA authority lands."

---

## Rollback / blocker path (if smoke fails)

**Do NOT close iter 141.1 with a failing smoke.**

1. **Capture the failure.** Save `Invoke-WebRequest` error output and CHL-Backend log tail (`Get-Content C:\CHL\logs\backend.log -Tail 50`) to a temp file.
2. **File a blocker note.** Create `chl-memory/research/iter_141_1_close_blocker_<YYYY-MM-DD>.md` with: what step failed, exact error, time of failure, what you tried.
3. **Defer the close.** Update `current_status.md` "Active stage" line to reflect new blocker (e.g., "1e SHIPPED but HTTP smoke FAIL — see blocker note"). Do NOT mark iter 141.1 closed.
4. **Commit the blocker note + status update** so @dev-engineer can pick it up next session.
5. **Common failure modes + first-pass fixes:**
   - `404 on /api/lanes/top/20` → backend didn't restart cleanly OR lanes route wasn't registered. Check backend logs for import errors. Restart-Service again. (A `401` is GOOD — route registered + auth-protected.)
   - `Connection refused on port 8001` → backend service started but isn't listening. Check `Get-NetTCPConnection -LocalPort 8001`. If empty, backend failed startup mid-init; tail backend log for the trace.
   - `Connection succeeded on port 8000 (or other)` → port mismatch. The runbook assumes 8001 (post-iter-141.1 verified). If your install uses a different port, capture it in the operator's local `.env` reference + update the runbook for next iter close.
   - `500 on health endpoint` → MongoDB connection issue or unhandled exception. Check both backend log + Mongo log.
   - `D:\` backup fails with permission error → BitLocker locked OR disk full. Unlock + check free space.
   - `Get-Service CHL-Backend → not found` → service uninstalled or renamed. Re-register via install script if available.
   - `Restart-Service: insufficient privilege` → PowerShell window is non-elevated. Right-click PowerShell → "Run as Administrator", accept UAC, retry. (Confirmed-real failure mode encountered during iter 141.1 close.)

---

**Source:** Drafted by @pm-lead 2026-05-07 as TASK 12 follow-on. Cross-references: `chl-memory/progress/current_status.md` (active state), `chl-memory/research/iter_141_2_launch_operator_runbook.md` (next iter pre-flight), `C:\CHL\memory\handoff_iter_141_1_close.md` (CHL-side close handoff doc, if drafted).
