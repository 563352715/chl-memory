# Emergent Task: Plivo White-Glove Submission Packet

> **Iteration:** 141.3 stage 1a (operator preparation)
> **Goal:** Generate a clean, single-document packet for the Plivo solutions engineer to use during the white-glove 10DLC migration. Operator pastes this into Plivo console chat after registering the brand. Solutions engineer uses it to submit the campaign on CHL's behalf using their pre-approved templates.
> **Scope:** One markdown deliverable: `chl-memory/research/plivo_white_glove_packet.md`
> **Estimated build:** 30-45 min Emergent time.

---

## CONTEXT

CHL (Continental Haul Logistics LLC) is migrating SMS from Twilio to Plivo. The Twilio 10DLC campaign was rejected (error 30909, CTA verification failed) after 4+ weeks of friction. Plivo's white-glove service is the differentiator — their solutions engineers handle campaign submission using pre-approved templates.

The operator's role is just to provide the right inputs to the white-glove team. This packet is the input.

**Cross-references:**
- `chl-memory/research/plivo_migration_research.md` — full migration research
- `chl-memory/research/iter_141_3_agenda_draft.md` — iter 141.3 stage spec
- `chl-memory/research/iter_141_3_kickoff_checklist.md` — operator pre-flight (item 8 = white-glove kickoff)

---

## DELIVERABLE

Single file: `chl-memory/research/plivo_white_glove_packet.md`

Format: clean markdown, sections with clear headers, designed to be copy-pasted (or attached) in Plivo console chat. Length: ~600-800 words. Should fit in one Plivo support ticket message.

**Section structure:**

### 1. Brand Information
- Legal name: Continental Haul Logistics LLC
- Business type: Private LLC (single-member, owner-operated)
- EIN: (operator fills in)
- Authorized contact: Jason Aaron Meyer, dispatch@continentalhaul.com
- Phone: (operator fills in primary contact phone — may differ from SMS sender)
- Business address: (operator fills in registered LLC address)
- Industry: Freight Brokerage / Transportation
- MC Number: 1817555 (FMCSA Motor Carrier authority — broker)
- USDOT Number: 4569843
- Website: continentalhaul.com (note: operator may not have a public site at the time of submission — note this if so, and reference the planned `/sms-opt-in` page)

### 2. Use Case Description
- **Use case category:** Mixed (operational notifications + 2-way customer service)
- **Primary message types:**
  - Load opportunity notifications to vetted carriers
  - Rate confirmation delivery alerts
  - Pickup/delivery status updates and check-ins
  - Document requests (BOL, POD, insurance certificates)
  - Payment status notifications
  - Two-way carrier-callback coordination

### 3. Recipients & Opt-In Flow

**Who receives messages:**
- Motor carriers who have signed CHL's Broker-Carrier Agreement
- Shippers who have signed CHL's Credit Application
- Both populations have provided explicit phone consent on signed paper agreements

**Opt-in evidence (TCR-acceptable proof of consent):**
1. **Signed agreement consent clause** (paper or PDF):
   > "By providing my mobile number, I consent to receive operational SMS from Continental Haul Logistics LLC (MC-1817555) regarding load dispatch, rate confirmations, status check-ins, document requests, and payment notifications. Msg & data rates may apply. Msg frequency varies. Reply HELP for help, STOP to opt out."
2. **Public CTA URL:** continentalhaul.com/sms-opt-in (page to be built, see `chl-memory/research/emergent_sms_opt_in_page_task.md`). Provides web-based opt-in for carriers/shippers who prefer digital workflow.
3. **Opt-back-in:** Recipients can text START to re-enable.

**Opt-out:**
- Reply STOP to any message → removed within 24 hours
- HELP → autoresponder with support info
- START → re-enables previously opted-out number

### 4. Sample Messages (5 examples — Emergent should write these to be Plivo-template-friendly)

Each ~140 chars, with required STOP/HELP keywords + brand identifier (CHL, MC-1817555). Examples:

1. Load opportunity:
   > "CHL Load Opportunity: Chicago IL → Dallas TX, $2,450, Van. Reply START to opt-in. MC-1817555. Reply STOP to opt out."
2. Carrier confirmation:
   > "Carrier confirmed pickup at 14:00 CT for Load #12345. Driver: J. Smith. Reply STOP to opt out. CHL MC-1817555."
3. Status update:
   > "Status: Load #12345 delivered at 09:15 CT. Awaiting POD. Reply STOP to opt out. CHL MC-1817555."
4. Document request:
   > "CHL: Please send signed POD for Load #12345 to docs@continentalhaul.com so we can release payment. HELP for support, STOP to opt out."
5. Payment notification:
   > "Payment of $2,850 for Load #12345 has been released to your factoring company. ACH should post within 24-48 hrs. CHL MC-1817555. Reply STOP to opt out."

### 5. Volume Estimate
- v1 (pre-revenue): 50-200 messages/month
- v2 (post-revenue, single-broker): 500-1,500 messages/month
- v3 (multi-broker SaaS scale): 5,000-15,000 messages/month
- Plivo's standard brand sufficient at v1-v2 (vetting only needed >6,000/day)

### 6. Phone Number(s) to Link
- Primary: +1 (417) 219-3856 — local 10DLC, will be ported from Twilio (port-in submitted in stage 1c)
- Optional: +1 (866) 490-6433 — toll-free, will be ported separately if cited in signed paperwork
- Temporary number (provisioned during stage 1a) used for stage 1b smoke testing only; release after cutover

### 7. Compliance & Privacy Statements (Plivo's pre-approved language)

**Emergent task:** Use Plivo's standard TCR-acceptance language for these (look up Plivo docs at https://www.plivo.com/docs/sms/concepts/use-cases/operational-notifications/ or similar):
- Privacy notice (numbers used only for stated purposes; not shared/sold/rented)
- Data retention (consent records retained for X years per FCRA/TCR requirements)
- HELP keyword response template
- STOP keyword response template
- START keyword response template

### 8. Migration-Specific Notes
- Migrating FROM: Twilio (Account SID `AC931ed3...`, Brand SID `BN250fa0...` APPROVED, Campaign rejected with error 30909)
- Reason for migration: 4+ weeks of compliance friction with Twilio campaign re-submission cycles
- Carrier vetting: brand approved at TCR level (TCR ID `B6384Q7`) — request Plivo team uses their standard low-volume operational template

---

## CONSTRAINTS

- Output MUST be valid markdown (operator copy-pastes to Plivo console)
- All claims must be factually correct (verify MC#, USDOT, etc. from CHL's actual records)
- No PII beyond what's needed for the submission (no SSN, EIN should be operator-fillable placeholder)
- Sample messages must conform to TCR template patterns (Plivo has docs)
- Length: 600-800 words target

---

## ACCEPTANCE CRITERIA

- ✅ Single markdown file at `chl-memory/research/plivo_white_glove_packet.md`
- ✅ All 8 sections present and populated
- ✅ Operator-fillable fields clearly marked (e.g., "(operator fills in)" or `<EIN>`)
- ✅ Sample messages match TCR template best practices
- ✅ Crisp + scannable formatting (operator can paste into Plivo chat as-is)

---

**Source:** Drafted by @dev-engineer 2026-05-07 overnight as iter 141.3 stage 1a accelerator. To be executed by Emergent.
