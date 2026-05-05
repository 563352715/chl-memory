# 📞 TWILIO BROWSER PHONE — Step-by-Step Playbook

> **Purpose:** Turn the CHL Command Phone into a full standalone softphone
> (mic + speaker in the browser are the phone). Cell stays armed as fallback.
>
> **Status:** IN PROGRESS — waiting on Jason to paste 3 SIDs from Twilio Console.
> **Requested by:** Jason, 2026-04-29. **Iter tag:** 134.16.

---

## PHASE A — What Jason does in Twilio Console (one-time, ~4 min)

### ✅ A1. Create TwiML App
Go to **https://console.twilio.com/us1/develop/voice/manage/twiml-apps**
→ Click **Create new TwiML App** (blue button, top-right)

Fill in:
- **Friendly Name:** `CHL Browser Phone`
- **Voice → Request URL:** `https://work-remote-search.preview.emergentagent.com/api/twilio/voice/browser-outgoing`
- **Voice → Request Method:** `HTTP POST`
- **Messaging:** leave blank (voice-only app)
- Leave Optional Settings collapsed

Click **Create** (bottom-right of modal).

After creating:
- You'll see the app in the list
- Click on `CHL Browser Phone`
- Top of the page shows **Properties → TwiML App SID** (starts `AP...`)
- Click the copy icon next to it → paste into chat to agent

### ✅ A2. Create API Key + Secret
Go to **https://console.twilio.com/us1/account/keys-credentials/api-keys**
→ Click **Create API Key** (blue button, top-right)

Fill in:
- **Friendly Name:** `CHL Browser Phone`
- **Key Type:** `Standard`
- **Region:** leave default (US1)

Click **Create API Key**.

⚠️ **CRITICAL:** The next screen shows the **Secret exactly once**. Copy both before clicking "Got it":
- **SID** (starts `SK...`) — you can retrieve this later
- **Secret** (long random string) — NEVER shown again; must be copied now

If you miss the Secret, just delete the key and make a new one — harmless.

### ✅ A3. Paste 3 values to the agent in chat
```
App SID: APxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
API Key SID: SKxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
API Key Secret: <long-random-string>
```

**Agent locks these into `backend/.env`, never commits them to git.**

---

## PHASE B — What the agent builds (3-4 hrs, next session)

### B1. Backend additions to `backend/.env`
```
TWILIO_TWIML_APP_SID=AP...
TWILIO_API_KEY_SID=SK...
TWILIO_API_KEY_SECRET=...
```

### B2. New backend endpoints (in `server.py` or new `browser_phone.py`)
- `POST /api/twilio/voice/browser-token` → mints short-lived JWT access token
  for the browser SDK. Scoped to voice-only, 1-hr TTL, includes the TwiML App SID.
- `POST /api/twilio/voice/browser-outgoing` → TwiML response when browser
  initiates an outbound call. Dials the carrier's number, plays the
  2-party-consent recording disclosure, bridges.
- `POST /api/twilio/voice/browser-incoming` → TwiML response when Twilio
  phone number receives a call. Dials the browser client + cell in parallel
  with 15-second ringtime, whoever picks up first wins.

### B3. Update the main Twilio number's Webhook
In Twilio Console → Phone Numbers → Active Numbers → `+1 (417) XXX-XXXX`:
- **A Call Comes In:** `TwiML App` → select `CHL Browser Phone`
  (this routes inbound to the browser + cell dual-ring)

### B4. Frontend additions
- Install `@twilio/voice-sdk` via `yarn add`.
- Upgrade `/command-phone` with:
  - 🎧 **"Browser phone ON / OFF"** toggle top-right
  - **Microphone permission request** (Chrome prompt)
  - **Dialpad** for manual dialing + IVR tones
  - **Incoming call banner** — slides in from top with caller ID + Accept/Decline
  - **In-call controls** — mute, hold, transfer, hang up, recording-on indicator
  - **Live transcript drawer** (optional — uses browser's SpeechRecognition)

### B5. Recording consent
Every outbound call TwiML starts with `<Say voice="Polly.Joanna">This call may be recorded for quality and compliance purposes.</Say>` before dialing the carrier. Covers all 13 two-party consent states.

### B6. Test plan (live with Jason)
1. Jason clicks "Browser phone ON" on `/command-phone`
2. Chrome asks mic permission → Jason allows
3. Jason types his own cell number into the dialpad, hits Call
4. His cell rings with CHL Twilio number as caller ID
5. He picks up — talks to himself through laptop speakers to confirm audio
6. Hang up. Then we ring INTO the Twilio number from the cell → browser should ring

---

## PHASE C — CNAM Registration (P1, immediately after softphone ships)

### C1. Why CNAM
Currently when Jason calls a carrier, receiving phone shows raw number (`417-XXX-XXXX`). Many carriers auto-decline unknown numbers. CNAM makes it show `CONTINENTAL HAUL` in the caller-ID strip. Industry data shows 30-50% decline-rate reduction.

### C2. How
- **Outbound CNAM:** Twilio Console → Phone Numbers → select CHL number → Voice Settings → Caller Name Display → set to `CONTINENTAL HAUL` (15-char limit). Propagates to major carriers in 3-7 business days. One-time.
- **Inbound CNAM Lookup:** Twilio Console → Phone Numbers → select CHL number → Voice → Caller ID Lookup → enable. Costs $0.01 per inbound call. Adds caller company name to Command Phone ring screen.

Total recurring cost: ~$1-2/mo + lookup fees.

---

## OPEN QUESTIONS (ask Jason if needed)

- Default recording ON or OFF for outbound calls? (recommend ON — needed for dispute defense on rate disagreements)
- Should incoming calls ring browser FIRST then cell after 15s (recommended), or ring BOTH simultaneously?
- Which CHL Twilio number becomes the primary Browser Phone line — the 417 local or the toll-free? (recommend 417 local for carrier trust; toll-free is SMS-gated anyway until TFV approves)
