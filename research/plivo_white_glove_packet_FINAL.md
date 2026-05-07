<!-- Source: Emergent-built on CHL branch conflict_060526_1955; finalized in chl-memory/research/. Operator pastes from here. -->

# Plivo White-Glove 10DLC Submission Packet — Continental Haul Logistics LLC

> **For:** Plivo Solutions Engineer / 10DLC Onboarding Team
> **From:** Continental Haul Logistics LLC (CHL)
> **Use:** Paste this entire packet into Plivo console chat after Brand registration. The white-glove team will use it to submit the A2P 10DLC campaign on our behalf using your pre-approved low-volume operational template.
> **Migration context:** Migrating from Twilio (4+ weeks of 30909 CTA-rejection friction). Twilio Brand `BN250fa0a51e815ad83bc1ee46b9556555` is approved at TCR (TCR ID `B6384Q7`). Requesting Plivo team apply standard low-volume Mixed-use-case operational template.

---

## 1. Brand Information

| Field | Value |
|---|---|
| Legal name | Continental Haul Logistics LLC |
| DBA / Trade name | Continental Haul (CHL) |
| Business type | Private LLC (single-member, owner-operated) |
| EIN | `<EIN>` *(operator fills in — see W-9 on file)* |
| State of formation | `<state>` *(operator fills in)* |
| Year formed | `<year>` *(operator fills in)* |
| Authorized contact | Jason Aaron Meyer |
| Authorized email | dispatch@continentalhaul.com |
| Authorized phone | `<contact phone>` *(operator fills in — may differ from SMS sender)* |
| Registered business address | `<street, city, state, zip>` *(operator fills in — must match SOS filing)* |
| Industry | Freight Brokerage / Transportation |
| MC Number (FMCSA broker authority) | **MC-1817555** |
| USDOT Number | **4569843** |
| Website | continentalhaul.com *(landing + `/sms-opt-in` page in build — see note in §3)* |
| Stock symbol / Russell 3000 | N/A (private) |
| Government entity | No |
| Tax-exempt | No |

---

## 2. Use Case

| Field | Value |
|---|---|
| **TCR use case** | **Mixed** (Low Volume) |
| **Sub-use cases** | Account Notifications · Customer Care · Delivery Notifications |
| **Embedded links?** | Yes (rate-confirmation download URLs, POD upload URLs) |
| **Embedded phone numbers?** | Yes (dispatch callback line) |
| **Age-gated content?** | No |
| **Direct lending or loan arrangement?** | No |

**Primary message types:**
1. Load opportunity notifications to vetted carriers
2. Rate confirmation delivery alerts
3. Pickup / delivery status updates and check-ins
4. Document requests (BOL, POD, insurance certificates, W-9)
5. Payment status notifications (factoring released, ACH posted)
6. Two-way carrier callback coordination

All messages relate to an active business relationship under a signed Broker-Carrier Agreement or Shipper Credit Application. CHL does not send marketing, promotional, or third-party content.

---

## 3. Recipients & Opt-In Flow

### Who receives messages

- **Motor carriers** who have signed CHL's Broker-Carrier Agreement (paper or e-signed PDF).
- **Shippers** who have signed CHL's Credit Application.
- Both populations have provided their phone number on the signed agreement as the preferred contact method.

### Three documented opt-in paths (TCR-acceptable proof of consent)

**Path A — Signed agreement consent clause (primary path):**

Every Broker-Carrier Agreement and Shipper Credit Application contains the following verbatim consent clause directly above the signature line:

> *"By providing my mobile number, I consent to receive operational SMS from Continental Haul Logistics LLC (MC-1817555) regarding load dispatch, rate confirmations, status check-ins, document requests, and payment notifications. Msg & data rates may apply. Msg frequency varies. Reply HELP for help, STOP to opt out."*

The signed agreement (PDF or paper) is retained in CHL's vendor file. Phone numbers are added to the SMS list **only after** the signed agreement is on file.

**Path B — Public web opt-in (digital CTA):**

Public URL: **https://continentalhaul.com/sms-opt-in**

Visitor enters mobile number and checks an unchecked-by-default consent checkbox containing the same verbatim consent text as Path A. On submit, CHL sends a one-time confirmation SMS asking the user to reply **YES** to confirm. Upon receipt of YES, the number is marked `opted_in` in our consent ledger.

> ⚠ **Note for Plivo team:** the `/sms-opt-in` landing page is in active build (target completion: stage 1d of iter 141.3). If submission requires a live URL today, please flag and we will accelerate that build to T-0.

**Path C — Keyword opt-in (ongoing self-serve):**

Recipients can text **JOIN** (or **START**) to the CHL SMS sender at any time. They receive a confirmation prompt and must reply **YES** to complete opt-in (double-opt-in). All keyword interactions are audit-logged at `/api/admin/sms-consent` (broker-authenticated endpoint). Sample audit row:

```json
{
  "phone": "+18165551234",
  "status": "opted_in",
  "source": "keyword_join",
  "join_initiated_at": "2026-05-07T03:08:00Z",
  "opt_in_at": "2026-05-07T03:08:42Z",
  "opt_in_text": "YES",
  "history": [
    {"keyword": "JOIN", "ts": "..."},
    {"keyword": "YES",  "ts": "..."}
  ]
}
```

### Opt-out and help

| Keyword | Behavior |
|---|---|
| **STOP** / STOPALL / OPTOUT / CANCEL / END / QUIT / UNSUBSCRIBE / REVOKE / REMOVE | Number marked `opted_out` within 24h (typically <1s). Outbound SMS to that number is blocked at the application layer for the lifetime of the opt-out, regardless of any other consent path. |
| **HELP** / INFO | Returns autoresponder with company name, MC number, support email, and support phone. No state change. |
| **START** / JOIN | Re-enables a previously opted-out number after explicit YES confirmation (Path C). |

**Suppression-state enforcement:** Opt-out state is stored as `db.carriers.sms_opt_in: false` (boolean per-recipient flag). The outreach module (`backend/carrier_outreach.py`, iter 142.1 stage 1d) checks this flag before every send; suppressed numbers receive no further outbound SMS until an explicit re-opt-in path (keyword `START` + `YES` confirmation, or fresh signed agreement) flips the flag back to `true`.

---

## 4. Sample Messages (5 — TCR-template-aligned, GSM-7-encoded, ≤140 chars each)

Each message is plain ASCII (GSM-7-safe), single-segment, includes brand identifier (CHL), and HELP/STOP keywords.

**1. Load opportunity notification (140 chars):**
> CHL: Load #CHL-24871, Chicago IL to Dallas TX, Van, $2450, PU 5/14 0800. Reply Y=claim, N=pass. Msg/data rates apply. STOP=optout HELP=info.

**2. Rate confirmation delivery alert (129 chars):**
> CHL: Rate-con for Load #CHL-24871 emailed to your dispatch. Sign/return in 30 min to lock. HELP or 417-219-3856. STOP to opt out.

**3. Status check-in (133 chars):**
> CHL: Driver check-in needed for Load #CHL-24871. Reply 1=Loaded, 2=En route, 3=At receiver, 4=Delivered. MC-1817555. STOP to opt out.

**4. Document request (POD) (130 chars):**
> CHL: Please send signed POD for Load #CHL-24871 to docs@continentalhaul.com to release payment. HELP for support, STOP to opt out.

**5. Payment notification (126 chars):**
> CHL: Payment $2850 for Load #CHL-24871 released to factor. ACH posts in 24-48 hrs. MC-1817555. STOP to opt out.

---

## 5. Volume Estimate

| Phase | Timeline | Daily peak | Monthly volume |
|---|---|---|---|
| **v1 — Pre-revenue / testing** | Now → ~6 weeks | <50 msg/day | 50–200 msg/mo |
| **v2 — Single-broker live ops** | Months 2–9 | <200 msg/day | 500–1,500 msg/mo |
| **v3 — Multi-broker SaaS scale** | Year 2+ | <2,000 msg/day | 5,000–15,000 msg/mo |

**Per-recipient frequency:** 5–20 msgs/recipient/month (event-driven; carriers receive operational SMS only on loads they are assigned to or have outstanding documentation for).

**Standard (non-vetted) brand throughput is sufficient for v1–v2.** Vetted-brand throughput (>6,000/day) only needed at v3. Please provision the standard low-volume operational campaign for v1–v2 and we will request brand vetting independently when v3 is approached.

---

## 6. Phone Numbers to Link

| Number | Type | Status |
|---|---|---|
| **+1 (417) 219-3856** | Local 10DLC | **Primary.** Currently provisioned at Twilio. Port-in to Plivo planned for stage 1c of iter 141.3 (~3-7 business days post-LOA submission). |
| +1 (866) 490-6433 | Toll-free | Optional. Currently provisioned at Twilio (TFV TWILIO_REJECTED 2026-05-04). Will port if cited in shipper paperwork; otherwise release. |
| `<temp>` | Plivo provisioning | Temporary number assigned during stage 1a for stage 1b smoke testing. **Release after cutover.** |

---

## 7. Compliance & Privacy Statements

> **Plivo team:** please apply your standard pre-approved language for items below. We will mirror whatever you provide on our public-facing pages so wording matches between TCR submission and the live consent flow.

- **Privacy notice** — Phone numbers collected by CHL are used only for the operational message types listed in §2. Numbers are not shared, sold, rented, or otherwise disclosed to third parties, except (a) to the SMS service provider (Plivo) strictly for message delivery, and (b) where required by law or subpoena.
- **Data retention** — Consent records (signed agreement on file, `/sms-opt-in` form submission, or keyword opt-in audit row) are retained for the duration of the active business relationship plus a minimum of 4 years thereafter, in line with FCRA and TCR audit-window expectations.
- **TCPA quiet hours** — Outbound messages restricted to recipient local 8am–9pm per TCPA; CHL respects the local time zone of the recipient on file. Time-critical operational exceptions (e.g., load-status callback responses initiated by the recipient) handled per TCPA's established-business-relationship operational exemption.
- **HELP autoresponder template:**
  > Continental Haul Logistics: dispatch SMS for loads, rate-cons, status, payments. MC-1817555. Help: 417-219-3856 / dispatch@continentalhaul.com. Msg & data rates apply. Reply STOP to opt out.
- **STOP autoresponder template:**
  > You have been unsubscribed from Continental Haul Logistics SMS. You will not receive further messages. Reply START to resubscribe.
- **START autoresponder template:**
  > Continental Haul Logistics: Thanks for joining. To complete opt-in, reply YES. You'll receive operational SMS (load dispatch, rate-cons, status, payments). Msg freq varies. Msg & data rates apply. Reply HELP for help, STOP to opt out.

---

## 8. Migration-Specific Notes

| Field | Value |
|---|---|
| Migrating FROM | Twilio |
| Twilio Account SID | `AC***` *(redacted from public packet; operator provides directly to Plivo via private channel — full SID on file, not a secret per Twilio classification but treated as PII for the port LOA)* |
| Twilio Brand SID | `BN250fa0a51e815ad83bc1ee46b9556555` (APPROVED at TCR) |
| **TCR Brand ID** | **`B6384Q7`** *(re-use this — Brand registration is portable across CSPs)* |
| Twilio Campaign | `QE2c6890da8086d771620e9b13fadeba0b` (REJECTED, error 30909 — CTA verification failed; not portable) |
| Twilio TFV | `HH4df57ab148a6c03f2fc2c5497a5308f7` (TWILIO_REJECTED 2026-05-04, "high-risk domain"; not portable) |
| Reason for migration | 4+ weeks of compliance friction with Twilio re-submission cycles. Plivo selected for white-glove campaign submission service. |
| Twilio account closure | **Not yet** — keep active until port + new campaign are live. |

**Specific request to Plivo white-glove team:**
1. Re-use existing TCR Brand ID `B6384Q7` (do not re-register the brand from scratch).
2. Apply Plivo's standard low-volume Mixed-use-case operational template.
3. CTA artifact for TCR review = the keyword opt-in flow (Path C in §3) + the live `/sms-opt-in` URL when it goes live (target T+5 days). Audit ledger access can be granted via temporary read-only API token if TCR requests independent verification.
4. Submit campaign for TCR vetting; expected approval window 1–3 business days for low-volume Mixed.
5. Once campaign is approved, link the ported `+1 (417) 219-3856` to the Messaging Profile.

---

## Operator-fillable checklist (before pasting)

- [ ] §1 — EIN
- [ ] §1 — State of formation + year formed
- [ ] §1 — Authorized contact phone
- [ ] §1 — Registered business address
- [ ] §6 — Plivo temporary number (after Brand registration)

---

*Packet prepared 2026-05-07 for iter 141.3 stage 1a kickoff. Cross-references: `chl-memory/research/plivo_migration_research.md`, `chl-memory/research/iter_141_3_agenda_draft.md`, `chl-memory/research/iter_141_3_kickoff_checklist.md`, `chl-memory/research/plivo_white_glove_review.md`.*
