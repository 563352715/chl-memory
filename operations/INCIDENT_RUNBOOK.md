# CHL Incident Response Runbook

**Audience:** operator (Jason Meyer), solo. No on-call team. This is the document you read when something breaks on May 14 launch day.

**Operator-mandated:** 2026-05-10. Self-contained — should be readable + actionable without re-reading other docs.

**Scope:** production CHL platform on `C:\CHL\`, Windows desktop host. Twelve-layer execution blueprint detail lives in `chl-memory/architecture/SYSTEM_MATRIX.md`; this runbook is the on-call quick-reference.

**Signature for vendor support tickets:**
`Dispatch Team / Continental Haul Logistics LLC / MC 1817555 · USDOT 4569843` (from `dispatch@continentalhaul.com`).

---

## Table of contents

1. [First 5 minutes — diagnostic checklist](#1-first-5-minutes--diagnostic-checklist)
2. [Top-10 failure modes](#2-top-10-failure-modes)
   - 2.1 [Backend service down](#21-backend-service-down-chl-backend-not-running)
   - 2.2 [Frontend service down](#22-frontend-service-down)
   - 2.3 [MongoDB connection failure](#23-mongodb-connection-failure)
   - 2.4 [Twilio / Plivo SMS not sending](#24-twilio--plivo-sms-not-sending)
   - 2.5 [Telnyx 10DLC TFV not approved](#25-telnyx-10dlc--tfv-not-approved)
   - 2.6 [FMCSA SAFER API down / rate-limited](#26-fmcsa-safer-api-down--rate-limited)
   - 2.7 [Carrier acceptance flow stuck](#27-carrier-acceptance-flow-stuck-token-expired--magic-link-broken)
   - 2.8 [Funding gate refusing all loads](#28-funding-gate-refusing-all-loads-gps-not-pinging)
   - 2.9 [RED throttle state stuck](#29-red-throttle-state-stuck)
   - 2.10 [Out of disk space on C: drive](#210-out-of-disk-space-on-c-drive)
   - 2.11 [Vault encryption key lost (catastrophic)](#211-vault-encryption-key-lost-catastrophic)
3. [Kill switches reference](#3-kill-switches-reference)
4. [Restore-from-backup procedure](#4-restore-from-backup-procedure)
   - 4.5 [How to test the restore procedure quarterly](#45-how-to-test-the-restore-procedure-quarterly)
5. [Working in test mode safely](#5-working-in-test-mode-safely)
6. [Vendor escalation contacts](#6-vendor-escalation-contacts)
7. [Standing operator directives](#7-standing-operator-directives)
8. [Communication during incident](#8-communication-during-incident)

---

## 1. First 5 minutes — diagnostic checklist

**Where to look first:** start at the top, stop when you have a clear signal.

### 1.1 Service status (run all three)
```powershell
Get-Service CHL-Backend, CHL-Frontend, MongoDB
```
**Pass:** all three `Running`. **Fail:** one or more `Stopped` → jump to section 2.1 / 2.2 / 2.3 by which one.

### 1.2 Health endpoints
```powershell
curl http://localhost:8000/api/health/system | ConvertFrom-Json | Select-Object overall_status, summary
```
**Pass:** `overall_status: healthy`. **Fail:** `unhealthy` or `degraded` → inspect `modules` field for which check failed.

```powershell
curl http://localhost:8000/api/health/scrapers
```
**Pass:** loadboard scraper reports `loadboard_health.status: healthy`.

```powershell
# Authenticated aggregator (requires owner cookie or basic auth):
curl -u "$env:CHL_OWNER_EMAIL`:$env:ADMIN_PASSWORD" http://localhost:8000/healthz/all
```
**Pass:** consolidated `overall: GREEN`. **Fail:** check per-component breakdown for the red item.

### 1.3 Recent operator alerts (last hour)
```powershell
mongo $env:MONGO_URL --quiet --eval "db.getSiblingDB('$env:DB_NAME').operator_alerts.find({timestamp:{`$gte:new Date(Date.now()-3600*1000).toISOString()}}).sort({timestamp:-1}).limit(20).pretty()"
```
**Pass:** zero rows or only `INFO`/`LOW`. **Fail:** any `CRITICAL` or `HIGH` rows → read `message` + `source` field; jump to the matching failure mode.

### 1.4 Recent cron failures
```powershell
mongo $env:MONGO_URL --quiet --eval "db.getSiblingDB('$env:DB_NAME').cron_health_events.find({event_type:'failed',timestamp:{`$gte:new Date(Date.now()-3600*1000).toISOString()}}).sort({timestamp:-1}).limit(20).pretty()"
```
**Pass:** zero rows. **Fail:** look at `cron_name` field for which cron is dying. Most common: scraper timeout, FMCSA throttling, factor submission failure.

### 1.5 Disk free
```powershell
Get-PSDrive C, D | Format-Table Name, @{N='FreeGB';E={[math]::Round($_.Free/1GB,2)}}, @{N='UsedGB';E={[math]::Round($_.Used/1GB,2)}}
```
**Pass:** C: > 50GB free. **Surface:** 20GB. **Hard-stop:** 10GB → jump to section 2.10.

### 1.6 Throttle state
```powershell
curl -u "$env:CHL_OWNER_EMAIL`:$env:ADMIN_PASSWORD" http://localhost:8000/api/throttle/status | ConvertFrom-Json
```
**Pass:** `current_state: GREEN`. **Fail:** `RED` blocks 100% of loads → jump to section 2.9.

If steps 1.1–1.6 all PASS but the operator is still seeing a problem, the issue is likely in a specific flow (carrier acceptance, dispatch, factor submission). Search `db.operator_alerts` with a longer window (24h) and grep for the keyword the user reported.

---

## 2. Top-10 failure modes

Ranked by likelihood × launch-day impact.

### 2.1 Backend service down (CHL-Backend not Running)

**Symptom:** Browser shows "Cannot reach the server" or all `/api/*` endpoints return connection refused. Frontend UI loads but no data populates.

**Diagnostic:**
```powershell
Get-Service CHL-Backend
Get-EventLog -LogName Application -Source "CHL-Backend" -Newest 20 -ErrorAction SilentlyContinue
```
If `Stopped`: the NSSM-managed service exited. If `Running` but unreachable: process is hung — `Get-NetTCPConnection -LocalPort 8000` shows no LISTEN line.

**Resolution:**
1. **Open** an elevated PowerShell.
2. **Run** `Restart-Service CHL-Backend`.
3. **Wait** 15 seconds.
4. **Run** `Get-Service CHL-Backend` → must show `Running`.
5. **Run** `curl http://localhost:8000/api/health/system` → must return JSON (not connection refused).
6. **If still failing:** inspect logs at `C:\CHL\backend\logs\backend.log` (last 200 lines) for the actual crash stacktrace.
7. **If logs show import error or missing module:** run `cd C:\CHL\backend; py -m pip install -r requirements.txt` and retry restart.

**Rollback:** if a recent code change is suspected — `cd C:\CHL; git log --oneline -10` to find last-known-good commit, then `git checkout <sha> -- backend/` (do NOT do a full `git reset --hard` on `main`). Restart service. If that fixes it, the diff between HEAD and `<sha>` is the regression.

---

### 2.2 Frontend service down

**Symptom:** `https://localhost:3000` returns "Cannot connect" or "This site can't be reached." Backend is fine (`/api/health/system` returns JSON).

**Diagnostic:**
```powershell
Get-Service CHL-Frontend
Get-NetTCPConnection -LocalPort 3000 -ErrorAction SilentlyContinue
```

**Resolution:**
1. **Run** `Restart-Service CHL-Frontend`.
2. **Wait** 30 seconds (React dev server boot is slower than the backend).
3. **Open** `http://localhost:3000` in browser.
4. **If still failing:** check `C:\CHL\frontend\logs\frontend.log` for compile errors.
5. **If a recent App.js edit broke compile:** `cd C:\CHL\frontend; git diff HEAD~1 -- src/App.js` to see the last change; revert with `git checkout HEAD~1 -- src/App.js` if obviously broken.

**Rollback:** same as 2.1 — revert `frontend/` to the last-known-good sha, restart.

---

### 2.3 MongoDB connection failure

**Symptom:** `/api/health/system` returns `unhealthy` with `database` module flagged. Backend logs show `pymongo.errors.ServerSelectionTimeoutError` or `Connection refused on 27017`.

**Diagnostic:**
```powershell
Get-Service MongoDB
Get-NetTCPConnection -LocalPort 27017 -ErrorAction SilentlyContinue
mongo --eval "db.adminCommand({ping:1})"
```

**Resolution:**
1. **Run** `Restart-Service MongoDB`.
2. **Wait** 20 seconds.
3. **Run** `mongo --eval "db.adminCommand({ping:1})"` → must return `{ok:1}`.
4. **If service won't start:** check `C:\Program Files\MongoDB\Server\<ver>\log\mongod.log` for the actual error. Most common: corrupt WiredTiger journal, disk full, or PID-file collision.
5. **Restart-Service CHL-Backend** after Mongo is back so the backend reconnects cleanly.

**Rollback (last resort if Mongo is corrupt):** see section 4 — restore Mongo from D-drive snapshot.

---

### 2.4 Twilio / Plivo SMS not sending

**Symptom:** `db.operator_alerts` rows show `sent_via:["text"]` but `results.text.success:false`. Carrier outreach blasts in `db.outbound_sms` show `status: failed`. Operator's phone receives no alerts.

**Context:** Plivo migration is in progress per iter 141.3. Today the system may be on either Twilio or Plivo depending on `SMS_PROVIDER` env. A2P 10DLC campaign may be in REJECTED or PENDING state.

**Diagnostic:**
```powershell
# Check which provider is wired:
Get-Content C:\CHL\backend\.env | Select-String "^SMS_PROVIDER="

# Recent send failures:
mongo $env:MONGO_URL --quiet --eval "db.getSiblingDB('$env:DB_NAME').outbound_sms.find({status:'failed',ts:{`$gte:new Date(Date.now()-3600*1000).toISOString()}}).sort({ts:-1}).limit(5).pretty()"

# Inspect error code field — `21610` = STOP'd, `30007` = carrier filter, `30034` = A2P unapproved.
```

**Resolution:**
1. **If error code is `30034` (A2P unapproved):** the brand/campaign is gated by carrier. Outbound SMS will fail for non-toll-free numbers. **Confirm** the Messaging Service SID (`TWILIO_MESSAGING_SERVICE_SID` / `PLIVO_APPLICATION_ID`) in `.env` points to the toll-free profile, which bypasses 10DLC.
2. **If error code is `30007` (carrier filter):** message body contains spam-trigger phrase. **Open** the failed row's `body` field; rewrite to remove ALL-CAPS, dollar amounts, URLs without context.
3. **If error code is `21610` (STOP):** recipient opted out. **Run** `mongo --eval "db.opt_out_list.find({phone:'<the number>'})"` to confirm; do NOT re-message.
4. **If error is `429` or `503`:** provider rate-limited or down. **Run** `curl https://status.twilio.com/api/v2/status.json` (or `https://status.plivo.com/`) and check vendor status. Wait + retry.
5. **For provisioning a new key after rotation:** use `C:\CHL\scripts\set_env_var.ps1 -Key TWILIO_AUTH_TOKEN -Value "..."` — never paste secrets into VS Code directly.

**Rollback:** flip `SMS_PROVIDER=twilio` (or `plivo`) in `.env` via `set_env_var.ps1` to the known-working provider; `Restart-Service CHL-Backend`.

---

### 2.5 Telnyx 10DLC / TFV not approved

**Symptom:** Outbound SMS via the Telnyx toll-free number (`+1-417-219-3856`) returns 400 with TFV-not-approved error. Inbound SMS to that number doesn't trigger the webhook.

**Context:** Telnyx local 10DLC port FOC = 2026-05-14. TFV (Toll-Free Verification) is gating launch. Field values for the TFV submission live at `chl-memory/operations/telnyx_tfv_field_values_2026_05_10.md`.

**Diagnostic:**
1. **Log into** Telnyx Mission Control: `https://portal.telnyx.com/`.
2. **Navigate to** Messaging → Toll-Free Verification.
3. **Check** the verification status for `sr_b98ea4`.

**Resolution:**
1. **If status = `IN REVIEW`:** wait. Telnyx target SLA is 1–3 business days. There is no operator action.
2. **If status = `REJECTED`:** read the rejection reason from the portal. Most common: opt-in proof URL not accessible, traffic-volume estimate too high, sample message not clearly transactional.
3. **Fix** the rejected field per `chl-memory/operations/telnyx_tfv_field_values_2026_05_10.md`.
4. **Resubmit** in the portal.
5. **DM `@pm-lead`** with rejection reason + remediation status.

**Fallback during outage:** route critical SMS via Twilio (still active during port soak) by flipping `SMS_PROVIDER=twilio` per 2.4 rollback. Telnyx port date is HARD; if rejection blocks past May 14, escalate to Telnyx support (see section 5).

---

### 2.6 FMCSA SAFER API down / rate-limited

**Symptom:** Carrier vetting checks fail. `db.vetting_checks` rows show `status: error` with FMCSA-source. New carriers can't be onboarded. Existing carriers may show stale authority data.

**Diagnostic:**
```powershell
curl "https://safer.fmcsa.dot.gov/CompanySnapshot.aspx?dot_number=4569843" -UseBasicParsing | Select-Object StatusCode
```
- **200:** SAFER is up; problem is our auth/key or rate limiting.
- **5xx:** SAFER is down (it goes down regularly).
- **429:** we got rate-limited.

**Resolution:**
1. **If SAFER is down (5xx):** there is NO action to take on the operator side. SAFER outages typically last 30 min–4 hours. **Surface** to `@pm-lead`; the vetting queue will drain when service returns.
2. **If rate-limited (429):** reduce poll cadence. **Edit** `db.cron_state` `fmcsa_poll_interval_min` from 5 to 15 min:
   ```powershell
   mongo --eval "db.cron_state.updateOne({_id:'fmcsa_poll'},{`$set:{interval_min:15}})"
   ```
3. **If our WebKey is invalid:** check `FMCSA_WEBKEY` env. The signup is operator-blocked — re-apply via the FMCSA registration portal if revoked.
4. **Do NOT book new carriers** during a SAFER outage; the loss-floor + vetting-gate rules will flag missing authority data.

**Rollback:** N/A — SAFER outages are external. Operator decision to either wait or temporarily allow `vetting_gate_relax=true` (NOT recommended — violates broker compliance posture).

---

### 2.7 Carrier acceptance flow stuck (token expired / magic-link broken)

**Symptom:** Carrier reports they clicked the accept-load link but it shows "Link expired" or "Invalid token." Load stays in `pending_acceptance` indefinitely. P&L queue fills with stale offers.

**Diagnostic:**
```powershell
mongo --eval "db.loads.find({status:'pending_acceptance', updated_at:{`$lte:new Date(Date.now()-3600*1000).toISOString()}}).count()"
```
- Lots of stale: tokens are expiring before carriers click. Likely a TTL misconfig.

```powershell
mongo --eval "db.magic_link_tokens.find({used:false,expires_at:{`$lte:new Date().toISOString()}}).count()"
```

**Resolution:**
1. **For a SPECIFIC carrier** who reports a broken link:
   1. **Open** `https://localhost:3000/admin/loads` in the operator UI.
   2. **Locate** the load by carrier MC + load_id.
   3. **Click** "Regenerate Accept Link."
   4. **Send** new link via the carrier's preferred channel (SMS or email).
2. **For widespread** (multiple carriers in a single hour):
   1. **Check** `db.magic_link_tokens` for a TTL collapse — `expires_at` minus `created_at` should be ~24h.
   2. **If TTL is too short:** edit `MAGIC_LINK_TTL_HOURS` in `.env` via `set_env_var.ps1`, restart backend.
3. **For "carrier never received the link":** see section 2.4 (SMS failure path).

**Rollback:** if `MAGIC_LINK_TTL_HOURS` change made it worse (e.g., set to 0), revert to `24` and restart.

---

### 2.8 Funding gate refusing all loads (GPS not pinging)

**Symptom:** All carrier dispatches refuse to advance past `loaded` state. Logs show `funding_gate_block: gps_pings missing`. Drivers report "the app says it's tracking but the broker keeps asking where I am."

**Context:** Per `project_pwa_is_primary_tracking_no_eld_vendor.md`, CHL accepts ~25% PWA failure rate. But if EVERY driver suddenly fails to ping, the issue is server-side, not driver-side.

**Diagnostic:**
```powershell
# Ping volume in the last hour:
mongo --eval "db.gps_pings.find({ts:{`$gte:new Date(Date.now()-3600*1000).toISOString()}}).count()"

# Per-load most-recent ping age:
mongo --eval "db.loads.find({status:'in_transit'}).forEach(l => print(l._id + ': ' + (l.last_gps_ping_ts || 'NEVER')))"
```
- Zero recent pings across all loads → server-side. Check WebSocket / PWA backend route.
- Some loads pinging, others not → driver-side (phone died, app closed) — acceptable per the 25% rule.

**Resolution:**
1. **If server-side (zero pings):**
   1. **Check** `/api/health/system` `modules.pwa_tracking` block.
   2. **Restart** `CHL-Backend` (PWA WebSocket endpoint lives in the backend monolith).
   3. **Verify** with `curl http://localhost:8000/api/pwa/health` (should return `{listening:true}`).
2. **If per-driver:** operator-call the driver, walk them through: open the CHL PWA → tap location icon → confirm permission granted → keep browser tab open.
3. **If a specific load is funding-blocked but driver IS pinging:** the `last_gps_ping_ts` index may be stale. Run:
   ```
   mongo --eval "db.loads.updateOne({_id:'<load_id>'},{`$set:{last_gps_ping_ts:new Date().toISOString()}})"
   ```
   Use sparingly — this is a manual override that should be audited.

**Rollback:** the funding gate is a safety mechanism (per `project_network_control_strategy_2026_05_09.md`, never book at a loss; funding requires confirmed in-transit). DO NOT disable the funding gate at the policy level. Per-load manual override (above) is the correct escape valve.

---

### 2.9 RED throttle state stuck

**Symptom:** Throttle status shows `RED` (accepting 0% of loads). Operator gets SMS "throttle RED: <reason>". Quote engine refuses to price new loads.

**Diagnostic:**
```powershell
curl -u "$env:CHL_OWNER_EMAIL`:$env:ADMIN_PASSWORD" http://localhost:8000/api/throttle/status | ConvertFrom-Json
curl -u "$env:CHL_OWNER_EMAIL`:$env:ADMIN_PASSWORD" http://localhost:8000/api/throttle/history | ConvertFrom-Json | Select-Object -First 5
```

Look at `last_metrics.error_rate`, `last_metrics.p95_latency_ms`, and `current_state_reason`. The throttle transitions to RED on:
- `error_rate >= 20%` over rolling 1h
- `unhealthy_count > 4` health-check modules
- Any module in `THROTTLE_CRITICAL_MODULES` reporting unhealthy
- Aggregate P&L 24h net < -1% (loss-floor auto-throttle)

**Resolution:**
1. **Identify** the root trigger from `current_state_reason`.
2. **Fix the underlying** issue (a SAFER outage will resolve when SAFER comes back; a memory leak needs a backend restart).
3. **Auto-recovery:** the throttle steps DOWN one state level every `RECOVERY_WINDOW_SEC` (default 15 min) once metrics are "good enough." Let it recover naturally if the root cause is fixed.
4. **Manual override** (owner-only, last resort):
   ```powershell
   curl -X POST -u "$env:CHL_OWNER_EMAIL`:$env:ADMIN_PASSWORD" `
        -H "Content-Type: application/json" `
        -d '{"force_state":"GREEN","duration_min":30,"reason":"manual recovery"}' `
        http://localhost:8000/api/throttle/override
   ```
5. **Clear override:**
   ```powershell
   curl -X DELETE -u "$env:CHL_OWNER_EMAIL`:$env:ADMIN_PASSWORD" http://localhost:8000/api/throttle/override
   ```

**Rollback:** if a manual override masks an actual production fire, the loss-floor rule kicks in and re-fires RED. Trust the throttle; do not chain overrides.

---

### 2.10 Out of disk space on C: drive

**Symptom:** Backend logs show write failures. Mongo journal complains. `Get-PSDrive C` shows < 10GB free (hard-stop threshold).

**Diagnostic:**
```powershell
Get-PSDrive C | Format-Table Name, @{N='FreeGB';E={[math]::Round($_.Free/1GB,2)}}
# Find the biggest space consumers:
Get-ChildItem C:\ -Directory | ForEach-Object {
  $size = (Get-ChildItem $_.FullName -Recurse -ErrorAction SilentlyContinue | Measure-Object -Property Length -Sum).Sum
  [PSCustomObject]@{ Path = $_.FullName; SizeGB = [math]::Round($size/1GB, 2) }
} | Sort-Object SizeGB -Descending | Select-Object -First 10
```

**Resolution:**
1. **Run** `Get-PSDrive C` to confirm current free space.
2. **Prune backend logs:**
   ```powershell
   Get-ChildItem C:\CHL\backend\logs\*.log -ErrorAction SilentlyContinue |
     Where-Object { $_.LastWriteTime -lt (Get-Date).AddDays(-7) } | Remove-Item -Force
   ```
3. **Prune frontend logs** same pattern under `C:\CHL\frontend\logs\`.
4. **Empty Recycle Bin:** `Clear-RecycleBin -Force -ErrorAction SilentlyContinue`.
5. **Prune old D-drive 5-copy floors** if they accidentally went onto C: — they should be on D: only.
6. **Re-check** `Get-PSDrive C` → must be > 20GB free before declaring resolved.

**Rollback:** if you delete the wrong files, restore them from the D-drive rolling snapshot (section 4). Never delete `C:\CHL\backend\.env` or `C:\CHL\vault_files\` — those don't live in git.

---

### 2.11 Vault encryption key lost (catastrophic)

**Symptom:** Vault decrypt operations fail with `cryptography.fernet.InvalidToken`. `CHL_SECRETS_MASTER_KEY` env var is missing or corrupted.

**Resolution:** This is **catastrophic** — without the master key, vault entries and vault documents cannot be decrypted. The key value itself is NOT backed up to D-drive (intentionally — secrets local-only per `feedback_secrets_local_only_no_bridge_transmission.md`).

**Operator action:**
1. **Stop** any further automation immediately: from section 3 — `kill_switch.disarm_all()`.
2. **Check** `C:\CHL\backend\.env` for `CHL_SECRETS_MASTER_KEY=` — if it's there but corrupted (extra chars / truncated), restore from your offline note storage.
3. **If the key is genuinely lost:** all vault entries are unrecoverable. The recovery path is:
   1. Generate a new Fernet key.
   2. Provision via `set_env_var.ps1 -Key CHL_SECRETS_MASTER_KEY -Value "<new key>"`.
   3. Re-onboard all vendor credentials manually (Twilio, Plivo, Telnyx, Stripe, Mercury, FMCSA WebKey).
   4. Vault documents become unreadable — operator must re-upload.
4. **DM `@pm-lead`** with a CRITICAL alert; this is also a regulatory issue (re-auth all carrier-shared secrets).

**Rollback:** N/A. The vault is the trust root. If lost, there is no rollback — only recovery via re-provisioning.

**Prevention:** store the Fernet master key in a password manager (1Password / Bitwarden) the moment it's generated. Operator-confirmed this is the only acceptable out-of-band storage.

---

## 3. Kill switches reference

**Source:** `C:\CHL\backend\kill_switch.py`. Stored in `db.kill_switch` doc `_id=global`. Owner-only.

### What the flags do

| Flag | What it stops |
|---|---|
| `autobid_disarmed` | Outbound autobid (`outbound_autobid.maybe_fire_autobid`) — no auto-quotes leave the platform |
| `hunter_disarmed` | Capacity hunter scan cycle — no proactive carrier reach-outs |
| `market_intel_disarmed` | Market-intelligence sub-agent dispatches — no external API/scraper polling |

### Emergency disarm-all

```powershell
curl -X POST -u "$env:CHL_OWNER_EMAIL`:$env:ADMIN_PASSWORD" http://localhost:8000/api/kill-switch/disarm-all
```

This flips all three flags to `true`, fires a CRITICAL operator alert (voice + SMS), and broadcasts the state to all connected frontend tabs (red banner appears immediately).

**When to use:**
- Twilio / Plivo bill spike detected (runaway SMS).
- Loss-floor breach not auto-caught by throttle.
- Vault compromise suspected (any chance secrets in transit).
- "Something is very wrong and I need automation to stop NOW."

### Per-flag toggle

```powershell
curl -X PUT -u "$env:CHL_OWNER_EMAIL`:$env:ADMIN_PASSWORD" `
     -H "Content-Type: application/json" `
     -d '{"autobid_disarmed":true,"reason":"twilio spend spike"}' `
     http://localhost:8000/api/kill-switch
```

### Re-arm after kill

```powershell
curl -X POST -u "$env:CHL_OWNER_EMAIL`:$env:ADMIN_PASSWORD" http://localhost:8000/api/kill-switch/rearm-all
```

This flips all three flags to `false`. **Before re-arming:**
1. **Confirm** the underlying issue is resolved (whatever made you disarm).
2. **Run** the section 1 first-5-minutes checklist again — all PASS before re-arm.
3. **Check** `db.operator_alerts` for any open CRITICAL rows.
4. **Re-arm.**
5. **Watch** `/api/throttle/status` for 5 minutes — should stay GREEN or step up from YELLOW naturally.

### Kill switches that are NOT in kill_switch.py

- **Throttle override** (section 2.9): forces a state level for a fixed duration.
- **Carrier outreach blast pause:** stop the cron via Windows Task Scheduler if a TCR/A2P campaign is rejected and you don't want to keep firing failed sends.
- **Factor submission pause:** edit `FACTOR_AUTO_SUBMIT_ENABLED=false` in `.env` via `set_env_var.ps1` + restart. Use this if the factor (revenue source) reports a system issue with submissions.

---

## 4. Restore-from-backup procedure

### 4.1 Where backups live

| Tier | Path | Cadence | Contents |
|---|---|---|---|
| **Rolling (most recent)** | `D:\CHL_backups\rolling\` | Every commit + 3x/session minimum | git bundles (CHL.bundle + chl-memory.bundle), Claude memory zip, commits.log |
| **5-copy floor (current full copies)** | `D:\CHL_backups\5copy_floor_a/b/c/d_<timestamp>\` | Auto-rotate when oldest > 24h | Full snapshot of `C:\CHL\` excluding `node_modules/`, `__pycache__/`, `.venv/` |
| **Iter-close** | `D:\CHL_backups\iter_<X_Y>_close_<timestamp>\` | At iter-close ceremony | Full snapshot + mongo dump + .env (encrypted) + manifest |
| **Pre-purge** | `D:\CHL_backups\pre_purge_<timestamp>\` | Before any large prune op | Full snapshot pre-prune |
| **GitHub remotes** | `github.com/...` | Every push | CHL repo + chl-memory repo |

**Trusted copy hierarchy:** for code, the OLDEST 5-copy floor that is still ≤72h old is "most trusted" (proven-stable). The rolling bundle is "most recent" but may include a bad commit. For mongo, the most recent iter-close dump is trusted.

### 4.2 Restore code (CHL backend/frontend)

**From git bundle (preferred):**
1. **Move aside** the broken `C:\CHL\` (do NOT delete): `Rename-Item C:\CHL C:\CHL_broken_$(Get-Date -Format yyyyMMddHHmm)`.
2. **Clone** from bundle: `git clone D:\CHL_backups\rolling\CHL.bundle C:\CHL`.
3. **Restore** `.env`: copy from iter-close manifest (the rolling backup does NOT include `.env` for security).
4. **Install deps**: `cd C:\CHL\backend; py -m pip install -r requirements.txt`.
5. **Install frontend deps**: `cd C:\CHL\frontend; npm install`.
6. **Restart-Service CHL-Backend, CHL-Frontend, MongoDB**.

**From 5-copy floor (file-level restore):**
1. **Identify** which floor copy: `Get-ChildItem D:\CHL_backups\5copy_floor_* -Directory | Sort-Object LastWriteTime -Descending`.
2. **Pick** the second-oldest (the oldest may be partial / pre-rotation).
3. **Copy** specific files: `Copy-Item D:\CHL_backups\5copy_floor_b_<timestamp>\backend\<file> C:\CHL\backend\<file>`.

### 4.3 Restore Mongo

**From iter-close dump:**
1. **Pick the dump**: `Get-ChildItem D:\CHL_backups\iter_*_close_*\mongo_dump -Directory`.
2. **Spin up an isolated test mongo first** to validate the dump before touching production:
   ```powershell
   # Test mongo on port 27018 (separate from production 27017)
   mongod --dbpath C:\CHL_test_mongo --port 27018 --fork --logpath C:\temp\test_mongo.log
   mongorestore --port 27018 --drop D:\CHL_backups\iter_<X_Y>_close_<ts>\mongo_dump
   mongo --port 27018 --eval "db.getSiblingDB('chl').loads.count()"  # sanity check
   ```
3. **If the test restore looks good**, restore to production:
   ```powershell
   Stop-Service CHL-Backend  # so backend doesn't write during restore
   mongorestore --drop D:\CHL_backups\iter_<X_Y>_close_<ts>\mongo_dump
   Start-Service CHL-Backend
   ```
4. **Verify** with `/api/health/system` + a couple of UI screens.

### 4.4 Restore chl-memory

```powershell
cd C:\CHL\chl-memory
git fetch D:\CHL_backups\rolling\chl-memory.bundle main:bundle_recovery
git checkout bundle_recovery
git push origin main --force-with-lease  # ONLY if intentional rollback
```

---

### 4.5 How to test the restore procedure quarterly

**Operator-mandated 2026-05-10 (pre-May-14 launch readiness).** The restore procedure in 4.1–4.4 is only as good as the last time it was actually proven. Untested backups are wishes. Run the restore validator on the cadence below, and BEFORE any major mongo migration.

**Tooling:**
- `C:\CHL\scripts\test_restore_from_backup.ps1` — orchestrator (validates git bundle + vault files + dispatches Python validator).
- `C:\CHL\scripts\test_restore_validators.py` — Python validator (does the mongo restore + count check + decrypt verification).

**When to run:**
- Quarterly (minimum) — calendar reminder.
- Before any major mongo migration (Atlas move, schema reshape, big index rebuild).
- After any change to `snapshot_to_d.ps1` or `vault_metadata_dump.py`.
- After a vault-encryption-key rotation (proves the new key decrypts the latest backup).
- During incident response if section 4.3 mongo restore is being considered — validate the dump FIRST against the test mongo before touching production.

**One-time setup (per workstation): standalone test mongo on port 27018**

1. **Stop** any existing test mongo: `Stop-Process -Name "mongod_test" -ErrorAction SilentlyContinue`.
2. **Create** data dir: `New-Item -ItemType Directory -Force -Path C:\CHL_test_mongo\data`.
3. **Start** a separate mongo instance on port 27018 (does NOT conflict with live :27017):
   ```powershell
   mongod --dbpath C:\CHL_test_mongo\data --port 27018 --logpath C:\CHL_test_mongo\test.log --fork
   ```
   On Windows without `--fork`, run in a separate PowerShell window:
   ```powershell
   mongod --dbpath C:\CHL_test_mongo\data --port 27018 --logpath C:\CHL_test_mongo\test.log
   ```
4. **Verify**: `mongo --port 27018 --eval "db.adminCommand({ping:1})"` → returns `{ok:1}`.

**Running the test:**

```powershell
# Option A: full test (requires the standalone mongo above to be running)
$env:MONGO_TEST_URL = "mongodb://localhost:27018"
$env:DB_TEST_NAME   = "chl_restore_test"
cd C:\CHL\scripts
.\test_restore_from_backup.ps1

# Option B: auto-detect (uses :27018 if present, falls back to dry-run if not)
.\test_restore_from_backup.ps1 -Auto

# Option C: dry-run only (validates files + decrypt path, skips mongo restore)
.\test_restore_from_backup.ps1 -DryRun
```

**Safety guarantees (defense-in-depth — codified into the scripts):**
1. Script REFUSES to run if `MONGO_TEST_URL == MONGO_URL` (would target live mongo).
2. Script REFUSES to run if `DB_TEST_NAME == DB_NAME` (would drop live db).
3. Script REFUSES if `MONGO_TEST_URL` or `DB_TEST_NAME` is empty (in full mode).
4. Even `--dry-run` rejects misconfigured TEST==LIVE pairs.
5. ALL audit rows go to `db.restore_test_audit` in the TEST mongo, NEVER live.

**Expected results (PASS):**
```
[PASS] git_bundle_readable       (  0.1s) main=709c79962174
[PASS] git_bundle_freshness      (  0.0s) bundle is 0.8h old
[PASS] vault_files_present       (  0.0s) 14 .enc files
[PASS] vault_metadata_present    (  0.0s) 4 collection JSONs + manifest present
[PASS] python_validator_full     (  1.2s) exit=0
RESULT:      PASS
```

**Failure modes + escalation:**

| Failure | Exit code | What it means | Action |
|---|---|---|---|
| `git_bundle_readable` FAIL | 1 | `CHL.bundle` missing or corrupt | Re-run `scripts\snapshot_to_d.ps1`; check D: drive health (`Get-PSDrive D`). |
| `git_bundle_freshness` WARN | 0 (warn only) | Bundle is >24h old | Soft signal that snapshot cron isn't firing — check Task Scheduler, but restore is still valid. |
| `vault_files_present` FAIL | 2 | `.enc` blobs missing from D: | Re-run snapshot. If still missing, the snapshot script's vault-file-copy step has regressed — investigate `snapshot_to_d.ps1`. |
| `vault_metadata_present` FAIL | 2 | JSON dumps missing or `_manifest.json` absent | Re-run `vault_metadata_dump.py` standalone to repopulate, then re-snapshot. |
| Python validator exit 4 (decrypt failed) | 2 (mapped) | `VAULT_ENCRYPTION_KEY` doesn't match the key used to encrypt the backed-up blobs | CATASTROPHIC — see section 2.11. Either the env key was rotated without re-encrypting, OR the blobs were tampered with. Immediately verify `$env:VAULT_ENCRYPTION_KEY` matches the one in your password manager. |
| Python validator exit 3 (mongo unreachable) | 3 | Test mongo on :27018 isn't responding | Restart the test mongod (setup step 3). Confirm it's on :27018 not :27017. |
| Python validator exit 1 (count mismatch) | 3 | Source JSON has N rows but restore inserted M ≠ N | Either the source JSON is malformed (re-run dump) OR a row failed BSON validation on insert (check restore log for the bad row). |
| Python validator exit 2 (safety violation) | 4 | Env-var differ check failed at the Python layer | The PS layer should have caught this; if it reaches Python, you have an env-var override happening mid-run. Audit the call chain. |

**On any FAIL: do NOT trust the rolling backup until the failure is resolved + the test passes.** The 5-copy floor (`D:\CHL_backups\5copy_floor_a/b/c/d`) is the operational fallback during a backup outage. The iter-close manifests are the long-term archive.

**Audit trail:** every successful run writes a row to `db.restore_test_audit` in the test mongo with `{ts, mode, git_bundle_ok, vault_blob_count, vault_metadata_restored_counts, decrypt_ok, duration_sec, error_or_null}`. Reviewable for quarterly compliance evidence.

**Cadence reminder:** add a calendar entry titled "CHL backup restore test — quarterly" with first occurrence 2026-08-10 (3 months out from this build). Repeating quarterly.

---

## 5. Working in test mode safely

Operator-mandated 2026-05-10. Synthetic load tests + smoke tests must NEVER write to the live production DB (`chl_local`). A `CHL_ENV=test` mode points the backend at an isolated test DB (`continental_haul_test`) so tests can write freely without touching production data.

### 5.1 Quick reference — the env vars

| Env var | Prod value | Test value | Notes |
|---|---|---|---|
| `CHL_ENV` | unset or `prod` | `test` | Active env. Default `prod`. |
| `DB_NAME` | `chl_local` | `chl_local` | Production DB name. Untouched by test mode. |
| `DB_TEST_NAME` | (optional) | `continental_haul_test` | Test DB. MUST differ from `DB_NAME` (refuse-to-boot guard fires otherwise). |
| `MONGO_URL` | `mongodb://localhost:27017` | `mongodb://localhost:27017` OR `mongodb://localhost:27018` | Same mongod for partial isolation, separate one for full isolation. |

**Refuse-to-boot guards** (defense in depth, all live in `backend/server.py:29`-area):
- Both `DB_NAME` and `DB_TEST_NAME` missing → `RuntimeError`
- `CHL_ENV=test` but `DB_TEST_NAME` unset → `RuntimeError`
- `CHL_ENV=test` but `DB_TEST_NAME == DB_NAME` → **catastrophic-misconfig** `RuntimeError`

The same three guards run in `backend/conftest.py` before any pytest test executes, so tests can't accidentally point at prod either.

### 5.2 Switch the backend to test mode

Two isolation levels — pick based on test scope.

**Partial isolation** (simpler, fast, OK for unit tests):
```powershell
# Reuses the existing :27017 mongod; separates by DB name only.
.\scripts\setup_test_mongo.ps1 -Mode partial
Restart-Service CHL-Backend
```

**Full isolation** (recommended for production-level testing, restore drills, bulk synth-load):
```powershell
# Stands up a SEPARATE mongod on :27018 with its own dbpath.
.\scripts\setup_test_mongo.ps1 -Mode full

# In a separate terminal (keep open), start the test mongod:
& 'C:\Program Files\MongoDB\Server\7.0\bin\mongod.exe' --dbpath 'D:\mongo-test' --port 27018

# Then restart the backend so it picks up the new MONGO_URL:
Restart-Service CHL-Backend
```

### 5.3 Seed baseline test data

After switching to test mode, populate the test DB with the seed baseline (10 carriers, 5 loads, 2 factors, 1 driver — all prefixed `TEST_*` for easy filtering):

```powershell
C:\CHL\.venv-local\Scripts\python.exe C:\CHL\scripts\seed_test_db.py
```

The script is **idempotent** (upserts by `id`) and **refuses to run** if `CHL_ENV != test` or if `DB_TEST_NAME == DB_NAME`.

### 5.4 Verify test mode is active

Sanity-check endpoint — confirms which DB the backend is actually pointing at right now:

```powershell
# Replace <owner_token> with a fresh owner access_token cookie.
curl http://localhost:8000/api/_env/info --cookie 'access_token=<owner_token>'
```

Expected response in test mode:
```json
{
  "env": "test",
  "db_name": "continental_haul_test",
  "mongo_url_redacted": "mongodb://localhost:27018",
  "started_at": "2026-05-10T...",
  "is_test": true
}
```

If `is_test=false` but you thought you were in test mode → the env vars didn't take effect. Restart the backend service and re-check.

### 5.5 Clean up test data

**Drop the entire test DB** (fastest; only do this when no test is running):

Partial isolation (shared :27017):
```powershell
mongosh --eval 'db.getSiblingDB("continental_haul_test").dropDatabase()'
```

Full isolation (:27018):
```powershell
mongosh --port 27018 --eval 'db.getSiblingDB("continental_haul_test").dropDatabase()'
```

**Drop only the seed-baseline rows** (preserves anything tests added):
```powershell
mongosh --eval 'use continental_haul_test; db.carriers.deleteMany({test_seed: true}); db.loads.deleteMany({test_seed: true}); db.factor_clients.deleteMany({test_seed: true}); db.drivers.deleteMany({test_seed: true})'
```

### 5.6 Revert to production mode

```powershell
.\scripts\set_env_var.ps1 -Name CHL_ENV -Value prod
# Optionally clear MONGO_URL back to :27017 if you used full isolation:
.\scripts\set_env_var.ps1 -Name MONGO_URL -Value 'mongodb://localhost:27017'
Restart-Service CHL-Backend
```

Verify via `GET /api/_env/info` — expect `is_test=false` + `db_name=chl_local`.

### 5.7 Synthetic-load production guard

`POST /api/admin/synthetic-load/create` is now **refused in production** unless the request body sets `allow_prod=true`. The default behavior matches operator intent: synthetic data should never bleed into production metrics by accident.

Error returned when `CHL_ENV != test` and `allow_prod` is unset:
```json
{
  "error": "synthetic_spawn_blocked_in_prod",
  "detail": "CHL_ENV != 'test'. Synthetic load creation is refused...",
  "current_env": "prod"
}
```

To deliberately spawn a synthetic load in prod (rare — used for live walkthrough demos):
```bash
curl -X POST http://localhost:8000/api/admin/synthetic-load/create \
  --cookie 'access_token=<owner_token>' \
  -H 'Content-Type: application/json' \
  -d '{"lane": {...}, "shipper_name": "...", "rate": 1500, "equipment": "dry_van", "target_stage": "lead", "allow_prod": true}'
```

### 5.8 What to do if a test accidentally writes to prod

This should be impossible with the guards in place, but if it ever happens:

1. **STOP** all running tests immediately (`Ctrl+C` the pytest process; `Stop-Service CHL-Backend` if uncertain which process wrote).
2. Inspect `db.<collection>` for rows where `test_seed: true` OR `synthetic: true` — these are the leaked rows.
3. Delete them: `db.<collection>.deleteMany({test_seed: true})` and `db.<collection>.deleteMany({synthetic: true})`.
4. Check the most-recent `handoff_*_close.md` to see if any production metrics rolled with the polluted data.
5. **Audit how the guard failed** — was `CHL_ENV` set? Was `DB_TEST_NAME` set? Was the test running against a stale backend that hadn't been restarted? File a follow-up to harden whichever check let it through.
6. Restore from the most recent rolling D-drive snapshot if production metrics were materially affected (Section 4.3).

---

## 6. Vendor escalation contacts

Per the no-fabrication rule, contact details are populated where they exist in chl-memory or codebase. Anything else is operator-to-populate.

| Vendor | Service | Contact path | Notes |
|---|---|---|---|
| **Twilio** | SMS / 10DLC | `https://console.twilio.com/` → top-right Help → "Get Support" | Owner-only login. Account SID + region required. |
| **Plivo** | SMS (migration target) | `https://support.plivo.com/` → "Submit a request" | Migration auth date 2026-05-07; account in setup. |
| **Telnyx** | TFV / FOC / 10DLC port | `https://portal.telnyx.com/` → bottom-right chat + `support@telnyx.com` | Port FOC date 2026-05-14. TFV field values in `chl-memory/operations/telnyx_tfv_field_values_2026_05_10.md`. |
| **Mercury Bank** | Banking | App → "Help" or `help@mercury.com` | TBD — operator to populate phone if escalated. |
| **FMCSA** | Broker authority / SAFER | `https://www.fmcsa.dot.gov/contact-us` — general 1-800-832-5660 (M-F 9-5 ET) | Broker MC 1817555 / USDOT 4569843. Login.gov + WebKey signup pending. |
| **Zoho** | Email / IMAP | `support@zohocorp.com` + chat in admin panel | Mailbox `dispatch@continentalhaul.com`. |
| **MongoDB local** | Database | Local install — no vendor support | Logs at `C:\Program Files\MongoDB\Server\<ver>\log\mongod.log`. |
| **MongoDB Atlas** | (when migrated) | `https://www.mongodb.com/cloud/atlas/support` | Not yet on Atlas. |
| **AWS / Cloud** | (when migrated) | TBD — cloud migration July-Sept 2026 | Hetzner candidate per hardware-assessment doc. |

**Signature for ALL vendor support tickets:** `Dispatch Team / Continental Haul Logistics LLC / MC 1817555 · USDOT 4569843` (from `dispatch@continentalhaul.com`).

---

## 7. Standing operator directives

These are load-bearing and quoted verbatim from operator instructions. Every incident response, handover doc, and runbook must carry them in full detail. No paraphrasing.

### Directive 1 — `set_env_var.ps1` helper

Operator's exact words:
> "I need you to operate more autonomously so to speak. I introduce way too many possible failures"

**Implementation:** `C:\CHL\scripts\set_env_var.ps1` (shipped 2026-05-08 EOD-6 commit `a7c59a6` stream E). When provisioning ANY secret (Twilio token, Plivo auth, Telnyx API key, FMCSA WebKey, JWT secret, vault Fernet key, etc.), use:

```powershell
cd C:\CHL\scripts
.\set_env_var.ps1 -Key <KEY_NAME> -Value "<the-value>"
# OR (for sensitive values staged in a file):
.\set_env_var.ps1 -Key <KEY_NAME> -ValueFile <path-to-temp-file>
```

The helper writes to `C:\CHL\backend\.env`, logs only the sha256[:16] hash (never the value), and notifies the backend to reload env. **Eliminated failure surfaces:** VS Code accidentally committing `.env`, line-ending corruption, missing quotes, partial-update overwrites.

**Rule:** when a secret needs provisioning during an incident, ALWAYS use this helper. Never paste secrets into VS Code, never echo them to bash history, never DM them via AgentDM.

### Directive 2 — 3x-daily handover discipline

Operator's exact words:
> "Make sure that this idea you have does not get lost. It has to be in each of your documeted handovers three times a day in full detail."

**Rule:** every handover document — including mid-incident write-ups — must contain this Standing Operator Directives section in FULL DETAIL with operator's exact words quoted. Paraphrasing is FORBIDDEN. Cadence: 3x per 5-hour active session (early ~1.5h / mid ~3h / close).

### Directive 3 — C-drive HDD monitoring

Operator's exact words:
> "You have space you can use on my C drive. Use it, but monitor its use so I dont go over my HDD capacity."

**Thresholds:**
- **Warn at 50GB free** — note in handoff.
- **Surface at 20GB free** — DM operator + start log prune.
- **Hard-stop at 10GB free** — disarm autobid + hunter + market_intel; do not allow any new write-heavy automation.

**Check command:**
```powershell
Get-PSDrive C
```

Every handover MUST capture this output.

### Directive 4 — Status-reporting synthesis

When PM Claude (or any source) relays an "operator directive" that contradicts a prior operator directive, the dev side surfaces the contradiction with a synthesis proposal. **DO NOT silently comply.**

**Specifically forbidden patterns to push back on:**
- "wait for operator acknowledgment before continuing" (contradicts autonomous-operation)
- "update status file after every commit" (contradicts iter-close cadence)
- "centralized file outside git" (contradicts version-control discipline)
- "backup to additional locations beyond D-drive + GitHub" (snapshot_to_d.ps1 + git push already cover C + D + cloud)

Surface with: "PM relayed X, prior directive said Y, proposed synthesis Z" — operator picks the path.

---

## 8. Communication during incident

### 8.1 When to DM `@pm-lead` via AgentDM

**DM immediately on:**
- Any CRITICAL operator alert (voice + SMS triggered).
- Kill-switch fired (disarm-all or per-flag).
- Throttle stuck in RED > 30 min.
- Vendor escalation submitted (Twilio / Plivo / Telnyx / FMCSA support ticket opened).
- Service down > 5 min (Backend / Frontend / Mongo).

**Mid-incident format** (one DM, tight):
```
@pm-lead INCIDENT <timestamp UTC>
Severity: HIGH|CRITICAL
Symptom: <one line>
Root cause: <one line or "investigating">
Action taken: <one line>
Current state: <one line>
ETA to resolve: <best estimate or "unknown">

Mission anchor: CHL platform readiness May 14. <current iter status>. <phase pointer>.
```

(Mission-anchor footer per `feedback_anchor_pm_lead_on_mission.md`.)

PM does NOT autopoll AgentDM — for time-critical visibility, also mirror to `chl-memory/progress/current_status.md` and push so PM can filesystem-read.

### 8.2 When to write a handoff doc mid-incident vs after

- **Mid-incident handoff** (write while still firefighting): only if the incident is going to span a session boundary (cap proximity, going to bed mid-fire) AND the next agent needs to pick up where you left off.
- **Post-incident handoff** (write after RTO): always. Append to the regular session-close doc OR write a dedicated `incident_<date>_<topic>.md` under `chl-memory/operations/` if material enough.

**Required incident-postmortem fields:**
- Detection time + how (which alert / which UI symptom).
- Root cause (verified, not just theorized).
- Restoration time + restore steps used.
- Customer impact (loads delayed, dispatches blocked, $).
- Prevention items: what monitor / circuit-breaker / kill-switch would have made this faster next time.

### 8.3 Outbound vendor email signature

When responding to vendor support tickets from `dispatch@continentalhaul.com`:

```
Dispatch Team
Continental Haul Logistics LLC
MC 1817555 · USDOT 4569843
```

Per `feedback_outbound_email_policy.md`: no AI-reveal, no fabricated personal stories, no autonomous money commitments, never lie if directly asked.

---

## Cross-references

- **Architecture:** `chl-memory/architecture/SYSTEM_MATRIX.md` (12-layer execution blueprint).
- **Pre-flight runbook:** `chl-memory/operations/may_14_platform_readiness_runbook.md` (the day-before checklist).
- **CHL SOP:** `chl-memory/operations/CHL_SOP_v1_2026_05_09.md`.
- **Boot protocol:** `C:\CHL\CLAUDE.md`.
- **Telnyx TFV field values:** `chl-memory/operations/telnyx_tfv_field_values_2026_05_10.md`.
- **Backup retrieval:** `chl-memory/operations/backup_retrieval_guide.md`.

**Document owner:** dev-engineer Claude (auto-updated when failure modes evolve; manual review at iter-close).
**Generated:** 2026-05-10.
