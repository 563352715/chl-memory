# Handoff — Iter 139.40 — NSSM Services

**Date:** 2026-05-05
**Session:** NSSM playbook (steps 1–19) executed end-to-end
**Outcome:** ✅ Both `CHL-Backend` and `CHL-Frontend` installed and Running under NSSM, StartType=Manual

---

## What Changed

CHL backend and frontend are no longer started by hand from a PowerShell window. They now run as Windows services managed by NSSM, but with **manual start mode** (`SERVICE_DEMAND_START`) — they will NOT start automatically at boot. Use `Start-Service` / `Stop-Service` to control them explicitly.

---

## Services

| Service | Display Name | Listens On | Working Dir | Start Type | Status |
|---|---|---|---|---|---|
| `CHL-Backend` | CHL Backend (uvicorn :8001) | 127.0.0.1:8001 | `C:\CHL\backend` | Manual | Running |
| `CHL-Frontend` | CHL Frontend (craco :3000) | 0.0.0.0:3000 | `C:\CHL\frontend` | Manual | Running |

### CHL-Backend — full config
- **Application:** `C:\CHL\.venv-local\Scripts\python.exe`
- **AppParameters:** `-m uvicorn server:app --host 127.0.0.1 --port 8001`
- **AppDirectory:** `C:\CHL\backend`
- **Environment:** none injected (backend auto-loads `C:\CHL\backend\.env` via `python-dotenv` — see `server.py:23`)
- **AppExit Default:** Restart (with 5000ms delay)
- **AppStdout:** `C:\CHL\logs\nssm\backend.out.log`
- **AppStderr:** `C:\CHL\logs\nssm\backend.err.log`
- **Log rotation:** 10 MB → roll
- **ObjectName:** LocalSystem

### CHL-Frontend — full config
- **Application:** `C:\Program Files\nodejs\npm.cmd`
- **AppParameters:** `start` (resolves to `craco start` per `package.json` scripts)
- **AppDirectory:** `C:\CHL\frontend`
- **AppEnvironmentExtra:**
  - `PORT=3000`
  - `BROWSER=none` (suppresses craco's browser-launch attempt; LocalSystem has no desktop)
  - `CI=true` (suppresses interactive prompts)
  - `PATH=C:\Program Files\nodejs;C:\Windows\system32;C:\Windows;C:\Windows\System32\Wbem;C:\Windows\System32\WindowsPowerShell\v1.0\` (LocalSystem has minimal PATH; node + npm need to find each other)
- **AppExit Default:** Restart (with 5000ms delay)
- **AppStdout:** `C:\CHL\logs\nssm\frontend.out.log`
- **AppStderr:** `C:\CHL\logs\nssm\frontend.err.log`
- **Log rotation:** 10 MB → roll
- **ObjectName:** LocalSystem

---

## Operational Cheat Sheet

```powershell
# Start (elevated PowerShell)
Start-Service CHL-Backend
Start-Service CHL-Frontend

# Stop
Stop-Service CHL-Backend
Stop-Service CHL-Frontend

# Status
Get-Service CHL-Backend, CHL-Frontend

# Tail logs
Get-Content C:\CHL\logs\nssm\backend.err.log -Tail 50 -Wait
Get-Content C:\CHL\logs\nssm\frontend.out.log -Tail 50 -Wait

# Reconfigure (NSSM GUI editor)
$nssm = "C:\Users\meyer\AppData\Local\Microsoft\WinGet\Packages\NSSM.NSSM_Microsoft.Winget.Source_8wekyb3d8bbwe\nssm-2.24-101-g897c7ad\win64\nssm.exe"
& $nssm edit CHL-Backend

# Uninstall (if ever needed)
& $nssm remove CHL-Backend confirm
& $nssm remove CHL-Frontend confirm
```

---

## Venv-Mixing Concern (Resolved)

**Prior state (flagged by user):** the previously-running uvicorn dev process had `python.exe` from `C:\Users\meyer\AppData\Local\...\Downloads venv` but the `uvicorn.exe` entry-point script was from `C:\CHL\.venv-local`. Two interpreters / two site-packages, ambiguous package origin.

**Now:** the service launches `C:\CHL\.venv-local\Scripts\python.exe -m uvicorn server:app …`. Python and uvicorn are guaranteed to resolve from the same venv prefix.

**Verified at first health-check (against a fresh spawn of the same `python.exe`):**
- `sys.executable` → `C:\CHL\.venv-local\Scripts\python.exe`
- `sys.prefix` → `C:\CHL\.venv-local`
- `uvicorn.__file__` → `C:\CHL\.venv-local\Lib\site-packages\uvicorn\__init__.py` (v0.32.1)
- `fastapi.__file__` → `C:\CHL\.venv-local\Lib\site-packages\fastapi\__init__.py` (v0.115.6)

**Note on Win32 `ExecutablePath`:** WMI reports the running backend's `ExecutablePath` as `C:\Users\meyer\AppData\Local\Python\pythoncore-3.14-64\python.exe`. This is **not** a regression — Windows venvs are implemented by copying the base interpreter binary into the venv's `Scripts\` directory, then a `pyvenv.cfg` redirects `sys.prefix`. The CommandLine field correctly shows the venv's path was used to launch, and `sys.prefix` confirms the runtime prefix. Don't be alarmed if the WMI ExecutablePath looks "wrong" — it isn't.

---

## Health-Check Results

### Backend (`http://127.0.0.1:8001`)
- `GET /docs` → **200 OK**
- `GET /api/` → **401 Unauthorized** (`{"detail":"Not authenticated"}`) — expected, proves the auth middleware is firing
- `GET /api/ops/state` → **401 Unauthorized** — same
- Boot log shows: 59/59 DB indexes ready, all schedulers armed (cron_scheduler 6 loops, failure_detectors 3, failure_detectors_part2 8, compliance_watcher, banker_digest, synthetic_auto_wipe, etc.), `Application startup complete`

### Frontend (`http://127.0.0.1:3000`)
- `GET /` → **200 OK**, Content-Type `text/html`, length 13018, title `Continental Haul Logistics`, bundle ref `/static/js/bundle.js`
- Boot log: `Compiled successfully!` / `webpack compiled successfully`

### Pre-existing warnings (not caused by NSSM)
- `compliance_watcher` logs a 503 `Twilio credentials are not configured` warning — this is the local dev DB lacking the production Twilio creds, not a service issue.
- Frontend logs CRA/webpack-dev-server `DeprecationWarning` noise about `fs.F_OK`, `onAfterSetupMiddleware`, `Compilation.assets` — long-standing CRA 5 / webpack 5 warnings, harmless.

---

## NSSM Binary Location

Installed via `winget install NSSM.NSSM` v2.24-101-g897c7ad. Resolved path:

```
C:\Users\meyer\AppData\Local\Microsoft\WinGet\Packages\NSSM.NSSM_Microsoft.Winget.Source_8wekyb3d8bbwe\nssm-2.24-101-g897c7ad\win64\nssm.exe
```

Winget added an alias `nssm` to PATH but the new entry only takes effect in shells started **after** the winget install — existing PowerShell sessions will not see it until restarted. Use the full path above (or open a new shell) for direct `nssm` invocations.

---

## Things to Watch / Future Work

1. **LocalSystem may not have access to user-scoped npm caches.** The frontend started fine because `node_modules` is in the project dir (963 packages), but if you ever need to `npm install` while running as a service, you'd have to do that as the user first.
2. **Auto-restart on crash is ON** (`AppExit Default Restart`, 5s delay). If either service crash-loops, the err log is the place to check first. `nssm` will keep restarting them every 5 s indefinitely.
3. **Manual start by design.** Per Jason's request, startup type is `SERVICE_DEMAND_START` (Manual). They will not start at Windows boot. If you want them to come up automatically later, run:
   ```
   & $nssm set CHL-Backend Start SERVICE_AUTO_START
   & $nssm set CHL-Frontend Start SERVICE_AUTO_START
   ```
4. **Log rotation is 10 MB.** With the backend's verbose scheduler logging, this rolls fast. Consider raising `AppRotateBytes` (currently 10485760 = 10 MB) if you want larger retention.
5. **Backend `.env` is read from `C:\CHL\backend\.env`.** If you move or rename it, the service will start in degraded mode (Twilio/Mongo/Resend creds unset). The pre-existing `compliance_watcher` 503 warning is the canary — if that flips to other 503/503-style warnings, check the env file first.

---

## Desktop Shortcuts (added 2026-05-05)

Three one-click batch files have been added so the operator does not need to remember PowerShell commands. Both authoritative copies and Desktop copies exist; either works.

**Authoritative source:** `C:\CHL\shortcuts\`
**Desktop copies:** `C:\Users\meyer\OneDrive\Desktop\` *(Desktop is OneDrive-redirected on this machine — `C:\Users\meyer\Desktop` does not exist as a folder)*

| Shortcut | Purpose | Elevation? |
|---|---|---|
| `Start CHL.bat` | Starts CHL-Backend, then CHL-Frontend, waits 10 s, opens default browser to `http://127.0.0.1:3000/ops`, then auto-closes | Yes — self-elevates via UAC if not already admin |
| `Stop CHL.bat` | Stops CHL-Frontend (UI layer), then CHL-Backend (data layer), prints confirmation, auto-closes after 3 s | Yes — self-elevates via UAC |
| `Open CHL.bat` | Just opens default browser to `http://127.0.0.1:3000/ops`. Use when services are already running. | No |

### Behavior details

- **Self-elevation pattern:** the Start/Stop scripts run `fltmc >nul 2>&1` to test for admin. If not admin, they relaunch themselves via `powershell Start-Process -Verb RunAs` and the un-elevated copy exits immediately. The user sees a UAC prompt; clicking Yes opens an elevated console with the actual output.
- **Idempotency:** `Start-Service` and `Stop-Service` are no-ops when the service is already in the requested state, so double-clicking Start when services are already running just re-opens the browser. Stop on already-stopped services prints `[SKIP]`.
- **Failure path:** if the underlying `Start-Service` / `Stop-Service` call throws something other than "already running"/"already stopped", the script prints the error and `pause`s so the operator can read it before closing.
- **Encoding:** all three .bat files are clean ASCII (no UTF-8/UTF-16 BOM), so cmd.exe parses them correctly.

### To re-deploy or update

```powershell
Copy-Item "C:\CHL\shortcuts\*.bat" -Destination ([Environment]::GetFolderPath('Desktop')) -Force
```

If the operator ever moves machines or the Desktop folder changes, treat `C:\CHL\shortcuts\` as the canonical source and re-copy.

---

## Connection to Pre-Flight Intake Findings (preflight_intake_20260505.md)

This handoff resolves **ops infrastructure**, not any of the ~14 RED FLAGS in the pre-flight report. Specifically:
- Does NOT touch R2 (cron_scheduler.py:68 unfiltered synthetic query) — code-level fix still pending
- Does NOT touch R1 (Day-1 Gate counter divergence between ops_state.py and boot_briefing.py) — still pending
- Does NOT touch R4 (`/loads/:id` route handler missing in App.js) — still pending
- DOES make the running backend more reliable (auto-restart on crash, clean venv, structured logs) which incidentally helps R8 (no central cron health-check) by ensuring the process hosting all 36 schedulers gets restarted on any unhandled exception within 5 s.
