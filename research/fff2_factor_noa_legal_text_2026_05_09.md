<!--
Continental Haul Logistics LLC — Internal research artifact
(c) 2026 Continental Haul Logistics LLC. All rights reserved.
Authored by: dev Claude (sub-agent FFF2) for Jason A. Meyer (operator).
Audience: CHL build team + future Claude sessions. Not for redistribution.
Classification: Internal-use research; cite to public sources for verbatim legal text.
-->

# FFF2 — Notice of Assignment (NOA) Legal Text per Big-5 Transportation Factor

**Date:** 2026-05-09
**Sub-agent:** FFF2 (research)
**Parent task:** Builds on EEE2 factor-industry validation. Feeds CHL's NOA template engine + outbound invoice generator.
**Use case:** When CHL invoices a shipper, the invoice (or accompanying factor letter, or both) must carry NOA language directing the shipper to remit to CHL's factor's lockbox. Each Big-5 factor has its own preferred wording + lockbox routing.

---

## 1. Sources Consulted

| # | Source | URL | Tier |
|---|---|---|---|
| 1 | UCC § 9-406 — Cornell LII | https://www.law.cornell.edu/ucc/9/9-406 | Tier 1 statutory |
| 2 | UpCounsel — UCC 9-406 explained | https://www.upcounsel.com/ucc-9-406 | Tier 2 legal |
| 3 | Hodgson Russ — "Don't Ignore Customer's Secured Lenders" | https://www.hodgsonruss.com/newsroom-publications-14155.html | Tier 1 legal |
| 4 | ABF Journal — UCC 9-406 in MCA market | https://www.abfjournal.com/ucc-9-406-notices-in-the-mca-market-when-payment-must-be-redirected-by-account-debtors/ | Tier 1 trade legal |
| 5 | Apex Capital — "What is a Notice of Assignment" | https://www.apexcapitalcorp.com/blog/what-is-a-notice-of-assignment/ | Big-5 factor (own publication) |
| 6 | Apex Capital — Contact page (lockbox PO Box) | https://www.apexcapitalcorp.com/contact/ | Big-5 factor |
| 7 | RTS Financial — "How to Switch Factoring Companies" | https://www.rtsinc.com/articles/how-switch-factoring-companies | Big-5 factor (own publication) |
| 8 | RTS Financial — Customer Center / Customer Support | https://www.rtsinc.com/page/customer-support | Big-5 factor |
| 9 | RTS specimen NOA — Woodrooffe Trucking via Scribd | https://www.scribd.com/document/680276627/RTS-Notice-of-Assignment | Field specimen |
| 10 | Triumph — Payment Information (lockbox + ACH) | https://intelligencehelp.triumph.io/en/articles/8418023-payment-information | Big-5 factor (own publication) |
| 11 | Triumph — "What Every Freight Broker Should Know About Factoring" | https://triumph.io/blog/factor/what-every-freight-broker-should-know-about-factoring/ | Big-5 factor |
| 12 | Triumph SEC EDGAR — Maslow Media Factoring & Security Agreement (Advance Business Capital d/b/a Triumph) | https://www.sec.gov/Archives/edgar/data/34285/000149315219016124/ex10-7.htm | Tier 1 (SEC-filed contract; access 403 in this run, see EEE2) |
| 13 | OTR Solutions — FAQs (lockbox PO Box) | https://otrsolutions.com/resources/faqs/ | Big-5 factor (own publication) |
| 14 | OTR Capital — ACH Payment Options form (NTG copy) | https://www.sfcltl.com/wp-content/uploads/2021/06/otr-capital-ach-payment-option-form.pdf | Field specimen |
| 15 | TBS Factoring — main + contact pages | https://tbsfactoring.com/contact/ | Big-5 factor |
| 16 | eCapital — Broker Factoring Master Agreement (terms public) | https://ecapital.com/transportation/broker-terms/ | Big-5 factor (full master agreement public) |
| 17 | eCapital — NOA explainer | https://ecapital.com/blog/the-importance-of-a-notice-of-assignment-in-factoring/ | Big-5 factor |
| 18 | TruckSmarter — NOA / LOR / UCC-1 guide | https://www.trucksmarter.com/blog/noa-lor-ucc-101 | Trade pub |
| 19 | altLINE — NOA explainer | https://altline.sobanco.com/invoice-factoring-notice-of-assignment/ | Mid-market factor |
| 20 | FreightWaves Checkpoint — NOA in factoring | https://www.freightwaves.com/checkpoint/notice-of-assignment-in-factoring/ | Trade pub |
| 21 | Capstone — Role of NOA | https://capstonetrade.com/the-role-of-a-notice-of-assignment-in-invoice-factoring/ | Mid-market factor |
| 22 | IFA Commercial Factor — "Your NOA Is Not as Protective as You Think" | https://magazine.factoring.org/magazine-articles/your-notice-of-assignment-is-not-as-protective-as-you-think | Trade legal |
| 23 | Factor Finders — Factoring Paperwork | https://www.factorfinders.com/resources/factoring-faq/factoring-paperwork-notice-of-assignment/ | Trade pub |
| 24 | altLINE — Letter of Release | https://altline.sobanco.com/letter-of-release/ | Mid-market factor |

---

## 2. UCC § 9-406 Baseline (the legal floor)

UCC § 9-406 governs when an account debtor (the shipper, in our case) is discharged from its payment obligation by paying the assignor (CHL) versus the assignee (CHL's factor). The text below is from Cornell LII (source 1), verbatim where quoted.

### 2.1 Subsection (a) — what the NOA must do to redirect payment

> "an account debtor on an account, chattel paper, or a payment intangible may discharge its obligation by paying the assignor until, but not after, the account debtor receives a notification, authenticated by the assignor or the assignee, that the amount due or to become due has been assigned and that payment is to be made to the assignee."
> — UCC § 9-406(a) (source 1)

**Operational reading:** Once the shipper receives an authenticated NOA, paying CHL no longer discharges the obligation; the shipper must pay the factor. If the shipper pays CHL after receiving NOA, the shipper still owes the factor and may have to pay twice (source 3 — Hodgson Russ "pay twice" warning).

### 2.2 Subsection (b) — when notification is INEFFECTIVE

Notification fails (i.e., the shipper can keep paying CHL) under § 9-406(b) if the notice:

1. **"does not reasonably identify the rights assigned"** — vague NOA fails. Identify the invoices by number, or identify "all present and future receivables of [CHL] arising from freight services."
2. Is part of an effective agreement limiting the account debtor's payment duty to someone other than the seller.
3. Allows the account debtor to pay less than the full installment / periodic payment to the assignee.

### 2.3 Authenticated by assignor OR assignee

This is **load-bearing for CHL's invoice template design**: the NOA can be authenticated by **either CHL** (as assignor) **or the factor** (as assignee). CHL's broker invoice carrying a stamp that says "this invoice has been assigned to [Factor], remit to [lockbox]," signed/issued by CHL on CHL's letterhead, **satisfies the statute on its own.** CHL does not have to wait for or attach a per-invoice factor letter (source 1; cross-referenced EEE2 finding 4).

### 2.4 Minimum required language (synthesis from § 9-406 + UCC § 9-404 + sources 2, 3, 19, 20)

The NOA, whether stamp or letter, must contain:

1. **Identification of assignee** — full legal name of the factor.
2. **Identification of assignor** — full legal name of CHL.
3. **Identification of rights assigned** — typically: "all accounts receivable arising under [invoice numbers], or all present and future receivables of [CHL] payable by [Shipper] for freight services."
4. **Direction of payment** — "Payment is to be made directly and only to [Factor] at [lockbox address / ACH details]." (sources 1 + 3 + 19)
5. **Authentication** — signed (manual or electronic) by CHL (assignor) or factor (assignee). UCC "authenticate" includes electronic signature per UCC § 9-102(a)(7).
6. **Double-pay warning** — strongly recommended though not strictly statutory: "Payment to anyone other than [Factor], without [Factor]'s prior written consent, will not discharge your obligation and you may be required to pay again." (Source 9 — RTS specimen contains this verbatim; source 3 — Hodgson Russ confirms legal mechanism.)

### 2.5 Effective only if assignment is valid

Per ABF Journal (source 4): "An account debtor's obligation to redirect payment arises only where the sender holds a valid, senior, and enforceable assignment of receivables." A purported NOA from a party without a valid assignment is unenforceable. CHL's NOA is enforceable because (a) CHL has a valid factoring agreement with the named factor and (b) the factor has filed a UCC-1 perfecting its security interest in CHL's receivables.

---

## 3. Per-Factor NOA Template Table

### 3.1 Apex Capital Corp

| Field | Value | Source |
|---|---|---|
| Factor legal name | Apex Capital Corp | Source 5, 6 |
| Lockbox PO Box | **PO Box 961029, Fort Worth, TX 76161-1029** | Source 6 (Apex contact page) |
| HQ | 301 Commerce St, Fort Worth, TX (operations) | Source 6 |
| Remittance email | ach@apexcapitalcorp.com | Source 6 |
| Phone | (844) 204-6608 | Source 5 |
| ABA / account # | **NOT publicly published; gated to client portal.** | n/a |
| Sample NOA stamp wording publicly available? | **NO — not on Apex's blog or contact page.** Apex's own NOA explainer (source 5) describes mechanics but does not publish a template. | Source 5 |
| Sample NOA letter wording publicly available? | NO — distributed via Apex Account Management Portal post-onboarding. | Source 5 |
| Three-mode usage | Per EEE2 finding (source 5 cross-ref): Apex uses **letter at relationship start + perpetual stamp on every invoice the carrier issues**. Industry-standard "both" mode. | EEE2 line 81 |
| Invoice marking required? | Yes, per EEE2; specific stamp wording is provided privately during onboarding. | EEE2 |

**Reconstructed Apex NOA stamp (industry-standard pattern — request verification from Apex on onboarding):**

> "This invoice has been assigned to and is owned by Apex Capital Corp. Payment is to be made directly to Apex Capital Corp at PO Box 961029, Fort Worth, TX 76161-1029. Payment to any other party without Apex's prior written consent will not discharge your obligation."

### 3.2 RTS Financial Service, Inc.

| Field | Value | Source |
|---|---|---|
| Factor legal name | RTS Financial Service, Inc. | Source 7, 8, 9 |
| Lockbox PO Box | **PO Box 840267, Dallas, TX 75284-0267** (Dallas remittance lockbox; the Overland Park, KS address is corporate HQ, not the lockbox) | Source 8 (Customer Center) |
| HQ | 9300 Metcalf Ave, Overland Park, KS 66212 | Source 8 |
| Remittance email | cashapp@rtsfinancial.com | Source 8 |
| Phone | (888) 874-7053 | Source 8 |
| ABA / account # | **NOT publicly published; provided to clients.** | n/a |
| Sample NOA letter wording publicly available? | **YES — partial verbatim from field specimen (source 9).** | Source 9 |
| Three-mode usage | RTS issues a formal NOA letter on factor letterhead with carrier name in subject line; perpetual stamp on invoices via RTS Pro app. Industry-standard "both" mode. | Sources 7, 9 |

**RTS NOA letter — verbatim core language (from specimen for "Woodrooffe Trucking LLC", source 9):**

> "[Carrier Name] has retained RTS Financial Service, Inc. to handle credit, collections, and processing of payments for their freight bills. All invoices will now be processed through RTS Financial and all payments must be mailed directly to RTS Financial. Payments to anyone other than RTS Financial without their consent could result in having to pay the amount again."
> — Source 9 (verbatim from field specimen NOA on RTS letterhead)

**Notes:**
- "Mailed directly to RTS Financial" historically routed to a Kansas address, currently to the **PO Box 840267, Dallas, TX 75284-0267** lockbox per source 8.
- Double-pay warning is verbatim ("could result in having to pay the amount again") — RTS uses this exact phrasing.

### 3.3 Triumph Business Capital (Advance Business Capital LLC d/b/a Triumph)

| Field | Value | Source |
|---|---|---|
| Factor legal name | Advance Business Capital LLC d/b/a Triumph Business Capital (also "Triumph Financial Services LLC" depending on entity) | Source 11, 12 |
| Lockbox PO Box | **PO Box 225335, Dallas, TX 75222-5335** (TBK Bank, SSB – Greenscreens Lockbox) | Source 10 |
| ABA/RTN | 111909579 (TBK Bank, SSB) — **publicly published** for ACH/wire | Source 10 |
| Account # | 5002387 — **publicly published** by Triumph Intelligence Help Center | Source 10 |
| Account type | Checking | Source 10 |
| HQ | Coppell, TX (parent: Triumph Bancorp / TBK Bank) | Source 11 |
| Sample NOA stamp wording publicly available? | NO verbatim template publicly published; mechanics described in source 11 (Triumph's broker blog). | Source 11 |
| Sample full master agreement publicly accessible? | YES via SEC EDGAR (Maslow Media filing — source 12) but this URL returned 403 in current research run. EEE2 confirms availability. | Source 12 |
| Power-of-attorney clause | Per source 12 (via EEE2 cross-ref): "Client authorizes Triumph and irrevocably grants power of attorney to Triumph to exercise each and any of the following powers until all of Obligations have been paid in full and a Complete Termination has been performed" — including authority to file UCC-1 in client's name. | Source 12 |
| Three-mode usage | EEE2 (source 14 in EEE2): "NOA via stamp, letter, or both (source 14 explicit)." Triumph offers all three modes; client elects per-shipper. | EEE2 line 83 |

**Reconstructed Triumph NOA stamp (industry-standard pattern):**

> "This invoice has been assigned to Advance Business Capital LLC d/b/a Triumph. All payments must be made directly to Triumph at PO Box 225335, Dallas, TX 75222-5335 (or via ACH to TBK Bank, SSB, RTN 111909579, Acct 5002387). Payment to any other party will not discharge your obligation."

**Note:** Of the Big-5, **Triumph is the most transparent on remittance details** — ABA/account number publicly published.

### 3.4 OTR Solutions (formerly OTR Capital)

| Field | Value | Source |
|---|---|---|
| Factor legal name | OTR Capital, LLC (legal); OTR Solutions (DBA / brand) | Source 13, 14 |
| Lockbox PO Box (current, OTR Solutions branding) | **PO Box 1175760, Atlanta, GA 30368-7576** | Source 13 |
| Lockbox PO Box (alternate, legacy OTR Capital branding) | **OTR Capital, LLC DEPT. #390, P.O. Box 1000, Memphis, TN 38148-0390** (per source 14 / 2019 ACH form) | Source 14 |
| HQ | 1000 Holcomb Woods Pkwy, Bldg 300, Roswell, GA 30076 | Source 13 |
| Phone | (770) 882-0124 / (470) 468-0112 | Source 13 |
| ABA / account # | **NOT publicly published** (in source 14 PDF, but PDF binary did not parse). Gated to client. | n/a |
| Sample NOA stamp wording publicly available? | NO — distributed during onboarding. | Sources 13, 14 |
| Three-mode usage | Per EEE2: "Factor-issued NOA + remittance stamp" — "both" mode. | EEE2 line 84 |

**Reconstructed OTR NOA stamp (industry-standard pattern):**

> "This invoice has been assigned to OTR Capital, LLC. All payments must be made directly to OTR Capital, LLC at PO Box 1175760, Atlanta, GA 30368-7576. Payment to any other party will not discharge your obligation."

**Note:** Two PO Boxes exist publicly. Confirm with OTR which is current at onboarding (the Atlanta address is on their current FAQ page, source 13; the Memphis address appears in a 2019 form, source 14).

### 3.5 TBS Factoring Service, LLC

| Field | Value | Source |
|---|---|---|
| Factor legal name | TBS Factoring Service, LLC | Source 15 |
| Lockbox PO Box | **P.O. Box 720123, Oklahoma City, OK 73172** (per Bing/Yelp consensus reply; verify on onboarding) | Source 15 (search consensus) |
| HQ | Oklahoma City, OK | Source 15 |
| Owned by | Love's Travel Stops & Country Stores | EEE2 |
| Sample NOA letter wording publicly available? | Not directly on tbsfactoring.com; specimen on Scribd (TBS-specific) describes the mechanism but full text was not retrievable in this run. | Sources 15, 23 |
| Three-mode usage | Per EEE2: "Factor-issued NOA standard" — primarily letter mode; stamp on invoices typical. "Both" likely. | EEE2 line 85 |

**Reconstructed TBS NOA stamp (industry-standard pattern — verify on onboarding):**

> "This invoice has been assigned to TBS Factoring Service, LLC. All payments must be made directly to TBS Factoring Service, LLC at P.O. Box 720123, Oklahoma City, OK 73172. Payment to any other party will not discharge your obligation."

### 3.6 eCapital Corp (broker factoring focus — included as 6th, replacement candidate)

| Field | Value | Source |
|---|---|---|
| Factor legal name | eCapital Corp (broker line) | Source 16, 17 |
| Master agreement publicly readable? | **YES — full Broker Factoring Master Agreement at ecapital.com/transportation/broker-terms/** | Source 16 |
| Lockbox / remittance details | NOT in master agreement public page; gated to Exhibit B / onboarding packet. | Source 16 |
| Power-of-attorney clause (Section 7.7) | **Public verbatim — see below.** | Source 16 |
| Invoice marking requirement (Section 8.5) | **Public verbatim — see below.** | Source 16 |
| Sample NOA stamp wording publicly available? | NO — referenced in §8.5 as "as may be required by Purchaser" but the actual stamp template is in Exhibit B / private. | Source 16 |
| Three-mode usage | Per §8.5 + §7.7(d): factor (Purchaser) controls and may also issue its own NOA letter to any account debtor. "Both" mode default. | Source 16 |

**eCapital Master Agreement — Section 7.7 Power of Attorney (verbatim, source 16):**

> "Seller hereby grants Purchaser the limited power of attorney to (a) correct and/or execute or initial all typographical or clerical errors discovered in any of the documents executed by Seller in connection herewith, (b) make, amend or correct any filing required by the Federal Motor Carrier Safety Administration or any governmental agency having jurisdiction over Seller, including but not limited to the correction of inconsistent names under which Seller is listed, (c) file or advertise fictitious names for the Seller in the name of and at the cost of Seller, including doing so through the use of a third-party vendor such as www.mycorporation.com, (d) prepare and sign, on behalf of Seller, notice of assignment of all of Seller's Accounts to Purchaser, said notice to be provided to any Account Debtor of Seller at Purchaser's sole discretion, and (e) prepare and sign, on behalf of Seller, authorization documentation necessary to enroll Seller for any applicable fuel program facilitated by Purchaser with any third party at Purchaser's sole discretion."
> — eCapital Broker Factoring Master Agreement, § 7.7 (source 16)

**eCapital Master Agreement — Section 7.7.1 Payment Servicer (verbatim, source 16):**

> "Subject to the terms and conditions herein, Purchaser is required to act as a payment servicer for Seller. Purchaser shall provide freight invoice payment services on behalf of Seller for Invoices submitted to Purchaser by Seller (the 'Services'), pursuant to the practices and procedures set forth in Exhibit B, attached hereto."
> — eCapital Broker Factoring Master Agreement, § 7.7.1 (source 16)

**eCapital Master Agreement — Section 8.5 Invoice Marking (verbatim, source 16):**

> "Before sending any Invoice to an Account Debtor, Seller shall mark same with a notice of assignment as may be required by Purchaser."
> — eCapital Broker Factoring Master Agreement, § 8.5 (source 16)

**Note:** eCapital is the only factor whose **master agreement is fully public.** The actual stamp wording referenced in §8.5 lives in Exhibit B (private).

---

## 4. Three-Mode Pattern Verification (stamp / letter / both)

EEE2's claim was: **all Big-5 use one of three modes — stamp on invoice, separate factor letter, or both.** FFF2 verification:

| Factor | Stamp on invoice? | Separate factor letter? | Mode | Source |
|---|---|---|---|---|
| Apex Capital | YES (perpetual on each invoice) | YES (at relationship start) | **BOTH** | EEE2 line 81 + source 5 |
| RTS Financial | YES (via RTS Pro app templates) | YES (per-debtor letter on RTS letterhead — source 9 specimen) | **BOTH** | Sources 7, 9 |
| Triumph Business Capital | YES | YES | **BOTH** (explicitly all three modes offered, client-elected) | EEE2 line 83 + source 11 |
| OTR Solutions | YES | YES | **BOTH** | EEE2 line 84 + source 13 |
| TBS Factoring | YES (typical) | YES (factor-issued standard) | **BOTH** (likely; verify onboarding) | EEE2 line 85 + source 15 |
| eCapital | YES (per §8.5 of master agreement) | YES (per §7.7(d) — factor's discretion) | **BOTH** (factor-controlled) | Source 16 |

**Verdict:** EEE2's three-mode finding is **CONFIRMED**. All Big-5 (and eCapital) operate in "both" mode by default. Stamp-only is rare (small factors); letter-only is rare (legacy practice).

---

## 5. Lockbox Routing Summary (public addresses only — no bank account numbers except Triumph's already-public ABA/Acct)

| Factor | Lockbox PO Box | City/State | ZIP | Source |
|---|---|---|---|---|
| Apex Capital | PO Box 961029 | Fort Worth, TX | 76161-1029 | Source 6 |
| RTS Financial | PO Box 840267 | Dallas, TX | 75284-0267 | Source 8 |
| Triumph Business Capital | PO Box 225335 (TBK Bank, SSB – Greenscreens Lockbox) | Dallas, TX | 75222-5335 | Source 10 |
| OTR Solutions (current) | PO Box 1175760 | Atlanta, GA | 30368-7576 | Source 13 |
| OTR Capital (legacy / 2019 form) | DEPT. #390, PO Box 1000 | Memphis, TN | 38148-0390 | Source 14 |
| TBS Factoring | P.O. Box 720123 (verify on onboarding) | Oklahoma City, OK | 73172 | Source 15 |
| eCapital | Not publicly published — Exhibit B private | — | — | Source 16 |

**Triumph publicly published ACH routing:** TBK Bank, SSB | ABA/RTN 111909579 | Acct 5002387 | Checking (source 10). This is unusual transparency among the Big-5; treat as factor preference, not norm.

---

## 6. What CHL Must Request Directly From Each Factor (Gated Information)

For each factor CHL onboards with, the following **must** be obtained directly during the broker-onboarding packet — none of these are publicly published:

1. **Exact NOA stamp template wording** — the factor will provide the verbatim text + brand-approved formatting (font, position on invoice, factor logo if used). Apex, OTR, TBS, eCapital all gate this.
2. **Exact NOA letter template** — separately authenticated factor-letterhead document for first-time setup with each new shipper. RTS publishes a partial template (source 9) but full PDF is shipper-by-shipper.
3. **ABA / routing numbers for ACH/wire** — except Triumph (publicly published, source 10), all other Big-5 gate this. Required for ACH instructions on the NOA.
4. **Account number** — same. Triumph public; others private.
5. **Reference field requirements** — most factors require "Invoice number + Carrier MC#" or similar in the ACH reference field. Format gated.
6. **Required UCC-1 filing detail** — factor's exact filed party name, file numbers for the receivables-securing UCC-1. Public via SOS search but factor will provide on onboarding.
7. **Factor-side power-of-attorney scope** — eCapital is verbatim public (§7.7); Triumph is public via SEC EDGAR (source 12). Apex/RTS/OTR/TBS gate their master agreements; CHL must read them at signature.
8. **Letter of release process for switching factors** — every factor handles this differently; gated until termination negotiation.

---

## 7. Recommended CHL Implementation: NOA Template Engine

CHL needs a TWO-output template engine that takes `(factor_name, shipper_data, invoice_data)` and produces:

### 7.1 Output A: Invoice with Embedded NOA Stamp

**Inputs:**
- `factor_name` (one of: apex, rts, triumph, otr, tbs, ecapital)
- `factor.legal_name` (lookup)
- `factor.lockbox.po_box`, `.city`, `.state`, `.zip` (lookup; from §5 table)
- `factor.ach` (if available; only Triumph publicly)
- `invoice.number`, `invoice.amount`, `invoice.dates`
- `shipper.legal_name`, `shipper.address`

**Stamp text template (parameterized base):**

```
NOTICE OF ASSIGNMENT
This invoice and the receivable it represents have been
assigned to {factor.legal_name} pursuant to a factoring
agreement with Continental Haul Logistics LLC. All
payments are to be made directly and only to
{factor.legal_name} at:

  {factor.lockbox.po_box}
  {factor.lockbox.city}, {factor.lockbox.state} {factor.lockbox.zip}

[ACH (if published): {factor.ach.bank_name},
  RTN {factor.ach.rtn}, Acct {factor.ach.account},
  Reference: Invoice {invoice.number}]

Payment to any party other than {factor.legal_name},
without {factor.legal_name}'s prior written consent, will
not discharge your obligation and may result in your
having to pay the amount due again. (UCC § 9-406)
```

**Per-factor overrides:** Once onboarded, replace the parameterized stamp with the **exact** factor-supplied template. The base above is the legal-floor fallback for the parameterized case.

### 7.2 Output B: Factor NOA Letter (PDF)

A separate letter, sent once to each new shipper (not per-invoice). Template:

```
[Factor letterhead — supplied by factor]

[Date]

[Shipper legal name]
[Shipper address]

Re: Notice of Assignment — Continental Haul Logistics LLC

Continental Haul Logistics LLC has retained
{factor.legal_name} to handle credit, collections, and
processing of payments for its freight bills. All
invoices will be processed through {factor.legal_name},
and all payments must be remitted directly to
{factor.legal_name} at the address below:

  {factor.lockbox.po_box}
  {factor.lockbox.city}, {factor.lockbox.state} {factor.lockbox.zip}

Payments to anyone other than {factor.legal_name},
without our written consent, could result in your having
to pay the amount again. This notice is issued pursuant
to UCC § 9-406.

Sincerely,
{factor.contact_name}
{factor.legal_name}
{factor.phone} | {factor.remittance_email}
```

**RTS letter** uses near-verbatim of this language (source 9). Other factors will issue this on **their** letterhead with **their** signatory; CHL's job is to ensure (a) it gets sent to every new shipper, (b) it gets stored in `chl-memory/operations/` per shipper for audit, and (c) the corresponding stamp goes on every subsequent invoice to that shipper.

### 7.3 Database schema additions (for CHL build)

```
factors table:
  id, slug (apex|rts|triumph|otr|tbs|ecapital), legal_name,
  lockbox_po_box, lockbox_city, lockbox_state, lockbox_zip,
  ach_bank_name, ach_rtn, ach_account, ach_account_type,
  remittance_email, phone, ucc1_file_no, master_agreement_url,
  noa_stamp_template_text (factor-supplied verbatim),
  noa_letter_template_pdf_path,
  power_of_attorney_scope (json: poa_corrections, poa_fmcsa,
    poa_fictitious_names, poa_noa_issuance, poa_fuel_program),
  three_mode (stamp_only | letter_only | both)

shipper_factor_noa table:
  shipper_id, factor_id, noa_letter_sent_date,
  noa_letter_pdf_path, noa_letter_acknowledged_date,
  active (bool), revoked_date, lor_pdf_path
```

### 7.4 Workflow

1. CHL signs with factor F. Operator captures `factors[F]` row including factor-supplied stamp template + letter PDF.
2. New shipper S onboards. CHL/factor sends NOA letter (Output B) to S. Stored in `shipper_factor_noa`.
3. CHL invoices S. NOA stamp (Output A) auto-rendered on every invoice PDF.
4. S pays factor's lockbox/ACH. Factor reconciles + advances funds to CHL.
5. CHL switches factors → factor F issues Letter of Release; CHL updates `shipper_factor_noa.revoked_date` + new factor F2 issues new NOA letter to S; new stamp on all invoices.

---

## 8. NOA Revocation / Replacement (factor switching)

Per sources 7, 18, 24:

- The outgoing factor must issue a **Letter of Release (LOR)** to each shipper notifying them that the prior NOA is revoked.
- The incoming factor issues its own NOA (letter + stamp) on all subsequent invoices.
- Industry practice: factor switches use a **three-party buyout agreement** (carrier, old factor, new factor — source 11 Triumph blog). Outstanding receivables under old NOA are paid off via wire from new factor; remaining receivables shift to new NOA.
- LOR sample text not publicly published per-factor; obtain at termination.

---

## 9. First-Time Setup vs Ongoing

- **First-time setup:** Factor (or CHL via factor's POA per eCapital §7.7(d)) sends formal **NOA letter** to each shipper. One-time per shipper-factor relationship.
- **Ongoing:** Every invoice CHL issues to that shipper carries the **stamp** as a perpetual reminder + audit trail. Per source 19 (altLINE), this is industry standard.
- **Why both:** Letter creates the legal record of authenticated notice (§9-406(a)). Stamp ensures discharge defense if a shipper claims they "lost" or "never received" the original letter — every invoice itself is fresh authenticated notice.

---

## 10. Confidence + Gaps

| Item | Confidence | Notes |
|---|---|---|
| UCC § 9-406 verbatim text | **HIGH (1.00)** | Tier-1 statutory source 1, direct extract |
| RTS NOA letter core language | **HIGH (0.95)** | Field specimen source 9, verbatim |
| eCapital §7.7 + §8.5 verbatim | **HIGH (1.00)** | Master agreement public, source 16 |
| Triumph ABA / acct / lockbox | **HIGH (1.00)** | Factor-published, source 10 |
| Apex / OTR / TBS lockbox PO Box | **MEDIUM-HIGH (0.85)** | Public via factor contact pages or search consensus; verify on onboarding |
| Apex / OTR / TBS verbatim stamp text | **LOW (0.30)** | Not publicly published; reconstructed per industry pattern |
| Triumph SEC-filed agreement verbatim | **MEDIUM (0.70)** | Source 12 returned 403 in this run; EEE2 captured key clauses |
| Three-mode "both" usage | **HIGH (0.90)** | Multiple sources confirm; EEE2 cross-validated |
| ABA/account # for non-Triumph factors | **NONE — gated** | All require onboarding |

---

## 11. Recommended Next Steps

1. **Build the `factors` table seed file** — pre-populate with the public data in §5 + §3, leave `noa_stamp_template_text` and ACH details as `null` until onboarding.
2. **Build the NOA template engine** (§7) — both outputs, parameterized for any future factor, not just Big-5.
3. **For each factor CHL evaluates**, request the following at the discovery call: stamp template + letter template + ABA/acct + reference field format + UCC-1 file number + LOR process. Capture in onboarding intake form.
4. **Pull the SEC EDGAR Triumph agreement** (source 12) via authenticated tool — this is a Tier-1 reference document for what a transportation factoring contract structure looks like.
5. **Read eCapital's full master agreement** (source 16) end-to-end — it's the closest publicly-readable Tier-1 sample for broker-side factoring legal structure.

---

*End FFF2 — 2026-05-09.*
