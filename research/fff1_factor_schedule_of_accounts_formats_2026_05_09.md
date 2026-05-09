# FFF1 — Schedule-of-Accounts Technical Format per Big-5 Transportation Factor (2026-05-09)

**License:** Continental Haul Logistics LLC internal-research artifact. (c) 2026 CHL. Internal use only; do not redistribute. All cited URLs are property of their respective owners and are referenced under fair-use research excerpt.

**Sub-agent:** FFF1 — factor-portal integration format
**Mission:** For each Big-5 transportation factor (Apex / RTS / Triumph / OTR / TBS), document the actual technical format used to submit a "Schedule of Accounts" / "Funding Schedule" / "Funding Batch": file format, required fields per invoice, batch-size limits, required attached documents, API vs portal-only, response format, resubmission rules, public TMS-partner integration list.
**Method:** WebSearch + WebFetch only. Tier 1 = factor's own developer/help docs; Tier 2 = TMS-partner integration docs that publish factor-side specs (Vektor, Alvys, AscendTMS, TAI, Ditat, McLeod); Tier 3 = trade-press confirmation. No Reddit / blogs / forums as primary sources.
**Companion docs:** `eee2_factor_industry_authority_validation_2026_05_09.md` (the "Schedule of Accounts" terminology call), `broker_documents_canonical_reference_v3_2026_05_09.md`, `chl_capability_map_2026_05_09.md`.

> **Headline.** No Big-5 factor publishes a public, downloadable, file-level CSV/JSON spec for its Schedule-of-Accounts. The detailed technical specs (field names, types, batch limits, response payloads) live behind partner-only NDAs and are released ONLY after a signed integration agreement. **OTR Solutions is the only Big-5 with a public developer portal (`docs.otrsolutions.com`)** — and even there, full endpoint specs require a provisioned subscription key. CHL must request integration credentials directly from each factor; the design strategy below assumes that gating is real and unavoidable.

---

## 1. Sources consulted (URL + accessed 2026-05-09)

| # | Source | URL | Authority tier |
|---|---|---|---|
| 1 | Apex Capital — Partner Program | https://www.apexcapitalcorp.com/about/partners/partner-program/ | Tier 1 factor |
| 2 | Apex Capital — Partners list (TMS integrations) | https://www.apexcapitalcorp.com/about/partners/ | Tier 1 factor |
| 3 | Apex Capital — Account Management Portal (AMP) | https://www.apexcapitalcorp.com/tools/amp/ | Tier 1 factor |
| 4 | Apex Capital — 24/7 factoring | https://www.apexcapitalcorp.com/factoring/24-7-factoring/ | Tier 1 factor |
| 5 | Vektor TMS — Apex Capital integration | https://vektortms.com/integrations/apex-capital-factoring | Tier 2 partner |
| 6 | RTS Pro — Factoring guide PDF | https://assets.ctfassets.net/r83p0mxwzi9g/jFfPkXYMGqfro8UKJHyiB/35ab8338a889b5d501c3d3f47c060aeb/Factoring_in_RTS_Pro-English.pdf | Tier 1 factor |
| 7 | RTS Inc. — corporate / factoring software | https://www.rtsinc.com/product/factoring-software | Tier 1 factor |
| 8 | Vektor TMS — RTS Financial integration | https://vektortms.com/integrations/rts-factoring | Tier 2 partner |
| 9 | DENEMO Carrier TMS — RTS Financial integration | https://www.denemo.com/integrations/rts-financial | Tier 2 partner |
| 10 | ProTransport — RTS integration | https://www.pro-transport.com/page/integrations | Tier 2 partner |
| 11 | Datatruck — RTS Factoring Integration | https://support.datatruck.io/hc/en-us/articles/40199198468243-RTS-Factoring-Integration | Tier 2 partner |
| 12 | Triumph — MyTriumph portal | https://www.mytriumph.com/ | Tier 1 factor |
| 13 | Triumph — invoice factoring portal | https://www.invoicefactoring.com/solutions/mytriumph-portal/ | Tier 1 factor |
| 14 | Triumph — factor-of-record holds | https://support.triumphpay.com/support/solutions/articles/44002492009-triumph-payments-factor-of-record-holds | Tier 1 factor |
| 15 | Alvys help — Triumph Business Capital integration | https://help.alvys.com/en/articles/11668769-triumph-business-capital-factoring-integration | Tier 2 partner |
| 16 | TAI Software — Triumph Business Capital integration | https://learn.tai-software.com/knowledge/triumph-business-capital | Tier 2 partner |
| 17 | TAI Software — Triumph Pay integration | https://learn.tai-software.com/knowledge/triumph-pay | Tier 2 partner |
| 18 | AscendTMS — Triumph Seamless Factoring Portal | https://ascendtms.kayako.com/article/149-triumph-business-capital-seamless-factoring-portal | Tier 2 partner |
| 19 | OTR Solutions — Carrier Client Portal | https://otrsolutions.com/solutions/tools/client-portal | Tier 1 factor |
| 20 | OTR Solutions — Developer Resources | https://otrsolutions.com/developer-resources | Tier 1 factor |
| 21 | OTR Solutions — API Integrations | https://otrsolutions.com/resources/api-integrations/ | Tier 1 factor |
| 22 | OTR Solutions — Developer portal landing | https://docs.otrsolutions.com/ | Tier 1 factor (gated) |
| 23 | OTR Solutions — Carrier Integrations doc | https://docs.otrsolutions.com/docs/carrier-integrations | Tier 1 factor |
| 24 | OTR Solutions — Building Your API Integration | https://docs.otrsolutions.com/docs/building-your-api-integration | Tier 1 factor |
| 25 | OTR Solutions — Broker Integrations | https://docs.otrsolutions.com/docs/broker-integrations | Tier 1 factor |
| 26 | LoadOps — OTR Solutions factoring integration | https://help.loadops.com/loadops-otr-solutions-factoring-integration | Tier 2 partner |
| 27 | Vektor TMS — OTR Solutions integration | https://vektortms.com/integrations/otr-solutions | Tier 2 partner |
| 28 | Motive Help Center — OTR Solutions integration | https://helpcenter.gomotive.com/hc/en-us/articles/30917364341277-How-to-enable-OTR-Solutions-integration | Tier 2 partner |
| 29 | TBS Factoring — Get Paid app | https://tbsfactoring.com/get-paid-app/ | Tier 1 factor |
| 30 | TBS Factoring — Fast Funding in Three Simple Steps | https://tbsfactoring.com/trucking-resources-articles-fast-funding-in-three-simple-steps/ | Tier 1 factor |
| 31 | TBS — Web Portal | https://web-portal.tbsokc.com/ | Tier 1 factor |
| 32 | TBS Factoring — Services | https://tbsfactoring.com/services | Tier 1 factor |
| 33 | Ditat DTM — File Upload Factoring (multi-factor SFTP spec) | https://help.ditat.com/dtm/integrate-with-file-upload-factoring | Tier 2 partner |
| 34 | McLeod DocumentPower — receivables-financing partners | https://www.mcleodsoftware.com/documentpower-integrations/ | Tier 2 partner |
| 35 | Apex EDI — ANSI X12 doc reference (NOTE: unrelated to Apex Capital — see §6) | https://developers.apexedi.com/Reference/Topic/AnsiDocs | Disambiguation |

---

## 2. Per-factor profile

### 2.1 Apex Capital

| # | Question | Answer | Citation |
|---|---|---|---|
| 1 | Term used | **"Invoice batch" / "submission"** — Apex's own materials use *"invoice"*, *"factored invoice"*, *"batch"*. The phrase "Schedule of Accounts" does not appear in any Apex public surface. | [3], [4], [5] |
| 2 | Format | Two paths: **(a) AMP web portal** ("Account Management Portal") for manual entry / per-invoice creation. **(b) Direct API** via Apex's partner-integration program (NDA-gated; no public spec). Mobile Factoring app for one-off submissions. | [1], [3], [5] |
| 3 | Required fields per invoice | Public materials (the Apex how-to-build-a-freight-bill blog, AMP help) list the freight-bill fields: bill-to (broker name + MC# + address), invoice number, invoice date, load number / pickup-date, shipper, consignee, line-haul rate, accessorials, total amount due, remit-to (with Apex NOA stamp). The exact API field schema is not public. | [3] (AMP onboarding); [5] (Vektor confirms "automatically generates invoices with all required attachments"). |
| 4 | Batch size limits | Not publicly documented. Vektor language ("submitting multiple invoices in batches") implies batches are supported; cap unknown. | [5] |
| 5 | Required attached documents | **Rate Confirmation + Bill of Lading (BOL/POD)** at minimum, per Vektor; accessorial receipts (lumper, detention) when applicable. | [5] |
| 6 | API endpoint + auth | **Not publicly documented.** Apex confirms "we support custom integrations and API connectivity" but releases specs only after partner-program enrollment. Direct contact required: 844-204-6608. | [1] |
| 7 | Portal-only fallback | **AMP web portal + Mobile Factoring app** are the universal fallback; portal accepts per-invoice creation with PDF doc upload. 24/7 factoring product ("Apex 24/7") accepts submissions any time, funds within minutes. | [3], [4] |
| 8 | Response format | Async on the human side — operator submits batch, then "logs into Apex Capital portal to review and confirm the batch before funding begins." Per-invoice approve/decline is exposed in AMP UI. Programmatic per-invoice status callbacks: gated behind partner agreement. | [5] |
| 9 | Resubmission rules | Not publicly documented; AMP UI permits invoice edits prior to confirmation. | (inferred from [3]) |
| 10 | Public integration partners | **Alvys, Ditat, ezLoads, LoadOps, LoadStop, McLeod Software, QuickManage, SmartBoard, Transport Pro, Vektor.** Also FlexTMS, Axele (now Optym LoadOps). | [2], [34] |

### 2.2 RTS Financial

| # | Question | Answer | Citation |
|---|---|---|---|
| 1 | Term used | **"Funding sheet" / "submission" / "purchase"**. RTS Pro's own UI uses *"Submit Purchase"* (not "Schedule of Accounts"). Vektor calls it a "funding sheet CSV." | [6], [8] |
| 2 | Format | **(a) RTS Pro web/mobile** for native client submission. **(b) FTP/SFTP integration** with TMS partners — invoice PDFs + a **funding-sheet CSV** that lists all invoices in the batch. **(c) Direct API** for partners with credentials. | [6], [7], [8], [9], [10] |
| 3 | Required fields per invoice | Public-doc level: invoice number, broker (debtor) MC#, load/PO reference, invoice amount, invoice/bill date, terms (net days). Exact CSV column schema for the funding-sheet is partner-NDA only. | [8], [11] (Datatruck confirms FTP integration with credential exchange) |
| 4 | Batch size limits | Not publicly documented; integration partners describe entire batches uploading at once with no cited cap. | [8] |
| 5 | Required attached documents | **Rate Confirmation + BOL/POD** per Vektor (Tier-2 confirmed). RTS' own customer guidance: "clean documentation — legible BOL, complete rate confirmation". | [7], [8] |
| 6 | API endpoint + auth | **FTP credentials issued by RTS account rep**; REST API exists for partners with credentials provisioned by RTS. No public OpenAPI spec. | [8], [10] (ProTransport: "secure API credentials from RTS Financial so packet handoffs stay automated") |
| 7 | Portal-only fallback | **RTS Pro mobile/web app**: scan POD + rate-con, fill load fields, tap Submit. | [6] |
| 8 | Response format | Async; account rep reviews funding instructions; per-invoice status comes through RTS Pro reporting. | [6] |
| 9 | Resubmission rules | Not publicly documented; common-industry practice (corrected docs / re-upload via Pro) implied. | (inferred) |
| 10 | Public integration partners | **ProTransport, DENEMO, Datatruck, Vektor**, and McLeod DocumentPower. Pilot Company (RTS-Pilot fuel program) integrates RTS for joint customers. | [8]–[11], [34] |

### 2.3 Triumph Business Capital

| # | Question | Answer | Citation |
|---|---|---|---|
| 1 | Term used | **"Invoice draft" / "schedule" / "factor invoices"**. MyTriumph UI uses *"submit unpaid invoices"*; TAI integration uses *"Carrier Sync file"*. AscendTMS uses *"Factor Invoices"* button. | [12], [13], [16], [18] |
| 2 | Format | **(a) MyTriumph portal / mobile app** — primary client interface. **(b) SFTP integration** with TMS partners — `invoice.csv` plus combined PDF, dropped under `TMS_INPUT/CARRIERS` folder. **(c) AscendTMS "Seamless Factoring Portal"** uses credential pass-through (each user has unique MyTriumph login). | [13], [15], [16], [18] |
| 3 | Required fields per invoice (csv) | TAI documents the SFTP `invoice.csv` schema operationally (exact columns NDA-only). Triumph Pay (broker side) sync exposes: External Payee Key (broker carrier ID), company name, MC#, DOT#, address, primary contact. Invoice draft sync includes: invoice draft ID, broker carrier ID, net terms, QuickPay flag, shipment status. | [16], [17] |
| 4 | Batch size limits | TAI runs **invoice sync every 5 minutes** + carrier sync **once per hour** — implies streaming / micro-batch rather than large fixed batch. No published max-per-batch. | [16] |
| 5 | Required attached documents | TAI integration packages **5 PDFs per invoice**: Carrier Bill, POD, Carrier Confirmation (auto-generated if missing), Customer Rate Quote Sheet (auto-generated if missing), Customer Invoice. | [16] |
| 6 | API endpoint + auth | No public REST API for Triumph Business Capital. Triumph Pay (sister product, broker→carrier rails) has API for network brokers (auth method NDA-gated). | [13], [17] |
| 7 | Portal-only fallback | **MyTriumph portal + mobile app** — load + submit invoice docs; "purchase decision in seconds on most invoices." | [13] |
| 8 | Response format | **Sync-feeling** for the human ("decision in seconds on most invoices") — backend likely async with fast SLA. Per-invoice approve/decline visible in MyTriumph. | [13] |
| 9 | Resubmission rules | Not publicly documented. Note: TAI integration explicitly **only supports Truckload** — LTL and Marketplace shipments must be handled manually. | [16] |
| 10 | Public integration partners | **AscendTMS (InMotion Global), TAI Software, Alvys, McLeod Software DocumentPower**. Triumph Pay (related but separate product) integrates with TAI, network brokers via API. | [15], [16], [17], [18], [34] |

### 2.4 OTR Capital / OTR Solutions

| # | Question | Answer | Citation |
|---|---|---|---|
| 1 | Term used | **"Invoice" / "Submit invoice" / "Document Exchange"**. Public docs name the relevant API "**Document Exchange API**" (covers invoice + rate-con + POD submission). No "Schedule of Accounts" wording. | [20], [21], [23] |
| 2 | Format | **(a) Client Portal + Mobile App** — manual per-invoice. **(b) REST API** — three named APIs: **Carrier Setup API**, **Rate Verification API**, **Document Exchange API**. Authenticated with **Bearer token** + **Subscription key** (provisioned per partner). | [20], [21], [22], [23], [24] |
| 3 | Required fields per invoice (API "Create Invoice") | Public docs explicitly list **PO Number, Carrier DOT, Broker MC, Invoice Number, Invoice Date, Invoice Amount, Lane Information** — all marked required. | [23] |
| 4 | Batch size limits | Not specified in public docs; the documented "Create Invoice" verb is per-invoice (batch happens client-side by looping). | [23] |
| 5 | Required attached documents | API supports file upload with `documentType="invoice-file-upload"`. Public client-portal flow names **Rate Confirmation + POD + accessorials** as universal attachments per submission. Empty-body 200 on successful upload. | [19], [23] |
| 6 | API endpoint + auth | **REST + Bearer token** authentication; partner gets a **subscription key** + sandbox account on developer portal at `docs.otrsolutions.com`. Access requested via `integrations@otrsolutions.com` / 770-501-2088. | [20], [22], [23] |
| 7 | Portal-only fallback | **Client Portal + Mobile App** — fill load details, attach Rate Con + POD + accessorials, submit. Same workflow for brokers (broker uploads carrier invoice + POD + shipper rate-con). | [19] |
| 8 | Response format | API exposes **GetInvoiceStatus** verb — partner can poll per-invoice approval. Document upload returns 200 empty body. Funding within 24 hours. | [19], [23] |
| 9 | Resubmission rules | Not detailed in public excerpt; API has both create + status verbs, implying corrected re-create is the standard pattern. | [23] |
| 10 | Public integration partners | **LoadOps (Optym), Motive, Vektor, McLeod DocumentPower, Simplex Group**. Public developer portal is the "open" channel — anyone can request access. | [26], [27], [28], [34] |

### 2.5 TBS Capital Funding

| # | Question | Answer | Citation |
|---|---|---|---|
| 1 | Term used | **"Schedule"** — **the only Big-5 to use this term in public-facing docs**. The TBS: Get Paid app says: *"you'll be guided step-by-step to submit your **schedule** in the app"*; the same app's PaperView feature lets users *"track the status of **schedules** and invoices."* | [29], [30] |
| 2 | Format | **(a) TBS: Get Paid mobile app** (iOS + Android) — primary submission rail; includes **VeriFast™** GPS pickup/drop verification. **(b) Email-all-paperwork** to a designated TBS submission inbox as fallback. **(c) TBS Web Portal** at `web-portal.tbsokc.com` for desktop access. | [29], [30], [31] |
| 3 | Required fields per invoice | App-driven step-through (load number, broker MC#, broker name, pickup date, delivery date, rate, accessorials). Exact field schema not published as a CSV/JSON spec; TBS' UX is form-driven. | [30], [32] |
| 4 | Batch size limits | Not documented; mobile-first design implies one-at-a-time but multi-load schedules supported. | [30] |
| 5 | Required attached documents | **Rate confirmation + signed BOL/POD + accessorial receipts** photographed in-app. VeriFast adds GPS verification of pickup/delivery as a substitute for paper proof. | [29], [30] |
| 6 | API endpoint + auth | **No public API surface.** TBS lists no integration partner program, no developer portal, no published TMS-side spec. | (negative finding — no public source) |
| 7 | Portal-only fallback | **Get Paid app + Web Portal + email-paperwork-to-rep**. | [29], [31] |
| 8 | Response format | Same-day processing ("you'll always get processed the same day you submit your request"); 24h funding. PaperView gives invoice/schedule status inside the app. | [30], [29] |
| 9 | Resubmission rules | Not publicly documented; account rep handles correction loop. | (inferred) |
| 10 | Public integration partners | **None publicly listed.** TBS does not appear in McLeod DocumentPower's receivables-financing partner roster. (Compass, OTR, RTS, Triumph, Apex all do.) | [34] (TBS absent) |

---

## 3. Common ground — the universal Schedule-of-Accounts contract

Every Big-5 transportation factor accepts a submission that contains, at minimum:

1. **One PDF per invoice**, attached as a binary blob.
2. **An invoice header record** with these universal fields:
   - Invoice number (carrier-/broker-assigned, must be unique)
   - Invoice date
   - Invoice (factored) amount in USD
   - Broker / debtor name
   - Broker MC# (and/or DOT#)
   - Load reference (PO# / load number / pickup-delivery dates)
3. **Mandatory document attachments** per invoice:
   - **Rate Confirmation** (the broker–carrier rate-con with carrier signature)
   - **Bill of Lading** (BOL) — pickup-side proof
   - **Proof of Delivery** (POD) — delivery-side signed BOL
   - Accessorial receipts (lumper, detention, layover) where applicable
4. **NOA-stamped remit-to** on the invoice itself, redirecting payment to the factor's lockbox/ACH (UCC § 9-406 requirement, validated in companion doc EEE2).

That set is the **universal SoA contract**. CHL's outbound Schedule-of-Accounts builder must always produce these as a base, then layer per-factor adapter logic on top.

---

## 4. Variation matrix — where the Big-5 differ

| Dimension | Apex | RTS | Triumph | OTR | TBS |
|---|---|---|---|---|---|
| **Vocabulary used** | "batch / invoice" | "funding sheet / Submit Purchase" | "invoice draft / factor invoices / Carrier Sync" | "Document Exchange / Create Invoice" | **"schedule"** (closest to canonical) |
| **Primary integration** | API (gated) + AMP portal | FTP (CSV+PDFs) + RTS Pro | SFTP (csv+pdf) + MyTriumph + Seamless Portal | **Public REST API** + Client Portal | Mobile app + email + web portal |
| **Sample cadence (TMS-published)** | Per-batch on submit | On-demand | invoice sync every 5 min, carrier sync hourly | Per-invoice REST | Per-invoice (mobile-first) |
| **Public dev portal** | No | No | No | **Yes (`docs.otrsolutions.com`)** — gated subscription key | No |
| **Auth method** | Partner-NDA | FTP creds + REST creds | SFTP creds + per-user MyTriumph login | **Bearer + Subscription key** | App login |
| **Required attached docs (TMS-confirmed)** | Rate Con + BOL | Rate Con + BOL | **5 PDFs**: Carrier Bill, POD, Carrier Confirmation, Customer Rate Quote, Customer Invoice | Rate Con + POD + accessorials | Rate Con + BOL + accessorials (+ VeriFast GPS) |
| **Async vs sync** | Async (operator confirms in AMP) | Async (rep reviews) | Near-sync ("decision in seconds") | Mixed: REST per-invoice + GetStatus | Sync app feedback, async funding |
| **Per-invoice approve/decline visible** | AMP UI | RTS Pro reports | MyTriumph UI | API (`GetInvoiceStatus`) | App PaperView |
| **LTL / non-truckload supported** | Yes (Apex serves diverse) | Yes | **No (TAI integration is Truckload-only)** | Yes | Yes |
| **24/7 submission window** | **Yes — Apex 24/7 product** | RTS Pro yes; funding business hours | Yes via portal; funding business hours | Yes via API + portal | Yes via app |

**Biggest single variation point:** **whether a public/partner-accessible API exists at all.** OTR is the only Big-5 with a public developer portal (still gated for production credentials but openly documented). Apex has API but specs are NDA-only. RTS uses FTP+CSV with optional partner REST. Triumph is SFTP-CSV-pluss-portal. **TBS has no public integration story whatsoever** — for TBS, CHL's only path is the Get Paid app or email-paperwork.

---

## 5. Public docs vs gated docs

| Factor | Public spec available? | Where gated info lives | What CHL must request |
|---|---|---|---|
| **Apex Capital** | Partial — partner-program page + AMP UI screenshots | Field-level API schema | Partner-program enrollment via partners@apexcapitalcorp.com / 844-204-6608 — request "API integration partner kit" |
| **RTS Financial** | No | FTP funding-sheet CSV column schema, REST API spec | Email RTS account rep — request "FTP integration credentials + funding-sheet CSV template" |
| **Triumph Business Capital** | Partial — TAI/Alvys docs describe SFTP folder structure + cadence | invoice.csv exact column schema | Direct request to Triumph integrations team — "MyTriumph SFTP integration spec for TMS partner" |
| **OTR Solutions** | **Yes (Tier-2 dev portal at `docs.otrsolutions.com`)** — public reference describes endpoint shape; production credentials gated | Production endpoint URLs, sandbox seeds | Email integrations@otrsolutions.com / 770-501-2088 — request subscription key + sandbox |
| **TBS Capital Funding** | **No** — no published integration spec exists | Anything programmatic | Direct call to TBS account rep — ask "do you have any TMS-partner integration program?" — likely answer: no API, use Get Paid app |

**Operator must request:** Apex API kit (NDA), RTS FTP funding-sheet template, Triumph invoice.csv schema, OTR subscription key, TBS clarification on whether any partner channel exists. These are five separate vendor outreach actions; they should be queued as part of the FFF-stream onboarding sprint.

---

## 6. Disambiguation note (Apex EDI vs Apex Capital)

WebSearch surfaced "Apex EDI" / `developers.apexedi.com` references. **This is a HEALTHCARE-EDI vendor, NOT Apex Capital factoring.** ApexEDI is an ANSI-X12 healthcare-claims EDI gateway. There is **no public ApexNetwork / Apex EDI X12 spec for transportation factoring**. CHL must not confuse the two when contacting Apex Capital partner support.

Source: [35] https://developers.apexedi.com/Reference/Topic/AnsiDocs (ANSI X12 healthcare docs only).

---

## 7. Recommended CHL implementation strategy

### 7.1 Universal builder + per-factor adapter shape

Build CHL's outbound Schedule-of-Accounts pipeline as **one canonical SoA model** + **N factor-specific adapters** (one per partner). Architecture:

```
chl/factor/
  models/
    schedule_of_accounts.py       # canonical pydantic model (universal-contract fields from §3)
    invoice_line.py
    attachment.py                 # binary + doc_type enum
  builder.py                      # builds the canonical SoA from CHL load+invoice DB rows
  adapters/
    apex.py                       # AMP portal automation (selenium/playwright) + API stub
    rts.py                        # SFTP funding-sheet CSV + invoice PDFs
    triumph.py                    # SFTP TMS_INPUT/CARRIERS folder + invoice.csv + 5 PDFs
    otr.py                        # REST POST /document-exchange + Bearer+SubKey headers
    tbs.py                        # email submission fallback (no API path) + Get-Paid-app handoff
  router.py                       # dispatches canonical SoA to the right adapter based on broker.factor_of_record
```

### 7.2 Canonical SoA model (the must-have fields)

```python
class InvoiceLine(BaseModel):
    invoice_number: str          # CHL-issued, unique
    invoice_date: date
    amount_usd: Decimal
    debtor_mc: str               # broker MC#
    debtor_dot: str | None
    debtor_name: str
    debtor_address: str
    load_number: str             # CHL load PK
    po_number: str | None
    pickup_date: date
    delivery_date: date
    pickup_state: str
    delivery_state: str
    terms_net_days: int          # 30/45/60
    accessorials: list[Accessorial]
    attachments: list[Attachment]    # rate_con, bol, pod, lumper, detention
    noa_stamp_applied: bool      # MUST be True for factored loads

class ScheduleOfAccounts(BaseModel):
    schedule_id: str             # CHL-issued
    schedule_date: datetime
    factor_id: str               # FK to chl.factors
    carrier_mc: str              # CHL = MC 1817555
    carrier_dot: str             # CHL = DOT 4569843
    invoices: list[InvoiceLine]
    total_amount_usd: Decimal
```

### 7.3 Adapter contract

Every adapter implements:

```python
class FactorAdapter(Protocol):
    def submit(self, soa: ScheduleOfAccounts) -> SubmissionResult: ...
    def get_status(self, schedule_id: str, invoice_number: str) -> InvoiceStatus: ...
    def resubmit(self, schedule_id: str, invoice_number: str, corrected: InvoiceLine) -> SubmissionResult: ...
```

### 7.4 Phasing recommendation

**Phase 1 (this sprint) — OTR-first.** OTR is the only Big-5 with a public dev portal. Build the canonical model + the OTR REST adapter against the public docs; treat it as the reference adapter that proves the universal contract. Operator requests OTR sandbox subscription key in parallel.

**Phase 2 — RTS + Triumph (SFTP siblings).** Both are SFTP+CSV+PDF; share most of the adapter scaffolding (file builder + SFTP uploader). Operator requests credentials from each.

**Phase 3 — Apex.** Two adapters: (a) AMP portal automation (selenium) as an immediate fallback, (b) Apex API once partner-program credentials are issued. Treat AMP automation as throwaway; replace with API once the partner kit lands.

**Phase 4 — TBS.** No API; build an email-submission adapter that bundles the canonical SoA into a single email with PDF attachments, sent to the TBS submission inbox. Capture Get-Paid-app workflow as a manual fallback for operator.

### 7.5 Guardrails

- **NOA stamp gate.** No invoice gets submitted to any factor without `noa_stamp_applied=True`. The CHL invoice PDF generator applies the stamp (per UCC § 9-406, validated in EEE2).
- **Idempotency.** Schedule_id + invoice_number is the natural dedup key. Adapters MUST be idempotent — duplicate submission must return the existing status, not create a phantom second record.
- **Per-factor rate-limit profile.** OTR is per-invoice REST (rate-limit each call). Triumph TAI uses 5-minute sync windows (batch into the next window). RTS / Apex large-batch.
- **Audit log.** Every adapter call logs: timestamp, factor_id, schedule_id, invoice_number, request body (PII-redacted), response status, response body. CHL's existing audit infrastructure should pick this up.
- **Resubmission requires manual approval.** Any factor's "rejected" callback must NOT auto-resubmit. Operator (or PM) reviews + corrects + manually re-fires. Auto-resubmit is a fraud-pattern accelerator.
- **TBS pathway is degraded service.** Until/unless TBS publishes an API or partner program, treat TBS-factored loads as a higher-touch tier — operator should aim to migrate TBS-factored brokers to Apex/OTR/RTS/Triumph when feasible.

### 7.6 Forward bet

OTR's public developer portal is the maturity signal — they are 5+ years ahead of the other Big-5 on tech-partner enablement. Expect Apex, RTS, Triumph to follow within 12-24 months (industry pressure from Vektor, LoadOps, Alvys driving demand). CHL should design the canonical SoA + adapter pattern now so that when each factor publishes its API, the work is "swap one adapter," not a full re-architecture.

---

## 8. Open follow-ups for FFF-stream

1. **Operator action — request integration kits** from each of the 5 factors (template language drafted in §5).
2. **FFF2 (next sub-agent)** — once at least one factor's NDA-gated spec is in hand, document the exact field-level CSV/JSON schema and update this doc with footnoted "after-NDA" sections (clearly marked confidential).
3. **FFF3** — survey OTR's full public REST schema on `docs.otrsolutions.com/reference/*` after operator provisions a subscription key (the public reference index returns paths but not full bodies without auth).
4. **EOD-cross-link** — link this doc into BUILD_STATUS_REPORT.md under the Factor Integration epic.

---

*End FFF1 — research artifact 2026-05-09.*
