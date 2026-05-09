# Telnyx Browser Softphone — Operator Setup Runbook

**Stream:** KKK1 (2026-05-09)
**Owner:** Operator (Jason). The dev side has shipped the code; the
remaining steps are entirely Telnyx-portal + env-var work.
**Pre-req:** local 10DLC port for `+1-417-219-3856` (`sr_b98ea4`) is live
(FOC May 14-15) **OR** you're testing pre-port using a Telnyx-issued DID.

This runbook is self-contained. You should be able to run it standalone
without re-reading other docs.

---

## What's already shipped (dev side, no operator action)

- `backend/telnyx_webrtc.py` — token-mint endpoint + TeXML for
  outgoing/incoming + ed25519 signature verification + `voice_call_log`
  audit + `/browser-phone-config` readiness probe.
- `frontend/src/views/TelnyxBrowserPhoneView.jsx` — `@telnyx/webrtc`
  dialpad with auto-refresh, mounted in `CommandPhoneView` (replaces the
  old "Dispatch Line" quick-action button).
- Twilio softphone (`browser_phone.py` + `BrowserPhoneView.jsx`) is
  **retained unmodified** as fallback during the migration window.
- 9/9 tests pass in `backend/tests/test_telnyx_webrtc.py`.

The platform is currently rendering an "awaiting activation" message
inside the Command Phone view, not a broken dialpad. Activation flips
to live when the steps below are complete.

---

## 1. Telnyx portal — Voice API App

1. Log in to <https://portal.telnyx.com/> using the master account.
2. Navigate **Voice → Voice API → Voice API Apps**.
3. Click **Create new App**.
4. Fill in:
   - **App name:** `CHL Browser Softphone`
   - **Webhook URL (incoming, primary):** `https://continentalhaul.com/api/telnyx/voice/browser-incoming`
   - **Webhook URL (outgoing, primary):** `https://continentalhaul.com/api/telnyx/voice/browser-outgoing`
   - **Webhook failover:** leave blank for now
   - **Webhook API version:** `1` (TeXML — matches our XML response shape)
   - **Webhook timeout:** `25` seconds
5. Click **Create**. Copy the resulting **Voice API App ID** — looks like
   `400000c1-c1b1-...`. You will paste it into the env var
   `TELNYX_VOICE_PROFILE_ID` in step 3.

---

## 2. Telnyx portal — Telephony Credential

1. Navigate **Voice → Telephony Credentials → Create Credential**.
2. Fill in:
   - **Username:** `chl-broker-softphone`
   - **Password:** generate a strong 32-character password
     (recommended: `openssl rand -base64 24` or use a password manager).
     Save it — you'll only see it once and you'll paste it into
     `TELNYX_WEBRTC_CREDENTIAL_PASSWORD`.
   - **Connection:** select the `CHL Browser Softphone` Voice API App
     from step 1.
3. Click **Create**.

---

## 3. Provision the 3 env vars

Open PowerShell **as the same user the NSSM CHL backend service runs
under** (default: your normal account; if you launched VS Code as admin
on EOD-14, that's the same user). Run:

```powershell
cd C:\CHL\scripts
.\set_env_var.ps1 -Key TELNYX_VOICE_PROFILE_ID    -Value "<App ID from step 1>"
.\set_env_var.ps1 -Key TELNYX_WEBRTC_CREDENTIAL_USERNAME -Value "chl-broker-softphone"
.\set_env_var.ps1 -Key TELNYX_WEBRTC_CREDENTIAL_PASSWORD -Value "<password from step 2>"
```

For the password, prefer the file-based form so the secret never lands
in your shell history:

```powershell
# Stage the password in a temp file
$tmp = New-TemporaryFile
"<password from step 2>" | Out-File -FilePath $tmp -Encoding ascii -NoNewline
.\set_env_var.ps1 -Key TELNYX_WEBRTC_CREDENTIAL_PASSWORD -ValueFile $tmp.FullName
Remove-Item $tmp -Force
```

You should already have `TELNYX_API_KEY` and `TELNYX_PUBLIC_KEY`
provisioned from the SMS port work; if not, provision them the same
way:

```powershell
.\set_env_var.ps1 -Key TELNYX_API_KEY    -Value "<bearer token from Telnyx portal>"
.\set_env_var.ps1 -Key TELNYX_PUBLIC_KEY -Value "<base64-encoded ed25519 public key from Telnyx portal>"
```

The `TELNYX_PUBLIC_KEY` is on the same Voice API App page (look for
"Webhook signing key" / "Public Key").

---

## 4. Telnyx portal — assign numbers to the Voice API App

1. Navigate **Numbers → My Numbers**.
2. For `+1-417-219-3856` (post-port): click the row, **Voice settings →
   Connection or App** → select `CHL Browser Softphone`. Save.
3. Same for `+1-866-490-6433` (toll-free) once it ports.

---

## 5. Restart the CHL backend

NSSM service must reload to see the new env vars:

```powershell
nssm restart CHL-Backend
```

Wait ~10 seconds, then check `Get-Service CHL-Backend` shows `Running`.

---

## 6. Verify activation

### A. Backend readiness probe

From an authenticated browser session (login to the platform as broker
first, then open DevTools → Network → copy the cookie), or via a curl
with the session cookie:

```powershell
curl -X GET "https://continentalhaul.com/api/telnyx/voice/browser-phone-config" `
     -H "Cookie: <paste your session cookie>"
```

**Pass:**
```json
{
  "configured": true,
  "missing_env": [],
  "identity": "broker",
  "telnyx_voice_profile_id_last4": "<last 4 of App ID>",
  "port_status": "live"
}
```

If `configured: false`, the response lists which env vars are still
missing — fix and re-run step 3 + step 5.

### B. Token mint

```powershell
curl -X POST "https://continentalhaul.com/api/telnyx/voice/browser-token" `
     -H "Cookie: <paste your session cookie>"
```

**Pass:** response body contains `"ok": true` and a non-empty
`login_token`. Failure modes:

- `"error": "telnyx_softphone_not_configured"` — env vars missing.
- `"error": "token_mint_failed:HTTPStatusError"` — TELNYX_API_KEY rejected
  or TELNYX_VOICE_PROFILE_ID points to a non-existent App. Check both.

### C. Frontend smoke

1. Open `https://continentalhaul.com/` in Chrome, log in as broker.
2. Navigate to **Command Phone** view.
3. The embedded softphone should now render the dialpad (NOT the
   "awaiting activation" amber banner).
4. Click **Turn ON**. Chrome will ask for mic permission — Allow.
5. Status pill should turn green ("Ready").

---

## 7. End-to-end test calls

### Outbound (browser → your cell)

1. Type your cell phone in E.164 (e.g., `+18165550123`) into the dialpad.
2. Click **Call**.
3. Your cell should ring with caller-ID `+1-866-490-6433` (post-port) or
   the temporary Telnyx DID (pre-port).
4. Answer it. You should hear "This call may be recorded for quality
   and compliance purposes." then silence (the bridge connecting).
5. Speak — confirm 2-way audio.
6. Hang up from either end.

### Inbound (your cell → CHL number)

1. Dial `+1-417-219-3856` from your cell.
2. The browser softphone should ring + your cell forwarding (if
   `TELNYX_BROKER_FORWARDING_NUMBER` is set) should ring simultaneously.
3. Answer in the browser → confirm 2-way audio.
4. Hang up — verify a row was written to the `voice_call_log` Mongo
   collection (you can spot-check via the platform Admin → DB Browser).

---

## Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| Frontend stuck on "Awaiting activation" | Env vars not loaded | Restart NSSM (step 5); confirm `Get-ChildItem env:TELNYX_VOICE_PROFILE_ID` shows the value in the service's user context |
| `/browser-token` returns `token_mint_failed` | Wrong API key or App ID | Re-paste from Telnyx portal; check Authorization header |
| Outbound call silent | Caller-ID not assigned | Step 4 — number must be assigned to the Voice API App |
| Inbound never reaches browser | Voice App webhook URL wrong | Step 1 — verify the URL exactly matches `/api/telnyx/voice/browser-incoming` |
| 400 from webhook | `TELNYX_PUBLIC_KEY` wrong | Re-paste the base64 public key from the portal; restart backend |

---

## Rollback

If anything regresses, the Twilio softphone is still on disk:

- Backend route `/api/twilio/voice/*` is still live.
- `frontend/src/views/BrowserPhoneView.jsx` is untouched and importable.
- To swap the Command Phone embed back to Twilio, edit
  `frontend/src/views/CommandPhoneView.jsx`:
  - Replace `import TelnyxBrowserPhoneView from "./TelnyxBrowserPhoneView";`
    with `import BrowserPhoneView from "./BrowserPhoneView";`
  - Replace the `<TelnyxBrowserPhoneView compact />` with
    `<BrowserPhoneView compact />`.
- Restart frontend dev server / rebuild + redeploy.

Twilio softphone has a 5-day useful-life ceiling per operator decision
2026-05-09 — keep this rollback path open only through the May 14-15
port window, then it can be retired with the Twilio account close.

---

## Modern Features (KKK2, 2026-05-09)

Layered on top of KKK1's base softphone per operator walkthrough
2026-05-09: *"Make the phone look nice too. Should have all features
of a modern phone. With stored numbers and redial, and call waiting."*

### 3 sub-tabs (Keypad / Recents / Contacts)

The configured-state UI now has a 3-tab strip at the top:

- **Keypad** (default) — the original dialpad. Type a number, tap Call.
- **Recents** — last 50 calls from `db.voice_call_log`, sorted newest
  first. Each row shows caller name (resolved against carriers +
  shippers + personal contacts), direction icon (incoming ↘ /
  outgoing ↗ / missed ↙), time, and duration. **Tap any row to
  redial that number.** Voicemails (with embedded audio + transcription)
  appear in a section at the top of this tab.
- **Contacts** — A-Z merged list of carriers, shippers, and personal
  contacts. Each row shows source label (Carrier / Shipper / Personal),
  name, phone, and MC/DOT (carriers only). **Tap to call.** Search
  bar filters by name or phone substring. Personal contacts have a
  delete (×) button; carrier/shipper contacts cannot be deleted from
  here (they live in their canonical collections).

### Caller-ID resolution

When a call comes in OR you view recents, the system resolves the
remote phone number against:

1. `db.carriers` — `contact_phone` / `dispatch_phone` / `primary_phone` →
   shows carrier `legal_name`.
2. `db.shippers` — `ap_phone` / `primary_phone` → shows shipper `name`.
3. `db.softphone_contacts` — personal contacts.
4. Fallback: pretty-formatted phone like `(816) 555-0123`.

This means an incoming call from a carrier in your database shows the
carrier name immediately, and recents shows "Alpha Trucking" instead
of just `+18165550111`.

### Adding personal contacts

In the Contacts tab, click **+ Add**. Form takes name, phone (any
format — auto-normalized to E.164), and optional notes. Persists to
`db.softphone_contacts`. Soft-delete (archived: true) when you click the
× button — never hard-delete to preserve history.

### DTMF tones

Tapping a dialpad digit plays the standard DTMF tone via Web Audio API
(two simultaneous sine waves at 697-1633 Hz frequency pair, ~150ms
duration). Works both from the Keypad tab AND in the active-call view's
"Keys" sub-panel — useful for IVR menus when you're already on a call.

### Active-call view

When a call connects, the entire panel switches to the active-call
view: large caller name centered, formatted phone below, mm:ss timer,
and 5 control buttons in a row:

- **Mute** — toggles `muteAudio` / `unmuteAudio` on the call object.
- **Hold** — toggles `hold` / `unhold`. Uses Telnyx server-side hold
  (caller hears music-on-hold).
- **Keys** — opens an in-call keypad for IVR navigation (DTMF tones
  are sent over the call AND played locally for audible feedback).
- **Speaker** — placeholder for `setSinkId` audio routing (browser
  device-permission gated — left as future iter).
- **End** (red) — hangs up.

### Call waiting (multi-call)

If a second call arrives while you're already on one:

1. An amber "Incoming call (waiting)" banner appears at the top.
2. Two buttons: **Hold current and answer** OR **Decline**.
3. Hold-and-answer: current call is put on hold (caller hears MOH),
   new call is answered. The held call shows in the active-call view
   as "On hold: {name}" with a **Swap** button to toggle which call is
   active (held call resumes, current goes on hold).
4. When the active call ends, if there's a held call it auto-promotes
   to the active position.

### Voicemail

Inbound calls that go unanswered (no client + cell timeout in
`browser-incoming` simul-ring) now hit a TeXML `<Record>` action with
`maxLength=120 transcribe=true`. Telnyx posts the recording URL +
auto-transcription to `/api/telnyx/voice/voicemail-callback`, which
persists to `db.voicemails`.

Recents tab shows unread voicemails at the top with:
- Caller name (resolver-matched) + time
- Embedded HTML5 `<audio>` player for the recording URL
- Auto-transcription text (italicized)
- "Mark read" button

A red badge with the unread count appears next to the **Recents** tab.

### Endpoints reference (KKK2)

| Endpoint | Purpose |
|---|---|
| `GET  /api/telnyx/voice/recents?limit=50` | Last N calls with caller-ID-resolved names |
| `GET  /api/telnyx/voice/contacts?q=&limit=200` | Merged carrier+shipper+personal contacts (q = case-insens substring filter) |
| `POST /api/telnyx/voice/contacts` | Add personal contact `{name, phone, notes}` |
| `DEL  /api/telnyx/voice/contacts/{id}` | Soft-delete personal contact (carrier/shipper rejected 403) |
| `POST /api/telnyx/voice/voicemail-callback` | Telnyx webhook — recording URL + transcription |
| `GET  /api/telnyx/voice/voicemails?limit=25` | Last N voicemails + unread_count |
| `POST /api/telnyx/voice/voicemails/{id}/mark_read` | Mark a voicemail read |

All require_broker auth except `voicemail-callback` which uses ed25519
signature verification (same pattern as the KKK1 webhooks).

### Tests

19/19 pass in `backend/tests/test_telnyx_webrtc.py` (9 KKK1 regression
+ 10 KKK2 new):
- recents sorted desc + paginates
- contacts merged + q filter works
- POST /contacts adds personal contact
- DELETE /contacts archives personal, rejects carrier/shipper 403
- caller-ID resolver matches carrier / shipper / unknown
- voicemail-callback writes db.voicemails row
- /voicemails GET + mark_read
- inbound TeXML now includes the `<Record>` voicemail fallback
