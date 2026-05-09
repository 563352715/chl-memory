# DPA Operator Runbook — Continental Haul Logistics LLC

**License**: Continental Haul Logistics LLC internal operations / 2026-05-09 / sub-agent JJJ1'.
**Status**: Active operator-side runbook for executing Data Processing Agreements (DPAs) with vendors that touch personal information.
**Scope**: Vendors enumerated in `frontend/src/views/CompliancePages.jsx` PrivacyPage §3 ("Who We Share With") + the new §9 ("Data Processing Partners"). Not exhaustive of all vendor relationships — only vendors processing **personal information** on CHL's behalf.

---

## Why this runbook exists

The CCPA's business-to-business (B2B) exemption **expired January 2023**. Carrier-employee work data (name, work email, work phone, job title) is now CCPA-protected personal information whenever the data subject is a California resident.

For every external vendor that processes such data on CHL's behalf, CCPA / CPRA and equivalent state laws (VCDPA, CPA, UCPA, CTDPA) require a **signed Data Processing Agreement** (DPA) that:
1. Limits the vendor's use of the data to CHL's documented business purposes.
2. Prohibits the vendor from selling or sharing the data for cross-context behavioral advertising.
3. Requires the vendor to honor data-subject rights (access, deletion, correction) flow-through.
4. Survives termination — vendor must delete or return the data on contract end.
5. Permits CHL to audit the vendor's compliance.

JJJ1' (2026-05-09) has wired the **technical** prerequisites — `/api/privacy/delete` (45-day SLA), `db.privacy_consent_log` (consent ledger), `db.enrichment_audit` (provenance ledger), `/api/_driver/revoke_tracking` (driver opt-out endpoint), pre-permission UI in QROnboardLoad.jsx, terminal-status guard on `gps_ping`. The remaining piece is **operator-side**: physically signing the executed DPAs and filing the PDFs.

---

## Vendor DPA registry

| Vendor | What data they touch | DPA URL / contact | Status | Re-review cadence |
|---|---|---|---|---|
| **Hunter.io** | Email-by-domain enrichment (carrier dispatcher work emails) | https://hunter.io/legal/dpa | Pending operator signature | Annual |
| **People Data Labs** | Contact name + title (officer/dispatcher lookup) | https://www.peopledatalabs.com/dpa or sales@peopledatalabs.com | Pending operator signature | Annual |
| **Cobalt Intelligence** | State Secretary-of-State business filings (officer names, registered agent, addresses) | https://cobaltintelligence.com → contact sales@cobaltintelligence.com | Pending operator signature | Annual |
| ~~Samsara~~ | ~~Carrier ELD GPS / fleet telematics~~ — **DROPPED 2026-05-09** | — | — | — |
| Highway (deferred) | Carrier-fraud aggregate identity + fraud signals | https://highway.com → contact sales | Deferred until ROI clear | Annual when adopted |
| RMIS / Truckstop (deferred) | Carrier risk monitoring (carrier risk scores + monitoring alerts) | https://truckstop.com/rmis | Deferred | Annual when adopted |
| **Resend** | Email metadata for transactional dispatch email (recipient address, subject, delivery status) | https://resend.com/legal/dpa | Pending operator signature | Annual |
| **Telnyx** (post-port) | SMS / voice infrastructure (carrier phone numbers, message content metadata, call records) | https://telnyx.com/legal/data-processing-agreement | Pending operator signature | Annual |
| Twilio (legacy, deprecating) | SMS / voice metadata (historical record only — being phased out) | https://www.twilio.com/legal/data-protection-addendum | ✅ Auto-signed via Twilio Terms of Service (legacy operator-account agreement) | Until Telnyx port complete |
| Stripe (idle / pivot-ability) | Payment card information for carrier payouts and shipper invoicing | https://stripe.com/legal/dpa | ✅ Auto-signed via Stripe Services Agreement | N/A (auto-renewed) |
| Mercury Bank | ACH bank-routing + account numbers for ACH payouts (financial data, narrower than CCPA "personal info" but covered as Mercury's own banking-customer privacy obligation) | https://mercury.com/legal | ✅ Auto-signed via Mercury Account Agreement | N/A (auto-renewed) |

---

## Why Samsara was dropped (decision, 2026-05-09)

Operator decision 2026-05-09: **substitute Samsara / Motive / Geotab vendor APIs with CHL-internal `db.gps_pings` aggregation** for the GGG7 carrier-fraud-filter use case (real-time GPS-based pickup-radius / on-time-delivery / route-deviation signals).

Reasoning:
1. **Free** — uses our own load-history pings; no monthly vendor fee. Samsara's API tier is $260+/mo per fleet seat plus per-truck telemetry licensing.
2. **Already operational** — `db.gps_pings` is populated by every active carrier's PWA via `POST /api/carrier/gps/ping` (now CCPA-hardened by JJJ1' as of 2026-05-09; terminal-status loads return 410 Gone, driver-revoked loads return 410 Gone, all events logged to `db.privacy_consent_log`).
3. **Privacy-clean** — no third party touches the pings; data stays inside CHL's database. One fewer DPA to negotiate, one fewer breach surface.
4. **Vendor-adapter hooks remain stub-callable** — the GGG7 fraud-filter module retains the Samsara/Motive/Geotab adapter interfaces as no-op stubs in case Highway is added later (Highway's value is fraud-aggregate identity, not GPS, so adopting Highway later does NOT require re-adopting Samsara).

Net savings: **~$260/month minimum** (single-fleet floor for Samsara API tier; scales with fleet adoption). Net risk reduction: **one fewer vendor-DPA signature surface**.

---

## Operator workflow (per vendor)

When a vendor adapter is being credentialed for the first time:

1. Visit the DPA URL listed in the table above.
2. Read the DPA in full. Flag any clauses to legal counsel that:
   - Permit the vendor to retain data after contract termination (refuse — must require deletion or return).
   - Permit the vendor to use CHL data for the vendor's own product improvement / model training (refuse — must be use-only-for-CHL-purposes).
   - Limit CHL's right to audit the vendor's compliance (negotiate broader audit rights).
3. Sign the DPA via the vendor's e-sign portal OR by uploading a counter-signed PDF.
4. Save the executed PDF to: `chl-memory/operations/dpa_signed/<vendor>_<YYYY-MM-DD>.pdf`. Filename example: `hunter-io_2026-05-15.pdf`.
5. Update this runbook's status field for the vendor: `Pending operator signature` → `✅ Executed YYYY-MM-DD`.
6. Set a calendar reminder for the re-review cadence (1 year out by default; some vendors prefer 18-month renewal cycles — match the vendor's renewal rhythm).
7. Commit + push the runbook update + PDF to chl-memory.

---

## What happens when CHL receives a CCPA deletion request

1. Request lands either via:
   - `DELETE /api/privacy/delete?carrier_id_or_email=...` (automated, owner-gated endpoint built by GGG9; logs to `db.privacy_requests` with 45-day SLA).
   - Email to `dispatch@continentalhaul.com` with subject "CCPA Deletion Request" or "Privacy Request" (manual operator triage).
2. Operator (or scheduled cron) verifies the request is from the data subject (or a verified authorized agent).
3. The endpoint synchronously purges `db.enrichment_audit` + `db.enrichment_raw` rows for that carrier_id (handled by `enrichment.audit.purge_for_carrier`).
4. **For each vendor in the registry above**, operator additionally must:
   - Trigger the vendor-side deletion via the vendor's data-subject-rights API or DPA-mandated email contact.
   - Track the vendor's confirmation timestamp.
   - Report completion within 45 calendar days from the original request receipt.
5. Closure recorded in `db.privacy_requests.{request_id}.status = "completed"` with vendor-confirmation timestamps in `db.privacy_requests.{request_id}.vendor_confirmations`.

---

## How JJJ1' technical changes back this up

| Privacy capability | Technical implementation | Backed by JJJ1' |
|---|---|---|
| Privacy policy publishes the rights | `frontend/src/views/CompliancePages.jsx` PrivacyPage (amended; new §7 California Rights, new §9 DPA Partners, expanded §3 sharing list) | ✅ |
| Automated deletion endpoint | `backend/enrichment/router.py` `DELETE /api/privacy/delete` (GGG9, 2026-05-09) | ✅ (pre-existing) |
| 45-day SLA tracking | `backend/enrichment/privacy.py` `submit_deletion_request` writes `db.privacy_requests` with `sla_deadline = submitted_at + 45 days` | ✅ (pre-existing) |
| Provenance audit ledger | `db.enrichment_audit` populated by every adapter call (GGG9, 2026-05-09) | ✅ (pre-existing) |
| Driver-side consent ledger | `backend/privacy_consent_logger.py` writes `db.privacy_consent_log` rows on consent / revoke / expire events | ✅ (NEW JJJ1') |
| Driver-side revocation endpoint | `POST /api/_driver/revoke_tracking` (token-authed via load_link); marks `db.loads.{id}.driver_revoked_tracking_at` + writes consent log | ✅ (NEW JJJ1') |
| GPS ping termination on revoke | `POST /api/carrier/gps/ping` returns HTTP 410 Gone for delivered / cancelled / completed loads OR loads with `driver_revoked_tracking_at` set | ✅ (NEW JJJ1') |
| Pre-permission consent UI | `QROnboardLoad.jsx` shows location-sharing notice + checkbox gate before form submit | ✅ (NEW JJJ1') |
| In-app revocation UX | `QROnboardLoad.jsx` "Stop sharing my location" button on the success/done screen | ✅ (NEW JJJ1') |
| DPAs (operator-side) | This runbook — pending operator signatures per table above | ⏳ Operator hand-off |

---

## Pending operator actions (priority ordered)

1. **Sign the Hunter.io DPA** — required before Hunter.io adapter is credentialed for production runs (currently stub-callable only). https://hunter.io/legal/dpa
2. **Sign the People Data Labs DPA** — required before PDL adapter is credentialed. https://peopledatalabs.com/dpa
3. **Sign the Cobalt Intelligence DPA** — required before state-SOS adapter is credentialed. sales@cobaltintelligence.com
4. **Sign the Resend DPA** — operationally already in use; sign-off plugs the legal gap. https://resend.com/legal/dpa
5. **Sign the Telnyx DPA** — must be in place BEFORE the Twilio → Telnyx port migration completes. https://telnyx.com/legal
6. **Decide on Highway** — defer until carrier-fraud signal needs more than `db.gps_pings` + FMCSA data alone (current GGG7 stack is sufficient for Phase 1). When adopted, sign Highway's DPA before crediting any production keys.
7. **Decide on RMIS / Truckstop** — defer; same reasoning as Highway. RMIS's main value is the broker-side carrier-monitoring dashboard, not data CHL doesn't already pull from FMCSA.

---

## Re-review cadence

- **Annual** (default for all enrichment + communication vendors): set a calendar reminder one year after each executed DPA's signature date. On re-review, check (a) vendor's privacy policy hasn't materially changed, (b) DPA terms haven't shifted, (c) the data scope CHL passes to the vendor still matches the documented purpose.
- **On vendor data-breach disclosure**: emergency re-review regardless of cadence. Pause the adapter. Notify any affected data subjects per state breach-notification laws.
- **On regulatory change**: when a new state privacy law comes into force or an existing one's enforcement posture shifts, audit which DPAs need amendment.

---

## Filing structure

```
chl-memory/operations/
├── dpa_operator_runbook_2026_05_09.md       <- THIS FILE
└── dpa_signed/                                <- create on first signature
    ├── hunter-io_YYYY-MM-DD.pdf
    ├── peopledatalabs_YYYY-MM-DD.pdf
    ├── cobalt-intelligence_YYYY-MM-DD.pdf
    ├── resend_YYYY-MM-DD.pdf
    ├── telnyx_YYYY-MM-DD.pdf
    └── ... (Highway / RMIS when adopted)
```

Twilio + Stripe + Mercury are auto-signed via their respective platform Terms of Service when the operator account was opened; no separate PDF is filed (the platform Terms acceptance is the executed instrument).

---

## Cross-references

- `frontend/src/views/CompliancePages.jsx` — public privacy policy (PrivacyPage)
- `backend/enrichment/router.py` — `/api/privacy/delete` (45-day SLA endpoint)
- `backend/enrichment/privacy.py` — `submit_deletion_request` implementation
- `backend/privacy_consent_logger.py` — consent-event audit trail (NEW JJJ1')
- `backend/server.py` — `/api/_driver/revoke_tracking` (NEW JJJ1') + gps_ping terminal-status guard (NEW JJJ1')
- `frontend/src/views/QROnboardLoad.jsx` — pre-permission consent UI + Stop Sharing toggle (NEW JJJ1')
- `chl-memory/research/fff5_carrier_contact_enrichment_sources_2026_05_09.md` — original FFF5 research (CCPA + Carrier411 + TIA findings); see § 6.2 ("CCPA B2B exemption expired Jan 2023") for the full legal context that drove this runbook.
