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
