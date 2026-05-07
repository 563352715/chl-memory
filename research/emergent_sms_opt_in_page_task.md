# Emergent Task: `/sms-opt-in` Page + Backend Endpoint (CHL)

> **Iteration:** 141.3 backup-path / iter 142.x prep
> **Goal:** Public-facing SMS opt-in page on continentalhaul.com that captures TCR-acceptable proof of consent. Required if Plivo white-glove 10DLC submission rejects (or if iter 141.3 needs to ship before white-glove approval).
> **Scope:** React page (`frontend/src/views/SmsOptInPage.jsx`) + FastAPI endpoint (`backend/sms_opt_in.py`) + db.sms_opt_in_consents collection schema.
> **Estimated build:** 1.5–2 hrs Emergent time.

---

## CONTEXT (read before building)

CHL = Continental Haul Logistics LLC, a freight brokerage (MC-1817555, USDOT 4569843). Operator: Jason Aaron Meyer (`dispatch@continentalhaul.com`).

The platform sends operational SMS to motor carriers and shippers per signed broker-carrier agreements. TCR (The Campaign Registry) requires a public, crawlable Call-To-Action (CTA) page documenting the opt-in flow. CHL's previous Twilio 10DLC submission was REJECTED with error 30909 ("CTA verification failed for message_flow") because no public CTA URL existed — only paper agreements.

This page is the durable fix. Plivo's white-glove migration may bypass the need (their solutions engineers use pre-approved templates), but having this page also strengthens any future re-submission and protects against vendor changes.

**Cross-references:**
- `chl-memory/research/plivo_migration_research.md` — Plivo white-glove process
- `chl-memory/research/iter_141_3_agenda_draft.md` — full iter 141.3 plan
- `backend/notification_service.py` — existing SMS infrastructure (A2P 10DLC gating)
- `frontend/src/views/BidReviewDashboard.jsx` — example of existing CHL React page (auth, styling, conventions)

---

## DELIVERABLE 1: `frontend/src/views/SmsOptInPage.jsx`

**Routing:** Add to `frontend/src/App.js` as a PUBLIC route (no auth required):
```javascript
import SmsOptInPage from "./views/SmsOptInPage";
// In the unauthenticated branch (BEFORE the auth gate):
if (path.startsWith("/sms-opt-in")) return <SmsOptInPage />;
```

NOTE: CHL's `App.js` uses hand-rolled `path.startsWith()` routing, NOT `react-router-dom`. Match that pattern (see how `/aged-ar` or other public routes are handled).

**Page sections (top to bottom):**

1. **Header (CHL branding):**
   - Logo or company name
   - Tagline: "Continental Haul Logistics LLC — MC-1817555 · USDOT 4569843"

2. **Opening explainer (2-3 short paragraphs):**
   - "Continental Haul Logistics provides operational SMS communications to motor carriers and shippers we work with."
   - "By providing your mobile number below, you consent to receive SMS messages from Continental Haul Logistics about loads, dispatch, and operational coordination."
   - Make this clear, plain English. No legalese.

3. **What types of messages section (bulleted list):**
   - Load dispatch confirmations
   - Rate confirmation delivery alerts
   - Pickup/delivery status updates
   - Document requests (BOL, POD, insurance certificates)
   - Payment status notifications
   - Two-way carrier-callback coordination

4. **Frequency disclosure:**
   - "Message frequency varies — typically 1-5 messages per active load. Standard message and data rates may apply per your carrier."

5. **Opt-in form:**
   - Phone number input (E.164 format, US-only initially) with format guide ("e.g. +1 555 123 4567")
   - Name input (optional, helps with carrier matching)
   - Company input (optional)
   - **Checkbox** (required): "I consent to receive SMS messages from Continental Haul Logistics LLC about operational coordination of loads I haul. I understand message and data rates may apply, message frequency varies, and I can opt out at any time by replying STOP."
   - Submit button: "Subscribe to CHL Operational SMS"

6. **Opt-out + Help section:**
   - "Reply **STOP** to any message to unsubscribe. Removed within 24 hours."
   - "Reply **HELP** for support information."
   - "Reply **START** to opt back in if you previously unsubscribed."

7. **Privacy notice:**
   - "We use your phone number ONLY for the operational SMS purposes above. We do NOT share, sell, or rent your number. Phone numbers and consent records are stored in our secure system and are subject to our [Privacy Policy](/privacy)."

8. **Footer:**
   - "MC 1817555 · USDOT 4569843 · Continental Haul Logistics LLC"
   - "Questions? dispatch@continentalhaul.com · 417-219-3856"

**Styling:**
- Tailwind CSS (CHL convention)
- Mobile-first — most carriers will hit this on a phone after a paper agreement says "go to continentalhaul.com/sms-opt-in to enable text alerts"
- Conservative palette: CHL's existing dark blue (`#002FA7` per the BidReviewDashboard's CTA buttons works fine) + white + slate grays
- Form: clean, generous padding, large tap targets

**Behavior:**
- POST submission to `/api/sms-opt-in` (the backend endpoint, deliverable #2)
- On success: replace form with thank-you message including a confirmation reference ID
- On error: show error message inline, preserve form values, allow retry
- Validate phone format client-side before POST (basic E.164 regex; backend re-validates)
- Disable button while submitting (prevent double-submit)
- Use `axios` with `withCredentials: false` (this is a PUBLIC endpoint, no auth)

**Accessibility:**
- Semantic HTML (`<form>`, `<label>`, `<button>`)
- ARIA labels on form fields
- Error messages programmatically associated with fields
- Tab-order sensible

---

## DELIVERABLE 2: `backend/sms_opt_in.py`

**FastAPI router (mount at `/api/sms-opt-in`, no auth required — this is PUBLIC).**

Schema for incoming POST:
```python
class OptInPayload(BaseModel):
    phone: str        # E.164, validated server-side
    name: Optional[str] = Field(default=None, max_length=100)
    company: Optional[str] = Field(default=None, max_length=100)
    consent_checked: bool  # must be True
    # Server-injected:
    # - ip_address (from request)
    # - user_agent (from request headers)
    # - consent_text_version (string, e.g. "v1.0_2026-05")
    # - submitted_at (ISO timestamp)
```

Endpoint behavior:
1. Validate `consent_checked == True` (return 400 if not — TCR requires explicit affirmative consent)
2. Validate phone is valid E.164 format (server-side, redundant with client-side check)
3. Capture IP from request headers (handle X-Forwarded-For for reverse-proxy setups)
4. Capture User-Agent
5. Capture timestamp
6. Insert into `db.sms_opt_in_consents` collection (see schema below)
7. Return `{"id": "<uuid>", "status": "recorded", "phone": "<masked>"}`
8. NEVER log full phone numbers — only last 4 digits in any logs

MongoDB schema (`db.sms_opt_in_consents`):
```javascript
{
  id: "<uuid v4>",                      // primary key
  phone: "+15551234567",                // E.164, unique-indexed
  name: "John Doe",                     // optional
  company: "Acme Trucking",             // optional
  consent_checked: true,
  consent_text_version: "v1.0_2026-05", // version of the consent text shown
  ip_address: "203.0.113.42",
  user_agent: "Mozilla/5.0...",
  submitted_at: "2026-05-10T14:32:11Z",
  status: "active",                     // "active" | "opted_out" | "bounced"
  opt_out_at: null,                     // populated if STOP received
  opt_out_reason: null,                 // "user_stop" | "delivery_failure" | "manual"
}
```

Indexes:
- `phone` (unique)
- `submitted_at` (descending, for queries)

If phone already exists in collection (re-subscribing after opt-out):
- If existing record `status == "opted_out"`: update to `status = "active"`, set `opt_out_at = null`, `opt_out_reason = null`, append to a history array (or just overwrite — operator's call)
- If existing record `status == "active"`: idempotent return (`{"id": <existing>, "status": "already_recorded"}`)

Owner-only admin endpoint at `/api/sms-opt-in/admin/list` (auth required, owner role):
- Returns paginated list of consent records
- Query params: `status`, `from_date`, `to_date`, `limit` (default 50, max 200)
- Returns: `{"items": [...], "count": N, "has_more": bool}`
- DOES NOT include full phone numbers — masks middle digits (`+1555***4567`)

---

## DELIVERABLE 3: Smoke battery

`backend/.smoke_sms_opt_in.py` (gitignored per `.smoke_*.py` rule):

7 tests:
1. POST with valid payload → 200 + `id` returned + record in DB
2. POST with `consent_checked=false` → 400
3. POST with invalid phone format → 400
4. POST with same phone twice → second is idempotent (200, but no duplicate)
5. GET admin endpoint without auth → 401
6. GET admin endpoint with owner auth → 200 with list
7. GET admin endpoint phone masking verified (no full numbers in response)

---

## CONSTRAINTS

- **DO NOT modify** `backend/notification_service.py` — separate concern
- **DO NOT modify** `frontend/src/App.js` beyond the 1 routing line — operator-approval-per-edit rule
- Use existing CHL conventions (Tailwind, axios, FastAPI, motor for async Mongo)
- Match the styling tone of `frontend/src/views/BidReviewDashboard.jsx`
- Provide `data-testid` attributes on form fields + submit button (E2E testability)
- Phone numbers are PII — handle with same care as auth credentials
- This is a PUBLIC endpoint — rate-limit at the FastAPI level (e.g., 10 submissions per IP per hour) to prevent abuse

---

## ACCEPTANCE CRITERIA

- ✅ `/sms-opt-in` page renders end-to-end on `localhost:3000`
- ✅ Form submission writes a record to MongoDB
- ✅ Phone format validation rejects bad input (both client and server)
- ✅ Re-submission with same phone is idempotent (no duplicate)
- ✅ Admin list endpoint requires auth + masks PII
- ✅ Smoke battery 7/7 PASS
- ✅ Mobile responsive (Lighthouse mobile score >85)

---

**Source:** Drafted by @dev-engineer 2026-05-07 overnight as iter 141.3 backup path. To be executed by Emergent.
