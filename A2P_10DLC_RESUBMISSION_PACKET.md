# A2P 10DLC Campaign Resubmission Packet — Continental Haul Logistics LLC
**Date prepared:** 2026-05-03
**Reason for resubmission:** TCR rejection — Error code **30909**, "Call to Action verification failed on MESSAGE_FLOW field"
**Brand status:** APPROVED (<REDACTED-TWILIO-BRAND-SID>) — no change needed
**Campaign SID:** <REDACTED-TWILIO-CAMPAIGN-SID> (RESUBMIT)
**Messaging Service SID:** <REDACTED-TWILIO-MESSAGING-SID>
**Sender phone:** +1 (417) 219-3856

---

## What TCR is asking for

TCR (The Campaign Registry, the body that vets all 10DLC campaigns for the
US carriers) rejected the campaign because the **opt-in flow** description
was insufficient to prove that recipients knowingly agreed to receive SMS.

Specifically, the rejected field is `MESSAGE_FLOW` — the section that
describes *exactly how a person ends up subscribed to the SMS list*.

The resubmission must answer four questions in plain English:

1. **Where does the consumer give consent?** (Web form / paper form / verbal)
2. **What does the consumer see at the moment of consent?** (Exact opt-in
   language, where it appears, whether the checkbox is pre-checked)
3. **What confirmation does the consumer receive?** (Welcome SMS, email,
   nothing)
4. **How does the consumer opt out?** (STOP, HELP, link, phone)

---

## ✅ Approved language — paste THIS into Twilio Console

> Field: **Description / How do end users consent**
>
> ```
> End users consent to receive SMS from Continental Haul Logistics LLC by
> taking one of three explicit, opt-in actions:
>
> 1. Filling out the freight quote form at https://continentalhaul.com (the
>    request-quote landing page) and ticking a UNCHECKED checkbox labeled:
>    "I agree to receive SMS updates from Continental Haul Logistics about
>    my shipment quote and load status. Message and data rates may apply.
>    Reply STOP to unsubscribe. Reply HELP for help. Up to 4 messages per
>    week."
>
> 2. Submitting their phone number on the carrier-onboarding form at
>    https://continentalhaul.com/carrier-onboard, where the same UNCHECKED
>    checkbox + disclosure language appears immediately above the Submit
>    button.
>
> 3. Replying YES to the double-opt-in confirmation SMS that we send the
>    first time a carrier contacts our dispatch line by voice. The
>    confirmation SMS reads:
>    "Continental Haul Logistics: Reply YES to receive load status SMS
>    from this number. Msg & data rates may apply. STOP to opt out."
>
> Each opt-in event is logged in our compliance database (collection:
> `consent_capture`) with timestamp, IP address (web) or call SID (voice),
> the exact disclosure text shown, and the user's opt-in selection.
>
> A double-opt-in welcome SMS is sent within 60 seconds of consent:
> "Welcome to Continental Haul Logistics. You'll receive load status and
> dispatch updates from this number. Reply STOP to opt out, HELP for help."
>
> Opt-out is honored within 30 seconds via the keyword STOP, STOPALL,
> UNSUBSCRIBE, CANCEL, END, or QUIT. We send a single confirmation SMS:
> "You have been unsubscribed from Continental Haul Logistics SMS. No
> further messages will be sent."
> ```

---

## ✅ Sample messages — copy into Twilio Console

> Field: **Sample Messages (provide 2-5)**

**Sample 1 — Load status update (the most common):**
```
CHL Dispatch: Load #FT8849 (Springfield MO → Dallas TX) is now IN TRANSIT.
ETA Tue 5/14 14:00 CT. Driver: Mike R. +1-555-0142. Reply STOP to opt out.
```

**Sample 2 — Quote follow-up:**
```
Continental Haul Logistics: Your Springfield→Atlanta dry-van quote is
$2,840 + FSC. Lock in by 5pm CT today: https://continentalhaul.com/q/A8K4
Reply STOP to opt out.
```

**Sample 3 — Carrier rate confirmation:**
```
CHL: Rate-con FT8849 ready for your e-signature. $2,400 net, pickup 5/13
Springfield MO. Sign here: https://continentalhaul.com/rc/X4F2
Reply STOP to opt out.
```

**Sample 4 — Carrier check-call:**
```
CHL Dispatch: Quick check-call on Load FT8849 — please reply with your
current location and ETA to receiver. Reply STOP to opt out.
```

---

## ✅ Field-by-field cheat sheet

| Field | Value |
|---|---|
| **Use case** | Mixed (Customer Care + Account Notifications) |
| **Brand** | Continental Haul Logistics LLC (already APPROVED) |
| **Campaign description** | "SMS dispatch notifications, rate confirmations, load-status updates, and quote follow-ups for shippers and contracted motor carriers." |
| **Volume** | Low (under 2,000 msgs/day) |
| **Opt-in keywords** | YES, START, SUBSCRIBE |
| **Opt-out keywords** | STOP, STOPALL, UNSUBSCRIBE, CANCEL, END, QUIT |
| **Help keywords** | HELP, INFO |
| **Embedded links?** | YES — rate-con + quote URLs (continentalhaul.com only) |
| **Embedded phone numbers?** | YES — driver/carrier phone numbers in load updates |
| **Age-gated content?** | NO |
| **Affiliate marketing?** | NO |
| **Number pooling?** | NO (single sender +1-417-219-3856) |
| **Direct lending?** | NO |

---

## 🚦 Step-by-step submission walk-through

**Step 1 — Log in**
- Twilio Console → https://console.twilio.com/
- Use the same login that owns the brand

**Step 2 — Navigate**
- Left rail → **Messaging**
- → **Regulatory Compliance**
- → **A2P 10DLC**
- → **Campaigns**
- Find campaign `<REDACTED-TWILIO-CAMPAIGN-SID>` (status: REJECTED)

**Step 3 — Edit & resubmit**
- Click the campaign
- Click **"Edit"** (or **"Resubmit"** if Twilio offers it directly)
- Find the **MESSAGE_FLOW / "How do end users consent"** field — this is
  the one TCR rejected
- **DELETE everything in that field**
- **PASTE the language from the green "Approved language" block above**
- For **Sample Messages**, paste the four samples above (delete any
  existing rejected samples first)
- Confirm all other fields match the cheat-sheet
- Click **Submit / Resubmit**

**Step 4 — Wait**
- Vetting typically takes 24-72 hours after resubmission
- TCR re-vetting fee: **$15** (charged to the Twilio account)
- We'll auto-poll status every hour via `/api/tcr/refresh` and the result
  will reflect on Day-1 Gate #4 (`a2p_10dlc_approved`)

**Step 5 — While you wait**
- All outbound SMS to carriers/shippers stays GATED — `notification_service`
  blocks them and falls back to email + voice. This is by design and is
  protecting you from racking up 30034 errors.

---

## 🛡️ What the platform already enforces (no action needed from you)

The CHL platform already implements every consent + opt-out behavior
described in the resubmission packet:

| Behavior | Module | Endpoint |
|---|---|---|
| Web form opt-in checkbox + log | `consent_capture.py` | `/consent/optin/{token}` |
| Double-opt-in confirmation SMS | `notification_service.py` | auto on first inbound |
| STOP/STOPALL/UNSUBSCRIBE keyword handling | `notification_service.py` | inbound webhook |
| Opt-out audit trail | `db.consent_capture` | every event timestamped + IP'd |
| 30-second opt-out enforcement | `volume_throttle.py` | hard-block on suppress list |
| Welcome SMS within 60s | `notification_service.py` | post-opt-in trigger |

In other words, **TCR's only beef was with the documentation**, not with
the actual implementation. You have the receipts; we just need to write
them up clearly.

---

## 📝 Notes for future Twilio compliance work

- **Brand stays APPROVED** — do NOT resubmit the brand. Only the campaign.
- **Same SIDs** — keep the existing campaign SID; don't create a new one,
  or you'll lose the existing message history and have to re-establish
  the brand-↔-campaign link.
- **TCR vetting fee** of $15 is per resubmission. Budget accordingly if
  this needs more than one round.
- **A2P gate is auto-locked** — `notification_service.fetch_10dlc_status_from_twilio`
  enforces "Brand APPROVED **AND** Campaign VERIFIED" before any SMS goes
  out. It will auto-unblock the moment TCR flips the campaign green.
