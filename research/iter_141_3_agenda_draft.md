# Iter 141.3 Agenda Draft (Twilio → Plivo Migration)

> **Iteration goal:** Migrate SMS provider from Twilio to Plivo to resolve 4+ weeks of 10DLC compliance friction. Restore full SMS functionality (voice-only alerts shipped in iter 141.2).
> **Timeline:** 3 weeks total (async to iter 141.2 stage 1d/1f execution)
> **Decision locked:** PLIVO selected for white-glove 10DLC migration service

---

## Pre-Flight (Operator, before stage 1a)

**Prerequisites:**
- [ ] Iter 141.2 shipped (DAT live + auto-bid backend + voice-only alerts working)
- [ ] Operator confirms external paperwork status: Do signed broker-carrier agreements cite +14172193856? (Yes = port numbers, No = instant new number option)
- [ ] Plivo account signup complete (free $10 credits, no setup fees): https://console.plivo.com/accounts/register/
- [ ] Vault entry created: "Plivo — Auth Credentials" (will populate in stage 1a)

---

## Stage 1a: Plivo Account Setup + Profile/Brand Registration

**Goal:** Complete Plivo onboarding + register Continental Haul Logistics LLC as 10DLC brand with white-glove assistance kickoff.

**What operator does (30 min hands-on):**

1. **Log into Plivo console:** https://console.plivo.com
2. **Navigate to 10DLC page:** Settings → A2P 10DLC
3. **Create Profile:**
   - Business type: Private (LLC)
   - Legal name: Continental Haul Logistics LLC
   - MC-1817555 + USDOT number
   - Authorized contact: Jason Aaron Meyer + email + phone
   - EIN: (from business registration docs)
   - Business address: (registered LLC address)
4. **Register Brand:**
   - Profile created → Click "Register Brand"
   - Standard brand: $4 one-time fee (charged to Plivo account)
   - Optional vetting: SKIP (CHL messaging volume <6,000/day at v1 scale)
   - Submit brand registration
   - **Kickoff white-glove service:** Contact Plivo support via console chat: "Migrating from Twilio, need white-glove 10DLC campaign assistance for broker operational SMS. MC-1817555." Solutions engineer will respond within 24h with migration checklist.
5. **Store credentials in vault:**
   - Navigate to Account → API Credentials
   - Copy Auth ID + Auth Token
   - Vault entry: "Plivo — Auth Credentials" → paste both values
6. **Provision phone number (temp for testing):**
   - Navigate to Phone Numbers → Buy Numbers
   - Search: US local number, SMS-enabled, any area code
   - Rent 1 number (~$0.80/month) for empirical smoke tests (stage 1b/1d)
   - Note number in `.env` prep doc

**What Dev Engineer does (15 min):**

1. Build `.env.plivo_migration_prep` template:
   ```
   # Plivo credentials (populate from vault after stage 1a)
   PLIVO_AUTH_ID=<from vault>
   PLIVO_AUTH_TOKEN=<from vault>
   PLIVO_PHONE_NUMBER=<temp number from stage 1a provisioning>
   OWNER_SMS_NUMBER=+18165609180  # unchanged
   ```
2. **Smoke battery (6 tests):**
   1. Profile creation success (response code 201)
   2. Brand registration submitted (status: PROCESSING)
   3. Auth credentials valid (API ping test)
   4. Temp number provisioned + SMS-enabled
   5. Plivo console accessible
   6. White-glove support contact initiated

**Success criteria:**
- ✅ Profile + Brand registered (brand status: PROCESSING or COMPLETED)
- ✅ Plivo support ticket open (white-glove migration assistance)
- ✅ Temp number provisioned
- ✅ Credentials in vault

**Estimated time:** 45 min (30 operator + 15 dev)

**Handoff doc:** `memory/handoff_iter_141_3_stage_1a.md`

---

## Stage 1b: notification_service.py Rewrite (Twilio → Plivo SDK)

**Goal:** Swap SMS client from Twilio to Plivo with empirical smoke test confirming delivery.

**What Dev Engineer does (30 min):**

1. **Install Plivo SDK:**
   ```powershell
   & "C:\CHL\.venv-local\Scripts\pip.exe" install plivo
   ```
2. **Rewrite notification_service.py:**
   - Replace `from twilio.rest import Client` with `import plivo`
   - Update `send_sms()` function:
     ```python
     # OLD (Twilio)
     client = Client(TWILIO_ACCOUNT_SID, TWILIO_AUTH_TOKEN)
     message = client.messages.create(
         to=recipient,
         from_=TWILIO_PHONE_NUMBER,
         body=text
     )
     
     # NEW (Plivo)
     client = plivo.RestClient(auth_id=PLIVO_AUTH_ID, auth_token=PLIVO_AUTH_TOKEN)
     response = client.messages.create(
         dst=recipient,    # "dst" not "to"
         src=PLIVO_PHONE_NUMBER,  # "src" not "from_"
         text=text         # "text" not "body"
     )
     ```
   - Update `.env` (rename Twilio vars to archive, activate Plivo):
     ```
     # Twilio (archived - kept for 30-day rollback window)
     # TWILIO_ACCOUNT_SID=AC...
     # TWILIO_AUTH_TOKEN=...
     # TWILIO_PHONE_NUMBER=+14172193856
     
     # Plivo (active)
     PLIVO_AUTH_ID=<from vault>
     PLIVO_AUTH_TOKEN=<from vault>
     PLIVO_PHONE_NUMBER=<temp number from stage 1a>
     ```
3. **Restart CHL-Backend:** `Restart-Service CHL-Backend`
4. **Empirical smoke test:**
   ```powershell
   & "C:\CHL\.venv-local\Scripts\python.exe" -c "from notification_service import send_sms; send_sms('+18165609180', 'Plivo migration smoke test - stage 1b')"
   ```
   - Check operator phone: SMS should deliver from Plivo temp number
   - Plivo console: Messages → Sent Messages → verify delivery status "delivered"

**Smoke battery (8 tests):**
1. Plivo SDK imports successfully
2. Client initialization (auth valid)
3. SMS send (temp number → OWNER_SMS_NUMBER)
4. Delivery confirmation (status: delivered in console)
5. Error handling (invalid dest → graceful fail)
6. Twilio SDK removed from imports (no orphaned deps)
7. `.env` Plivo vars populated + Twilio vars archived
8. Backend restart successful (health check green)

**Success criteria:**
- ✅ SMS delivers to +18165609180 from Plivo temp number
- ✅ notification_service.py rewrite complete
- ✅ All 8 smoke tests PASS

**Estimated time:** 30 min

**Handoff doc:** `memory/handoff_iter_141_3_stage_1b.md`

---

## Stage 1c: Number Port + 10DLC Campaign Approval (Plivo White-Glove)

**Goal:** Port +14172193856 (and optionally +18664906433) from Twilio to Plivo + complete 10DLC campaign registration with Plivo solutions engineer assistance.

**What operator does (20 min hands-on + 7-10 days async wait):**

1. **Submit number port request (Plivo console):**
   - Navigate to Phone Numbers → Port-In
   - Click "New Port Order"
   - Provide:
     - Numbers to port: +14172193856 (primary), +18664906433 (optional toll-free)
     - Current carrier: Twilio
     - Twilio account SID: (from Twilio console)
     - Authorized user: Jason Aaron Meyer
     - Signature: (digital signature via console)
   - Submit port request
   - **Status: Submitted** - awaiting carrier acceptance + Firm Order Commitment (FOC) date
2. **10DLC campaign registration (white-glove path):**
   - Plivo solutions engineer (from stage 1a support ticket) provides campaign submission template
   - Operator fills template:
     - Use case: "Operational notifications" (mixed standard use case)
     - Message flow: "Broker sends load opportunities + confirmations + delivery updates to carriers per signed agreements"
     - Sample messages:
       1. "CHL Load Opportunity: Chicago IL → Dallas TX, $2,450, Van. Reply START to opt-in. MC-1817555."
       2. "Carrier confirmed pickup at 14:00 CT. Load ref #12345. Reply STOP to opt-out."
       3. "Load delivered. Payment processing. Text HELP for support."
     - Opt-in mechanism: "Signed broker-carrier agreements + future website CTA at continentalhaul.com/sms-opt-in"
     - Opt-out: "Reply STOP to any message, removed within 24h"
     - Monthly volume estimate: 50-200 messages/month at v1 scale
   - Plivo engineer submits campaign to TCR on operator's behalf
   - **Status: Processing** - carrier vetting (1-2 weeks)
3. **Monitor port + campaign status:**
   - Port status: Phone Numbers → Port-In → check FOC date (7-10 days typical)
   - Campaign status: A2P 10DLC → Campaigns → check vetting status (PROCESSING → COMPLETED)
   - Plivo sends email alerts when both complete

**What Dev Engineer does (async, 10 min when port completes):**

1. **Once port completes + campaign approved:**
   - Update `.env`:
     ```
     PLIVO_PHONE_NUMBER=+14172193856  # replace temp number with ported number
     ```
   - Plivo console: Phone Numbers → click +14172193856 → Link to Campaign → select approved campaign
   - Restart CHL-Backend: `Restart-Service CHL-Backend`
2. **Empirical smoke test (ported number):**
   ```powershell
   & "C:\CHL\.venv-local\Scripts\python.exe" -c "from notification_service import send_sms; send_sms('+18165609180', 'Plivo migration complete - ported number test')"
   ```
   - Check operator phone: SMS should deliver from +14172193856 (ported number)
   - Plivo console: verify delivery + campaign linkage

**Smoke battery (5 tests):**
1. Port status: COMPLETED (FOC date passed)
2. Campaign status: APPROVED (TCR vetting passed)
3. Number linked to campaign (Plivo console)
4. SMS sends from ported number (+14172193856)
5. Delivery confirmation (operator receives SMS)

**Success criteria:**
- ✅ +14172193856 ported to Plivo
- ✅ 10DLC campaign approved by carriers
- ✅ Ported number linked to campaign
- ✅ SMS delivers from ported number

**Estimated time:** 20 min operator hands-on + 7-10 days port/campaign async wait

**Handoff doc:** `memory/handoff_iter_141_3_stage_1c.md`

---

## Stage 1d: Cutover + Twilio Deprecation

**Goal:** Finalize migration, deprecate Twilio credentials, confirm CHL fully operational on Plivo.

**What Dev Engineer does (20 min):**

1. **Final .env cutover:**
   ```
   # Twilio DEPRECATED (keep for 30-day rollback window, then delete)
   # TWILIO_ACCOUNT_SID=AC...
   # TWILIO_AUTH_TOKEN=...
   # TWILIO_PHONE_NUMBER=+14172193856
   
   # Plivo ACTIVE (production)
   PLIVO_AUTH_ID=<from vault>
   PLIVO_AUTH_TOKEN=<from vault>
   PLIVO_PHONE_NUMBER=+14172193856  # ported number
   OWNER_SMS_NUMBER=+18165609180
   ```
2. **Restart CHL-Backend:** `Restart-Service CHL-Backend`
3. **Integration smoke test (end-to-end):**
   - Trigger actual load event → verify notification_service.py sends SMS via Plivo
   - Check Plivo console: delivery confirmation
   - Check operator phone: SMS received
4. **Update current_status.md:**
   - SMS path: "DEGRADED (Twilio 10DLC rejection)" → "RESTORED (Plivo migration complete)"
   - Bridge cumulative: add iter 141.3 to tally
5. **Twilio console cleanup (30-day rollback window):**
   - Keep Twilio account active for 30 days post-cutover
   - Don't cancel numbers immediately (port may have 30-day reversal window)
   - After 30 days: release Twilio numbers, archive Twilio creds in vault

**What operator does (5 min):**

1. **Visual confirmation:**
   - Receive test SMS from +14172193856 (ported number, now on Plivo)
   - Reply STOP → verify opt-out handling (Plivo webhook if configured, manual if not)
   - Reply HELP → verify auto-response (Plivo auto-responder if configured)

**Smoke battery (7 tests):**
1. SMS sends via Plivo (not Twilio)
2. Ported number delivers successfully
3. notification_service.py integration works end-to-end
4. Plivo console shows delivery logs
5. No errors in CHL-Backend logs
6. current_status.md updated to "SMS RESTORED"
7. Twilio creds archived (not deleted, 30-day rollback)

**Success criteria:**
- ✅ CHL fully operational on Plivo
- ✅ SMS path restored (no more voice-only alerts)
- ✅ Twilio deprecated but recoverable (30-day rollback window)
- ✅ Operator confirms SMS delivery from ported number

**Estimated time:** 25 min (20 dev + 5 operator)

**Handoff doc:** `memory/handoff_iter_141_3_stage_1d.md`

## Iter 141.3 Close Ceremony

(Standard CHL iter close — same protocol as iter 141.1/141.2)

- [ ] Verify smoke totals across stages 1a/1b/1c/1d (26 tests total)
- [ ] Write `memory/handoff_iter_141_3_close.md` (Dev Engineer does this)
- [ ] Run `scripts/backup_to_d.ps1 -IterId "iter_141_3_close"` (full ceremony)
- [ ] Update `chl-memory/progress/current_status.md` to reflect iter close + bridge cumulative tally
- [ ] Decide on iter 142.1 (Phase 3 Carrier Network) or other priority

---

## Common Pitfalls + Mitigations

### "Plivo 10DLC campaign rejected by TCR"
- **Cause:** Sample messages flagged as spam, opt-in mechanism unclear, or missing business registration docs
- **Fix:** Plivo white-glove team provides feedback + assists with resubmission. Use their pre-approved templates for operational SMS (not marketing/promotions). Ensure sample messages include MC-1817555, clear opt-out, and broker context.

### "Number port stuck in 'Submitted' status for >10 days"
- **Cause:** Twilio blocking port (incorrect account info, unpaid balance, or missing authorization)
- **Fix:** Contact Plivo support to escalate. Provide Twilio account SID, authorized user signature, and billing status. If Twilio blocks maliciously, provision new Plivo number as fallback + update external docs.

### "SMS delivers but from wrong number (temp instead of ported)"
- **Cause:** `.env` still has temp number after port completes
- **Fix:** Update `PLIVO_PHONE_NUMBER` to ported number (+14172193856), restart CHL-Backend, retest. Plivo console should show ported number linked to campaign.

### "Plivo API returns 401 Unauthorized after migration"
- **Cause:** Wrong Auth ID or Auth Token in `.env`
- **Fix:** Re-copy from Plivo console → Account → API Credentials. Auth ID starts with "MA..." (not "AC..." like Twilio).

### "Operator regrets migration, wants to return to Twilio"
- **Cause:** Plivo deliverability issues, cost higher than expected, or workflow friction
- **Fix:** Reverse-port numbers back to Twilio (7-10 day window). Revert notification_service.py to Twilio SDK. Twilio account kept active during 30-day rollback window specifically for this scenario.

---

## What to skip / NOT do during iter 141.3

- ❌ Don't delete Twilio credentials immediately after cutover (keep 30-day rollback window)
- ❌ Don't merge Twilio Voice migration into this iteration (Voice still works on Twilio, defer to Phase 3+)
- ❌ Don't rush number port (7-10 days is normal, don't escalate prematurely)
- ❌ Don't skip white-glove support (Plivo's solutions engineers are the value-add, use them)

---

**Status:** ✅ AGENDA DRAFT COMPLETE. Ready for operator/dev-engineer review before iter 141.3 kickoff.

**Cross-reference:**
- `chl-memory/research/plivo_migration_research.md` - Full Plivo vs Twilio comparison + white-glove process
- `chl-memory/research/iter_141_2_launch_operator_runbook.md` - Voice-only alerts (iter 141.2 baseline before 141.3 restores SMS)

**Source:** Drafted by PM Claude (@pm-lead) 2026-05-07 per operator decision to switch providers + @dev-engineer execution plan (msgs 4317dde0 + ab232278).
