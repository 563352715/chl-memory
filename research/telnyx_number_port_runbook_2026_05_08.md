# Telnyx number port runbook (2026-05-08 EOD-16)

## Decision

Operator-mandated 2026-05-08 EOD-16: PORT existing CHL phone number(s) into Telnyx rather than buy a new Telnyx number. Avoids re-registering the number across FMCSA / Stripe / banking / business cards / website / email signature.

## Numbers to port

Operator-confirmed 2026-05-08 EOD-16:

| Number | Type | Current carrier | Account holder | Use case |
|---|---|---|---|---|
| `+1-866-490-6433` | Toll-free | Twilio | Continental Haul Logistics LLC | Public-facing customer support |
| `+1-417-219-3856` | 10DLC long code | Twilio | Continental Haul Logistics LLC | SMS dispatch + voice-forward to operator cell |

**NOT porting:** `+1-816-560-9180` (operator's personal cell, not a CHL number).

## Twilio-specific port info

- **Account number for Twilio port-out:** Twilio Account SID (starts with `AC...`). Find at console.twilio.com -> Account -> General Settings.
- **Port-out PIN:** generated on demand. Twilio Console -> Account -> General Settings -> "Number Porting" section -> "Get Port-Out PIN". Valid ~30 days.
- **Recent bill:** downloadable PDF from Twilio Console -> Billing -> Usage -> "Download monthly summary".
- **Service address must match Twilio's records exactly.** If Twilio has a different address than what's on the LoA, the port will reject. Verify via Twilio Console -> Account -> General Settings -> Address.

## Post-port voice-forwarding reconfiguration (10DLC)

`+14172193856` currently forwards inbound voice calls to operator's cell via Twilio TwiML. After port to Telnyx, this forwarding rule must be recreated in Telnyx:

1. Telnyx Mission Control -> Voice -> Programmable Voice or Call Control.
2. Configure inbound-call webhook OR configure Call Control flow to forward to operator's cell number.
3. Test with one inbound call before retiring the Twilio config.

Alternatively, configure as a simple call-forward via Telnyx's Voice Settings on the number, no code needed.

## Telnyx port prerequisites

Before initiating the port in the Telnyx portal, gather:

1. **Letter of Authorization (LoA)** -- see `telnyx_loa_template.md` in this folder. One per number (or one batch LoA covering multiple numbers from the same carrier).
2. **Recent bill from current carrier** -- PDF or scan, not older than 30 days, must show:
   - Phone number being ported
   - Account holder name (must match the LoA)
   - Service address (must match Telnyx account address)
3. **Current carrier account number**
4. **Port-out PIN / transfer PIN** -- most carriers require this; sometimes called "transfer PIN", "port PIN", "account PIN", or "billing passcode". Find in current carrier's online portal under Account / Security / Transfer PIN.
5. **Authorized signature** on the LoA -- typed signature is acceptable; some carriers require wet signature.

## Telnyx port steps (operator-side)

1. Log in to Telnyx Mission Control Portal.
2. Numbers -> Port Numbers -> "Start a Port".
3. Select port type:
   - **Voice port** if the number is for inbound/outbound calls only.
   - **Voice + SMS port** if the number needs to send/receive SMS too (CHL needs this for dispatch SMS).
4. Enter the phone number(s).
5. Upload supporting docs:
   - LoA (PDF) per template in this folder.
   - Recent bill (PDF or image).
6. Enter current carrier account number + port PIN.
7. Confirm Telnyx Messaging Profile to attach (create one named "CHL Dispatch" first if not present -- see Profile setup below).
8. Submit. Telnyx queues the port with the current carrier.

## Profile setup (do this BEFORE submitting the port if attaching to a profile)

1. Telnyx -> Messaging -> Messaging Profiles -> "Create Profile".
2. Name: `CHL Dispatch`.
3. Webhook URL: `https://continentalhaul.com/api/webhook/telnyx_sms` (will need to be public when SMS goes live; placeholder until FMCSA broker authority completes).
4. Webhook API version: `Edge` (latest).
5. (Numbers attach to this profile after port lands.)

## Port timeline

- **Submit -> approval by current carrier:** 1-7 business days (FCC-mandated minimum + carrier processing).
- **Approval -> activation:** 24-48 hours after carrier approval.
- **Total realistic window:** 7-14 business days.
- During the port, calls + SMS to the number CONTINUE to work via the current carrier until the activation window. Brief outage (minutes to a few hours) at activation.

## Failure modes

- **PIN mismatch / account # mismatch:** carrier rejects the port. Re-fetch from current carrier portal + resubmit. No fee from Telnyx.
- **Recent bill > 30 days old:** carrier may reject. Pull a fresh bill from current carrier portal.
- **Account holder name mismatch:** if LoA name doesn't match what's on file with current carrier, port rejects. Make sure LoA uses the EXACT name on the carrier account.
- **A2P 10DLC registration pending:** SMS won't work immediately after port until A2P brand + campaign register with TCR (~1-3 business days post-port via Telnyx auto-registration).

## Post-port checklist

- [ ] Number ringing through Telnyx (test call from external phone).
- [ ] SMS sending (Telnyx -> external recipient).
- [ ] SMS receiving (external sender -> Telnyx).
- [ ] A2P 10DLC brand + campaign approved (Telnyx Messaging -> 10DLC).
- [ ] Webhook endpoint hitting CHL backend (when public URL active).
- [ ] CHL backend env vars provisioned (see below).
- [ ] Plivo migration: deactivate Plivo subscription once Telnyx is fully live + tested for 1 week.

## CHL backend env vars to provision after port + API key generation

Same `set_env_var.ps1` pattern used for Stripe:

```
.\scripts\set_env_var.ps1 -Key TELNYX_API_KEY -Value "<KEY_V1_xxxxxxxx>"
.\scripts\set_env_var.ps1 -Key TELNYX_MESSAGING_PROFILE_ID -Value "<UUID>"
.\scripts\set_env_var.ps1 -Key TELNYX_FROM_NUMBER -Value "+18165609180" -Restart
```

Then update code to read from these vars (currently the codebase references `PLIVO_*` env vars; we'll need a parallel-runner / cutover commit similar to the stripe_connect path).

## SMS migration code changes (next iter)

After port lands + A2P registers + env vars provisioned:

1. Create `backend/sms_providers/telnyx_provider.py` mirroring `backend/plivo_*` shape.
2. Update `backend/sms_dispatcher.py` (or wherever the SMS-send happens) to use a provider abstraction reading from `CHL_SMS_PROVIDER` env (default `plivo`, switch to `telnyx`).
3. Run parallel for 1 week -- both providers reachable, but only Telnyx actively sends. Plivo as fallback.
4. After confidence: switch default to `telnyx`, deprecate Plivo path.

## References

- Telnyx porting docs: https://telnyx.com/resources/porting-guide
- FCC port-out timing: https://www.fcc.gov/consumers/guides/keeping-your-phone-number-when-you-change-providers
- A2P 10DLC requirements: https://telnyx.com/messaging/10dlc

## Audit trail

- 2026-05-08 EOD-16: runbook drafted post-Telnyx-Verified-tier-upgrade. Awaiting operator-side prereqs (carrier name, account number, port PIN).
