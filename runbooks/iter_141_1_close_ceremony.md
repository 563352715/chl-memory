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

### Step 2 — HTTP smoke for stage 1e endpoint (~30 sec)

```powershell
$resp = Invoke-WebRequest -Uri "http://localhost:8000/api/lanes/top/20" -UseBasicParsing
$resp.StatusCode  # expect: 200
$resp.Content | ConvertFrom-Json | Select-Object -First 1
```

**PASS criteria** (all required):
- StatusCode = `200`
- Response is valid JSON (no parse error)
- Each lane object has the expected fields: `origin_state`, `dest_state`, composite score, `dat_status: "deferred_to_iter_141_2"`, `opportunities_24h: 0`, `historical_loads_90d` populated

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
| HTTP `/api/lanes/top/20` | 200 OK |
| Response JSON parse | Valid |
| `dat_status` field | `"deferred_to_iter_141_2"` |
| `opportunities_24h` field | Present (value `0` is valid pre-iter-141.2) |
| `historical_loads_90d` field | Populated with non-null integer |
| Health endpoint `/api/health/system` | All 14 modules green; `lane_scoring_cron.loop_running: true` |
| Backup to D: | Exit 0, checksums match |

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
   - `404 on /api/lanes/top/20` → backend didn't restart cleanly OR route wasn't registered. Check backend logs for import errors. Restart-Service again.
   - `500 on endpoint` → MongoDB connection issue or unhandled exception. Check both backend log + Mongo log.
   - `D:\` backup fails with permission error → BitLocker locked OR disk full. Unlock + check free space.
   - `Get-Service CHL-Backend → not found` → service uninstalled or renamed. Re-register via install script if available.

---

**Source:** Drafted by @pm-lead 2026-05-07 as TASK 12 follow-on. Cross-references: `chl-memory/progress/current_status.md` (active state), `chl-memory/research/iter_141_2_launch_operator_runbook.md` (next iter pre-flight), `C:\CHL\memory\handoff_iter_141_1_close.md` (CHL-side close handoff doc, if drafted).
