# May-14 Platform-Readiness Runbook

**Operator-mandated:** 2026-05-09 EOD-17-late.
**Halting context:** `~/.claude/projects/c--CHL/memory/project_may_14_platform_readiness_deadline.md`.
**Why:** May 14 is the Telnyx local 10DLC port FOC date for `+1-417-219-3856` (`sr_b98ea4`). When the number goes live + carriers/shippers come online, the platform must not be the bottleneck.

Operator's exact words:
> "What I want is the system to be ready and fully functional by the 14th if possible. I understand we are waiting for services to be connected and loadboard API keys etc..., but I dont want our limitation to operating this platform on the 14th. Does that make sense?"

This runbook is **self-contained**. The operator should be able to run it standalone on May 13-14 without re-reading other docs.

---

## 0. Scope summary

**In scope (must be TRUE by May 14):**
- All 25 recent CHL commits (TT through BBB) restart-verified in production.
- Critical-path smoke (load intake -> dispatch -> tracking -> invoice -> factor -> settlement) passes end-to-end.
- All operator surfaces render without console errors (Inbox / Renewals / Pipeline / Aggregates / Self-Heal / Reviews / Alerts / Cost Monitor).
- All 26 cron entries register with `loop_running=true`.
- Telnyx env vars provisioned post-port (TELNYX_FROM_NUMBER + TELNYX_TOLLFREE_NUMBER + TELNYX_WEBHOOK_SECRET).
- D-drive snapshot taken within 24h of May 14 capturing the full restart-verified state.

**Out of scope (deferred, do NOT block May-14 on these):**
- DAT/Truckstop live API integration (operator-blocked on signup/keys; mock paths active).
- FMCSA broker authority verification (operator-blocked on Login.gov + WebKey).
- Self-Heal Phase 3 *activation* (revenue-gated, not deadline-gated).
- Specialty hauler RSS scrape live wiring.
- Plivo deactivation (post-port + 1 week soak).
- Twilio account close (post-Plivo retire).
- A2P 10DLC re-registration (auto-triggered, 1-3 business days post-port).

---

## 1. Pre-restart preparation (operator runs day-before, May 13)

Run each item; check off when pass-criteria met.

- [ ] **Confirm 25-commit chain.** Run:
      ```
      cd C:\CHL
      git log --oneline -25
      ```
      Pass: top line shows `33c55a6 BBB4:` or later. Bottom line shows `f3e1c88 TT1+TT2+TT3:` (the start of the 25-commit batch). If HEAD is not `33c55a6` or descendant, STOP and page dev.

- [ ] **Run preflight.** Run:
      ```
      cd C:\CHL
      python scripts\preflight_server_smoke.py
      ```
      Pass: 0 NEW warnings beyond the 20 baseline missing-SDK ones (vendor SDKs not yet installed in venv -- documented baseline). Any NEW warning = STOP and investigate.

- [ ] **Pre-restart D-drive snapshot.** Run:
      ```
      cd C:\CHL
      pwsh -File scripts\snapshot_to_d.ps1
      ```
      Pass: command exits 0; `D:\CHL_backups\rolling\` mtime is within last 5 minutes.

- [ ] **Verify required env vars provisioned in `C:\CHL\backend\.env`.** Run:
      ```
      cd C:\CHL\backend
      Get-Content .env | Select-String -Pattern "^(TELNYX_API_KEY|STRIPE_API_KEY|STRIPE_WEBHOOK_SECRET|JWT_SECRET|ADMIN_PASSWORD|CHL_SECRETS_MASTER_KEY|MONGO_URL|DB_NAME|ANTHROPIC_API_KEY|GMAIL_SMTP_USER|GMAIL_SMTP_PASS|ZOHO_IMAP_PASSWORD|GITHUB_WEBHOOK_SECRET)=" | Measure-Object -Line
      ```
      Pass: line count is 13 (one per key). If a key is missing, provision via:
      ```
      cd C:\CHL\scripts
      .\set_env_var.ps1 -Key <KEY_NAME> -Value "<the-value>"
      ```
      Sensitive values can be staged in a temp file and consumed via `-ValueFile <path>` instead of `-Value` (helper hashes sha256[:16] for verification, never logs the value).

- [ ] **Capture C-drive free space.** Run:
      ```
      $d = Get-PSDrive C; "Free GB: {0:N2}" -f ($d.Free/1GB)
      ```
      Pass: > 50GB free (warn threshold). 20GB = surface; 10GB = hard-stop.

- [ ] **Confirm chl-memory is current.** Run:
      ```
      cd C:\CHL\chl-memory
      git fetch && git status
      ```
      Pass: "Your branch is up to date with 'origin/main'."

---

## 2. Restart sequence (admin-elevated PowerShell on host)

Operator pastes one command at a time. Wait for each to complete before the next.

1. **Confirm admin-elevated session.**
   ```
   $p = New-Object Security.Principal.WindowsPrincipal([Security.Principal.WindowsIdentity]::GetCurrent()); if ($p.IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)) { "ADMIN" } else { "standard - STOP and re-launch as Administrator" }
   ```
   Pass: prints `ADMIN`. If `standard`, close PowerShell, right-click PowerShell -> "Run as administrator", redo.

2. **List CHL services.**
   ```
   Get-Service | Where-Object {$_.Name -like "*CHL*"} | Format-Table -AutoSize
   ```
   Expected output:
   ```
   Status  Name         DisplayName
   ------  ----         -----------
   Running CHL-Backend  CHL Backend (uvicorn :8001)
   Running CHL-Frontend CHL Frontend (craco :3000)
   ```
   Service names are `CHL-Backend` and `CHL-Frontend`.

3. **Restart the backend (this is the load-bearing step).**
   ```
   Restart-Service -Name CHL-Backend -Force
   ```
   No output = success. Wait ~5 seconds.

4. **Confirm backend is Running.**
   ```
   Get-Service -Name CHL-Backend
   ```
   Pass: `Status` column = `Running`.

5. **Restart the frontend (rebuilds CRA bundle so newest BBB1+BBB2 tab code ships).**
   ```
   Restart-Service -Name CHL-Frontend -Force
   ```
   Wait ~30 seconds for CRA to recompile before browser-testing.

6. **Tail the most recent backend log to confirm clean boot.**
   ```
   Get-ChildItem C:\CHL\backend\logs -Filter "*.log" | Sort-Object LastWriteTime -Descending | Select-Object -First 1 | ForEach-Object { Get-Content $_.FullName -Tail 80 }
   ```
   Pass: Look for `Uvicorn running on http://0.0.0.0:8001`. No tracebacks. Look specifically for the AAA1 BSON-Date migration startup line: `migrate_stripe_dedup_first_seen_to_date` followed by `migrated=N` (first run) or `migrated=0` (subsequent runs).

---

## 3. Post-restart verification (12-point, ~10 minutes)

Each step has an exact command + a pass-criteria. Do NOT skip; each is a load-bearing assertion.

### 3.1 Service running
```
Get-Service CHL-Backend
```
Pass: `Status` = `Running`.

### 3.2 Health endpoint
```
curl.exe -s http://localhost:8001/api/health/system | python -m json.tool
```
Pass: JSON returns `"status": "ok"` with a module-health list under `checks` (audit_trail, budget_caps, circuit_breakers, operational_state, mongo_replica, etc.).

### 3.3 All 26 crons registered + loop_running=true
The `/api/health/system` payload includes per-cron health rows. Inspect:
```
curl.exe -s http://localhost:8001/api/health/system | python -c "import sys,json; d=json.load(sys.stdin); rows=[c for c in d.get('checks',[]) if 'loop_running' in str(c)]; print(f'cron rows: {len(rows)}'); [print(c.get('name'), c.get('healthy')) for c in d.get('checks',[]) if c.get('name','').endswith('_loop') or 'cron' in c.get('name','').lower()]"
```
Pass: 26 cron entries (CRON 1 through CRON 26 latest from BBB4 / `test_pass_rate sampler`). All `healthy: true`. Specifically: detention, redispatch, renewals, renewals_notifier, inbox_triage, carrier_tier_promoter, credit_override_revoker, factor_decline_handler, lane_eta, ci_runs_prune, api_audit_log_prune, cron_health_prune, sla_metrics_prune, stripe_event_dedup_prune, audit_trail, spend_forecasting (BBB3), test_pass_rate_sampler (BBB4).

### 3.4 TTL index provisioned (ZZ2)
```
mongo.exe chl_local --eval "JSON.stringify(db.stripe_event_dedup.getIndexes())"
```
(Use the actual `DB_NAME` from `.env` if not `chl_local`.)
Pass: one index with `"expireAfterSeconds": 604800` on `first_seen_at` (7-day TTL).

### 3.5 BSON Date migration ran (AAA1)
Re-tail logs (same command as step 2.6 above). Pass: line `migrate_stripe_dedup_first_seen_to_date ... migrated=N completed=true` is present. First-time migration shows `migrated=N` with N > 0 if any pre-AAA1 string-typed rows existed; subsequent restarts show `migrated=0`.

### 3.6 Security headers active (XX1)
```
curl.exe -I http://localhost:8001/api/health/system
```
Pass: Response headers include ALL of:
- `Strict-Transport-Security: max-age=...`
- `X-Frame-Options: DENY`
- `X-Content-Type-Options: nosniff`
- `Referrer-Policy: strict-origin-when-cross-origin`
- `Content-Security-Policy: <policy>`

### 3.7 Cost-Monitor dashboard (UU1)
1. Open browser to `http://localhost:3000/admin`.
2. Log in with Owner credentials.
3. Find the "Cost Monitor" widget on the admin dashboard.

Pass: Widget renders showing "Today / Week / Month" tiles + a "Projected month-end" line. No console errors (open DevTools, Console tab, look for red rows).

### 3.8 Self-Heal + Reviews + Alerts + PushQueueHealthTile render
Still in `/admin`:
- Click **Self-Heal** tab (BBB1+BBB2 bulk-aware updates) -> renders. No `TypeError`/`undefined` in console.
- Click **Reviews** tab (TT3+SS3 + YY2 operator-push tile) -> renders.
- Click **Alerts** tab -> renders. PushQueueHealthTile (BBB2) visible at top of UrgentAlerts page; shows "queued / inflight / failed" counts.
- Click **Cost Monitor** tab -> renders (widget from 3.7).

Pass: All four tabs paint with NO console errors. If any tab shows an error overlay, screenshot + page dev.

### 3.9 Inbox view (NN1)
1. In `/admin`, go to **Inbox** tab.
2. Confirm dispatch@continentalhaul.com mailbox lists rows.
3. Click into any one email.

Pass: List loads; clicked email body renders CSS-stripped (no inline `<style>` tags surviving). HTML body shows readable plain text. No console errors.

### 3.10 Renewals tab (KK1+LL3)
Pre-condition (operator-pending action): seed renewals if not yet seeded:
```
cd C:\CHL
python scripts\seed_renewals.py --apply
```
Then in `/admin`, click **Renewals** tab.

Pass: 34 baseline rows render (insurance + permits + state authorities + UCR). Each row shows due-date column. Filter chips work.

### 3.11 Stripe webhook readiness (YY3)
Owner cookie required (be logged in to /admin first). Hit:
```
curl.exe -s -b "<owner-session-cookie>" http://localhost:8001/healthz/stripe | python -m json.tool
```
Easier alternative: while logged in to /admin, open browser DevTools -> Network tab, then visit `http://localhost:8001/healthz/stripe` directly. Browser supplies the cookie automatically.

Pass: JSON includes `"status": "GREEN"`, `"dedup_ttl_index_present": true`, `"stripe_api_reachable": true`, `"vault_resolved": {"STRIPE_API_KEY": true, "STRIPE_WEBHOOK_SECRET": true, "STRIPE_PUBLISHABLE_KEY": true}`.

### 3.12 CI webhook endpoint live (BBB4)
```
curl.exe -s -X POST http://localhost:8001/api/_ci/github_actions_webhook -H "Content-Type: application/json" -d "{}" -o nul -w "%{http_code}"
```
Pass: prints `401`. Body (re-run without `-o nul`) shows `{"detail":"invalid_signature"}`. This is the CORRECT rejection -- HMAC is the auth, and an unsigned request must be refused.

---

## 4. Critical-path synthetic load test

Drives a single synthetic freight load through all 25 stages (RFQ -> dispatch -> tracking -> invoice -> factor -> settlement). Source: `backend\tests\synthetic_load_walkthrough.py` (extended this session with Mongo-fake `$type` operator support per AAA1).

**Run:**
```
cd C:\CHL
python -m pytest backend\tests\synthetic_load_walkthrough.py -v
```

Or use the smoke wrapper:
```
cd C:\CHL
pwsh -File scripts\run_walkthrough.ps1
```

**Expected output:**
```
test_01_rfq_intake                           PASSED
test_02_quote                                PASSED
test_03_carrier_match                        PASSED
test_04_rate_con                             PASSED
test_05_dispatch                             PASSED
test_06_check_call_gps                       PASSED
test_07_pod_upload                           PASSED
test_08_audit_gate                           PASSED
test_09_invoice                              PASSED
test_10_factor_request                       PASSED
test_11_payment                              PASSED
test_12_cancel_tonu                          PASSED
test_13_carrier_vetting                      PASSED
test_14_carrier_failure_redispatch           PASSED
test_15_realtime_pricing_rebid               PASSED
test_16_dispatch_packet_pdf                  PASSED
test_17_late_checkcall_escalation            PASSED
test_18_off_route_exception                  PASSED
test_19_detention_review                     PASSED
test_20_cargo_claim                          PASSED
test_21_aged_ar_escalation                   PASSED
test_22_carrier_tier_promotion               PASSED
test_23_shipper_credit_override_revocation   PASSED
test_24_predictive_eta_slip_reconciliation   PASSED
test_25_factor_approval_vs_decline           PASSED

25 passed in <T> s
```

Pass-criteria: 25/25 PASSED. Each stage SLA < 5 seconds. Total wall-clock < 30s. ANY failure = STOP, capture stage name + traceback, page dev.

---

## 5. Telnyx port-day actions (May 14 morning, operator)

Order matters. Run sequentially.

- [ ] **Confirm Telnyx FOC email arrived** for `sr_b98ea4` (local 10DLC, +1-417-219-3856). Subject usually: "Number port complete". If not received by 12:00 noon CT, log into Telnyx portal -> Numbers -> Port Requests -> check status.

- [ ] **Toll-free `sr_edc875` is NOT expected to be active yet.** Toll-free FOC realistic ETA is May 15 -> May 22 (7-14 day toll-free window). Do NOT block May 14 readiness on toll-free.

- [ ] **Provision local 10DLC env var.** Run:
      ```
      cd C:\CHL\scripts
      .\set_env_var.ps1 -Key TELNYX_FROM_NUMBER -Value "+14172193856"
      ```
      Pass: helper logs sha256[:16] of the value + writes to `C:\CHL\backend\.env`. NEVER prints the value itself.

- [ ] **Provision toll-free placeholder env var** (kept until toll-free FOC clears, then re-run with `+18664906433`):
      ```
      .\set_env_var.ps1 -Key TELNYX_TOLLFREE_NUMBER -Value "+18664906433"
      ```

- [ ] **Re-create voice-forwarding rule** for +1-417-219-3856 in Telnyx portal. Path:
      Telnyx Portal -> Voice -> Programmable Voice (or Call Control) -> Applications -> select / create app -> set webhook to your existing voice-forwarding flow OR set "Forward to PSTN" to the operator's destination number.

- [ ] **Submit Toll-Free Messaging Use Case form** in Telnyx portal. Path:
      Messaging -> Toll-Free Verification -> "New Submission" -> fill use-case (transactional dispatch + carrier/shipper notifications), brand info (Continental Haul Logistics LLC, MC 1817555, USDOT 4569843), expected volume.

- [ ] **Submit Hosted SMS request for toll-free** `+18664906433`. Path:
      Messaging -> Hosted SMS -> "New Request" -> upload v2 LoA (file: `C:\CHL\memory\CHL_Telnyx_LoA_TollFree_v2_2026_05_08.pdf`).

- [ ] **Re-upload v2 LoA to `sr_edc875`** if Telnyx rejected the v1. Path:
      Numbers -> Port Requests -> sr_edc875 -> Documents -> Upload -> select `C:\CHL\memory\CHL_Telnyx_LoA_TollFree_v2_2026_05_08.pdf`.

- [ ] **Restart backend after env var changes** (env vars are read at process start):
      ```
      Restart-Service -Name CHL-Backend -Force
      Get-Service CHL-Backend
      ```
      Pass: `Status: Running`.

- [ ] **Re-run health check 3.2 + 3.11** to confirm Telnyx-touching crons + Stripe webhook still GREEN after env-var change.

---

## 6. Smoke probe sequence post-Telnyx

Three test SMS commands using new `TELNYX_FROM_NUMBER`. Confirm Telnyx delivers and the inbound webhook lands a row in `db.inbound_messages`.

### 6.1 Outbound SMS test (operator's own cell)
Replace `+18165550100` with operator's actual cell number:
```
cd C:\CHL
python -c "from backend.notifications.telnyx_client import send_sms; print(send_sms(to='+18165550100', body='CHL outbound smoke test 1 - ' + __import__('datetime').datetime.utcnow().isoformat()))"
```
Pass: function returns `{"status": "queued", "message_id": "..."}` and operator's cell receives the SMS within 30 seconds.

### 6.2 Inbound SMS test (operator replies)
Operator replies to the SMS from 6.1 with text "TEST INBOUND".

After ~10 seconds, query Mongo:
```
mongo.exe chl_local --eval "JSON.stringify(db.inbound_messages.find().sort({_id:-1}).limit(1).toArray())"
```
Pass: one row with `from_number: "+18165550100"`, `to_number: "+14172193856"`, body containing "TEST INBOUND", `received_at` within last 60 seconds.

### 6.3 Webhook signature verification
Verify the inbound webhook hit `/api/webhook/telnyx_sms` with a valid signature (no `webhook_signature_invalid` log lines after the test):
```
Get-ChildItem C:\CHL\backend\logs -Filter "*.log" | Sort-Object LastWriteTime -Descending | Select-Object -First 1 | ForEach-Object { Select-String -Path $_.FullName -Pattern "telnyx.*signature|telnyx_sms" | Select-Object -Last 5 }
```
Pass: most-recent log entries show successful webhook handling, no `signature_invalid` or `unauthorized` entries.

---

## 7. Operator escalation triggers

Page dev (write a `~/.claude/projects/c--CHL/memory/escalation_<ts>.md` note OR DM via the bridge) immediately if ANY of these occur:

- [ ] ANY cron showing `loop_running=false` in `/api/health/system` post-restart (verification step 3.3).
- [ ] ANY 500 on operator surfaces (Inbox / Renewals / Pipeline / Aggregates / Self-Heal / Reviews / Alerts / Cost Monitor / Dashboard).
- [ ] Stripe webhook returning 4xx on real Stripe events (check `/healthz/stripe` periodically May 14-15).
- [ ] ANY `UnicodeError` / `ConnectionError` flood in `C:\CHL\backend\logs\*.log` (more than 5 occurrences within a 10-minute window).
- [ ] Synthetic load walkthrough (section 4) drops below 25/25 PASSED.
- [ ] Telnyx 6.1/6.2 smoke probes fail to deliver / fail to write inbound row to `db.inbound_messages`.
- [ ] Any `dedup_ttl_index_present: false` from `/healthz/stripe` after restart.

When paging, include:
1. Which step failed (step number from this runbook).
2. Exact command run.
3. Exact output (or screenshot for browser-side).
4. Most recent 50 lines of relevant log file.

---

## 8. Final D-drive snapshot (within 24h of May 14)

Captures the full restart-verified state for post-deadline rollback:
```
cd C:\CHL
pwsh -File scripts\snapshot_to_d.ps1
```
Pass: command exits 0; verify `D:\CHL_backups\rolling\` has a fresh timestamp.

---

## 9. Standing operator directives (always reference)

These apply to every session and every handoff doc. Quoted verbatim:

### Directive 1 -- `set_env_var.ps1` helper
> "I need you to operate more autonomously so to speak. I introduce way too many possible failures"

When provisioning a secret, ALWAYS use `C:\CHL\scripts\set_env_var.ps1` (built 2026-05-08 EOD-6 commit a7c59a6 stream E). NEVER ask the operator to edit `.env` manually in VS Code. Helper logs `sha256[:16]` for verification but never the value. Eliminated failure surfaces: literal-paste-as-value, char-truncation, save-not-saved.

### Directive 2 -- 3x-daily handover discipline
> "Make sure that this idea you have does not get lost. It has to be in each of your documeted handovers three times a day in full detail."

Every handover doc must contain this Standing-directives section in FULL DETAIL with operator's exact words. Paraphrasing is forbidden.

### Directive 3 -- C-drive HDD monitoring
> "You have space you can use on my C drive. Use it, but monitor its use so I dont go over my HDD capacity."

Thresholds: warn at 50GB free, surface at 20GB free, hard-stop at 10GB free.
Current state (captured at runbook authorship 2026-05-09): **Free GB 792.95 / Used GB 137.18** -- well above warn threshold.

### Status-reporting synthesis directive
When PM Claude relays an "operator directive" that contradicts a prior operator directive (most common: ack-wait-gate vs autonomous-operation), surface the contradiction with a synthesis to operator. Specifically forbidden patterns to push back on: "wait for operator acknowledgment before continuing" / "update status file after every commit" / "centralized file outside git" / "backup to additional locations". DO NOT silently comply.

---

## 10. Rollback path (if May-14 readiness fails)

If verification 3.x or section 4 walkthrough breaks AND a fix is not feasible before May 14 12:00 noon CT:

1. Identify the offending commit by walking `git log --oneline -25` against the verification step that failed.
2. Revert via:
   ```
   cd C:\CHL
   git revert --no-edit <SHA>
   git push origin main
   Restart-Service -Name CHL-Backend -Force
   ```
3. Re-run section 3 verification + section 4 walkthrough.
4. DM @pm-lead via AgentDM with revert SHA + reason.
5. File a `~/.claude/projects/c--CHL/memory/escalation_<ts>.md` for next-session pickup.

The 25-commit chain is INDEPENDENT enough that single-commit revert is safe; cross-commit dependencies are documented in each commit's body.

---

**End of runbook.** Self-contained -- operator runs sections 1 -> 2 -> 3 -> 4 May 13, then sections 5 -> 6 -> 8 May 14 morning. Section 7 escalation list applies throughout. Section 9 directives apply always.
