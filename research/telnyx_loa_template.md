# Letter of Authorization (LoA) -- Telnyx Number Port

> **Operator instructions**: copy this template into a Word/Google doc, fill in the bracketed fields, sign, save as PDF. Upload to Telnyx port wizard. Some carriers require a wet signature; typed signature is generally accepted but check your current carrier's requirements first.

---

## LETTER OF AUTHORIZATION TO PORT TELEPHONE NUMBER(S)

**Date:** [YYYY-MM-DD]

**To:** Telnyx LLC, 311 W Superior St, Chicago, IL 60654

**From:**
Continental Haul Logistics LLC
618 West Greenwood Street
Springfield, MO 65807
EIN: 42-2003434

---

I, **Jason Aaron Meyer**, as the authorized representative of Continental Haul Logistics LLC, hereby authorize Telnyx LLC to act as my agent for the purpose of porting the telephone number(s) listed below from my current service provider, **[CURRENT CARRIER NAME]**, to Telnyx.

I authorize Telnyx to:

1. Submit port-out requests to **[CURRENT CARRIER NAME]** on behalf of Continental Haul Logistics LLC.
2. Coordinate with **[CURRENT CARRIER NAME]** to schedule and complete the porting process.
3. Activate the ported number(s) on Telnyx's network upon successful port completion.

I understand that:

- Active service on the listed number(s) will continue with **[CURRENT CARRIER NAME]** until the porting process completes.
- A brief service interruption may occur during the cutover window.
- This authorization remains valid until the port is completed or cancelled by either party.
- I am the rightful account holder of the number(s) listed below and have the legal authority to authorize this transfer.

---

## TELEPHONE NUMBER(S) TO PORT

| Phone Number | Service Type (Voice / SMS / Voice+SMS) |
|---|---|
| +1-866-490-6433 | Voice + SMS (toll-free; public-facing customer support) |
| +1-417-219-3856 | Voice + SMS (10DLC long code; SMS dispatch + voice-forward) |

---

## CURRENT CARRIER ACCOUNT INFORMATION

- **Current Carrier:** Twilio Inc.
- **Account Holder Name (must match Twilio records exactly):** Continental Haul Logistics LLC
- **Service Address (must match Twilio records exactly):** 618 West Greenwood Street, Springfield, MO 65807
- **Current Carrier Account Number (Twilio Account SID):** [OPERATOR-FILL-ON-LOA-SIGNING] (template-redacted per `feedback_secrets_local_only_no_bridge_transmission.md`; the actual SID is in `backend/.env` on the local build machine + verified via Twilio SDK against the two CHL numbers)
- **Port-out / Transfer PIN:** [OPERATOR-FILL-ON-LOA-SIGNING] (generate at Twilio Console -> Account -> General Settings -> Number Porting -> "Get Port-Out PIN"; valid ~30 days)

---

## CONTACT FOR PORT QUESTIONS

- **Authorized Contact:** Jason Aaron Meyer
- **Email:** dispatch@continentalhaul.com
- **Phone:** [SECONDARY CONTACT NUMBER -- not the number being ported, since it'll briefly drop during cutover]

---

## SIGNATURE

By signing below, I confirm that the information provided is accurate and that I am authorized to make this request on behalf of Continental Haul Logistics LLC.

**Signature:** _______________________________

**Printed Name:** Jason Aaron Meyer

**Title:** Owner / Authorized Representative

**Date:** [YYYY-MM-DD]

---

## ATTACHMENTS

- [ ] Most recent bill from current carrier (within last 30 days), showing the number(s) being ported and the account holder name.
- [ ] Photo ID of the authorized signer (if requested by Telnyx or current carrier).

---

*This LoA is FCC-compliant (47 CFR ss 64.1190). Carriers may not refuse a properly-authorized port-out request and must complete it within the FCC-mandated timeframe.*
