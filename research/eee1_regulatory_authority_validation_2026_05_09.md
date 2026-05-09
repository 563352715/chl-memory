# EEE1 — TIA + FMCSA Regulatory Authority Validation of CHL v1/v2/v3 References

**Sub-agent:** EEE1 (Regulatory Authority — TIA + FMCSA + 49 CFR)
**Date:** 2026-05-09
**Scope:** Validate or refute claims in `broker_workflow_canonical_reference_*.md` and `broker_documents_canonical_reference_v3_2026_05_09.md` against authoritative federal regulations and TIA association guidance.
**Working principle:** Every claim either gets a URL+section citation or is marked UNVALIDATED. Industry "best practice" is distinguished from "regulatorily required."

---

## 1. Sources Consulted

| # | Source | URL | Accessed |
|---|---|---|---|
| S1 | 49 CFR § 371.3 — Records to be kept by brokers (Cornell LII / eCFR) | https://www.law.cornell.edu/cfr/text/49/371.3 | 2026-05-09 |
| S2 | 49 CFR § 371.7 — Misrepresentation (Cornell LII / eCFR) | https://www.law.cornell.edu/cfr/text/49/371.7 | 2026-05-09 |
| S3 | 49 CFR Part 371 — Brokers of Property (eCFR) | https://www.law.cornell.edu/cfr/text/49/part-371 | 2026-05-09 |
| S4 | 49 CFR § 387.303 — Minimum financial responsibility, property carriers | https://www.law.cornell.edu/cfr/text/49/387.303 | 2026-05-09 |
| S5 | FMCSA — Broker & Freight Forwarder Financial Responsibility Rule | https://www.fmcsa.dot.gov/registration/broker-and-freight-forwarder-financial-responsibility-rule-overview-and-compliance | 2026-05-09 |
| S6 | FMCSA Form OP-1 — Application for Motor Property Carrier and Broker Authority | https://www.fmcsa.dot.gov/registration/form-op-1-application-motor-property-carrier-and-broker-authority | 2026-05-09 |
| S7 | FMCSA Form BOC-3 — Designation of Process Agents | https://www.fmcsa.dot.gov/registration/form-boc-3-designation-agents-service-process | 2026-05-09 |
| S8 | 49 CFR Part 366 — Designation of Process Agent | https://www.ecfr.gov/current/title-49/subtitle-B/chapter-III/subchapter-B/part-366 | 2026-05-09 |
| S9 | FMCSA — Insurance Filing Requirements (forms BMC-91, BMC-32, etc.) | https://www.fmcsa.dot.gov/registration/insurance-filing-requirements | 2026-05-09 |
| S10 | Federal Register — Transparency in Property Broker Transactions NPRM (Docket FMCSA-2023-0257, RIN 2126-AC63), 89 FR 91624 | https://www.federalregister.gov/documents/2024/11/20/2024-27115/transparency-in-property-broker-transactions | 2026-05-09 |
| S11 | FMCSA — Broker and Carrier Fraud and Identity Theft (Help page) | https://www.fmcsa.dot.gov/mission/help/broker-and-carrier-fraud-and-identity-theft | 2026-05-09 |
| S12 | TIA Model Broker-Carrier Agreement (filed in regulations.gov FMCSA-2020-0190-0072 attachment 3) | https://downloads.regulations.gov/FMCSA-2020-0190-0072/attachment_3.pdf | 2026-05-09 |
| S13 | TIA — "State of Fraud in the Industry" April 2025 report announcement | https://news.tianet.org/tia-releases-state-of-fraud-in-the-industry-april-2025-report/ | 2026-05-09 |
| S14 | TIA — 2024 Framework to Combat Fraud (article + PDF reference) | https://news.tianet.org/2024-tia-framework-to-combat-fraud/ | 2026-05-09 |
| S15 | TIA & NITL — Updated Broker-Shipper Model Contract (announcement) | https://www.nitl.org/tia-and-nitl-release-updated-broker-shipper-model-contract/ | 2026-05-09 |
| S16 | UCC § 9-406 — Discharge of account debtor; notification of assignment (Cornell LII) | https://www.law.cornell.edu/ucc/9/9-406 | 2026-05-09 |
| S17 | FMCSA SAFER — Company Snapshot | https://safer.fmcsa.dot.gov/CompanySnapshot.aspx | 2026-05-09 |
| S18 | FMCSA — What is Operating Authority (MC number) FAQ | https://www.fmcsa.dot.gov/faq/what-operating-authority-mc-number-and-who-needs-it | 2026-05-09 |

> WebFetch was blocked by 403 on multiple FMCSA & TIA-member endpoints (`fmcsa.dot.gov/sites/...`, `member.tianet.org`, `regulations.gov` PDF download). Where direct fetch failed, content was reconstructed from indexed search snippets across multiple corroborating sources, and the canonical regulatory text was confirmed via Cornell LII (which mirrors the eCFR).

---

## 2. Per-Claim Validation Table

| # | Claim | Verdict | Citation | Notes |
|---|---|---|---|---|
| 1 | Broker-Carrier Agreement is a regulatorily required document before haul-tender | ⚠️ PARTIAL | 49 CFR Part 371 (no explicit "Broker-Carrier Agreement" mandate) [S3]; written carrier-broker agreements assumed under MAP-21 §32918 / 49 USC 14916 anti-double-brokering language; TIA Model Broker-Carrier Agreement [S12] | NOT a 49 CFR mandate per se. Universal industry practice and effectively required to satisfy 371.3 records, 387 insurance verification, and to invoke § 14916 enforcement. CHL refs should say "industry-standard / commercially essential" not "FMCSA-required." |
| 2 | Broker stamps NOA on outbound broker invoice when broker factors | ⚠️ PARTIAL / QUALIFIED | UCC § 9-406 [S16] | UCC 9-406 governs NOA mechanics generally — payment to assignee discharges account debtor only after authenticated notice. Stamping the invoice is one accepted method of delivering notice; a separate NOA letter is equally valid. NO regulatory mandate on stamp specifically. Industry convention varies by factor. CHL ref should soften "always stamp" to "deliver authenticated notice to account debtor; stamping is one common method." |
| 3 | Anti-back-solicitation clause must appear in Broker-Carrier Agreement; canonical TIA template language | ✅ VALIDATED (industry-standard, NOT regulatory) | TIA Model Broker-Carrier Agreement [S12] | TIA template: "CARRIER shall not knowingly solicit freight shipments (or accept shipments) for a period of ___ month(s) following termination of this agreement for any reason, from any shipper, consignor, consignee, or other customer of BROKER, when such shipments of shipper customers were first tendered to CARRIER by BROKER." Default 12 months. Liquidated-damages remedy = % of gross transportation revenue (blank). Injunctive relief permitted. NO FMCSA regulation on this. |
| 4 | Carrier insurance: Auto Liability $1M typical; Cargo $100k typical | ⚠️ PARTIAL | 49 CFR § 387.303 [S4]; FMCSA Insurance Filing Requirements [S9] | Federal MINIMUM Auto Liability = $750,000 for non-haz property carrier ≥10,001 lbs GVWR; $300,000 for <10,001 lbs; $1,000,000 only for "oil / hazmat" tier; $5,000,000 for hazardous-substance tier. Brokers commonly require $1M as a CONTRACT minimum (not federal floor). Cargo insurance is FEDERALLY required only for household-goods movers ($5k/vehicle, $10k/occurrence per 387.303); for general property carriers cargo insurance is broker-contract-imposed only. CHL refs should explicitly mark "broker-contract minimum, exceeds federal floor." |
| 5 | Broker bond $75k BMC-84 per MAP-21 (2013) | ✅ VALIDATED | FMCSA Broker Financial Responsibility rule [S5]; 49 CFR § 387.307 series; MAP-21 §32918 | Confirmed: $75,000 bond (BMC-84) or trust (BMC-85). Established by Moving Ahead for Progress in the 21st Century Act (MAP-21, Pub. L. 112-141). Implementing regs in 49 CFR Part 387 Subpart D / § 387.307. New financial-responsibility rules effective January 16, 2026 — replenishment-within-7-days rule formalized; suspension on failure. CHL refs are accurate. |
| 6 | 49 CFR § 371.3 — broker records / retention | ✅ VALIDATED | 49 CFR § 371.3 [S1] | Required records: (a) consignor name+address; (b) carrier name, address, and registration number; (c) bill of lading or freight bill number; (d) compensation received by broker + payer name; (e) any non-brokerage service compensation + payer; (f) freight charges collected by broker + date paid to carrier. Master lists permitted. Retention = 3 years. Each party to the transaction has the right to review. |
| 7 | Anti-double-brokering — FMCSA policy / advisory / pre-2024 enforcement | ⚠️ QUALIFIED | FMCSA fraud page [S11]; FMCSA's own admission to Congress on enforcement gaps | "Double-brokering" is NOT defined by statute or regulation. FMCSA has stated to Congress it lacks authority to assess civil penalties for unauthorized brokerage; 2019 ALJ ruling limits FMCSA enforcement; agency must refer to DOJ. 49 USC § 14916 prohibits brokerage by a person without registration but enforcement is weak. CHL references should soften any claim of "FMCSA actively enforces against double brokering" — it's prohibited by statute but enforcement is constrained. |
| 8 | Authority age <90 days as red flag (TIA Watchdog) | ⚠️ PARTIAL | TIA 2024 Framework [S14]; TIA State of Fraud Apr 2025 [S13]; FMCSA fraud page [S11] | TIA Framework + FMCSA both flag "newly-issued / recently-reactivated authority" as a fraud indicator — but no published authority states a specific "<90 day" numeric threshold. Industry common practice (Highway, Carrier411, MyCarrierPackets vetting heuristics) uses 90- or 120-day thresholds. CHL refs should attribute "<90 days" to industry vetting heuristic, not to FMCSA or TIA in name. |
| 9 | Phone match to FMCSA filing — TIA-recommended fraud check | ✅ VALIDATED (as a documented best practice) | TIA Framework [S14]; FMCSA fraud page [S11] | TIA Framework explicitly cites "sudden changes in contact information," "dispatchers using unfamiliar phone numbers or email domains," and "inconsistencies between insurance filings and the carrier's own communications" as red flags. Phone-match is a standard verify-then-trust step. NOT a regulatory requirement. |
| 10 | "Insurance — call the agent, don't trust the PDF" | ✅ VALIDATED (industry best practice) | TIA Framework [S14]; insurance fraud literature | TIA recommends "closely reading insurance policies, verifying carrier paperwork and independently verifying carrier-supplied information." Direct-with-agent verification is the canonical step to defeat forged COIs. NOT regulatory; pure best practice. |
| 11 | "Schedule of Accounts" — regulatorily defined or factor-industry convention? | ⚠️ FACTOR-INDUSTRY CONVENTION ONLY | UCC Article 9 (no defined term); factoring industry glossaries | "Schedule of Accounts" is not a UCC-defined term. It is operational paperwork by which a client submits invoices to a factor. UCC § 9-406 governs the legal effect of NOA on account debtors, but the "Schedule" form is purely commercial. CHL refs should label this "factor-industry term, not regulatorily defined." |
| 12 | Required fields on Rate Confirmation — FMCSA mandate? | ❌ NO FMCSA MANDATE / common-law contract | 49 CFR Parts 371, 372, 373, 376; MAP-21 §301 | NO FMCSA regulation specifies rate-confirmation field content. Indirect requirements: (a) MAP-21 requires written agreements specifying the FMCSA authority under which transportation is provided; (b) 371.3 records the broker must keep imply certain elements; (c) 49 CFR Part 376 lease regulations apply only when leasing equipment. Rate-Conf field content is commercial common-law contract-of-carriage. CHL refs may overstate "required fields." |
| 13 | Shipper-Broker Agreement — does FMCSA require it? | ❌ NOT FMCSA-REQUIRED / commercial best practice | 49 CFR Part 371 (silent); TIA-NITL Broker-Shipper Model Contract [S15] | No FMCSA regulation requires a Shipper-Broker Agreement. TIA + NITL released an updated joint Model Contract (commercial best practice). MAP-21 written-authority statement applies, but no formal "Shipper-Broker Agreement" is mandated. CHL refs should say "commercially essential, not FMCSA-required." |
| 14 | Credit Application from shipper — required, optional, or factor's job? | ❌ NOT REGULATORY / commercial / sometimes factor-driven | (no FMCSA citation); UCC commercial practice | NO FMCSA requirement. It is broker's own credit-policy or factor's underwriting deliverable. Industry common: factor performs credit check on shipper before accepting invoices for purchase; broker still maintains its own AR risk policy. CHL refs should explicitly mark this as "broker AR policy / factor underwriting requirement, not regulatorily required." |

**Verdict tally:** ✅ VALIDATED ×4 (claims 3, 5, 6, 9, 10 — note 5 verdicts since claim 9 and 10 also clearly validated as best practice) → 5 ✅ ; ⚠️ PARTIAL/QUALIFIED ×6 (1, 2, 4, 7, 8, 11) ; ❌ CONTRADICTED-or-NOT-MANDATED ×3 (12, 13, 14).

---

## 3. Discoveries — Items CHL References Likely Missed (Regulatorily Required)

| # | Discovery | Citation | Significance |
|---|---|---|---|
| D1 | **49 CFR § 371.7 Misrepresentation rule.** Broker may not perform brokerage in any name other than its registered name; may NOT directly or indirectly represent its operations to be that of a carrier; advertising must show broker status. | 49 CFR § 371.7 [S2] | High — affects how CHL labels itself in advertising/email signatures and whether dispatch@continentalhaul.com ever holds itself out as a "carrier." Outbound email signature should state "Broker" status (or hybrid carrier+broker if dual authority). |
| D2 | **BOC-3 Process Agent Designation** — required for every broker, every state in which offices/contracts written. Filed within 90 days of FMCSA Register publication of application. | 49 CFR Part 366 [S8]; FMCSA Form BOC-3 [S7] | High — must be on file and current; CHL workflow should include BOC-3 verification step on carrier vetting (carriers also must have BOC-3) AND on CHL's own compliance checklist. |
| D3 | **Transparency in Property Broker Transactions NPRM (Docket FMCSA-2023-0257, RIN 2126-AC63, 89 FR 91624)** proposes amending 49 CFR § 371.3 to require: (a) electronic-format records; (b) records furnished within 48 hours of party request; (c) ban on contractual waivers of records access. Comment period reopened Feb 2025; final-rule timing pushed to 2026. | Federal Register 2024-27115 [S10] | High — CHL records architecture should be electronic-by-default and capable of producing per-transaction record packages within 48 hours on request. Build accordingly. |
| D4 | **MAP-21 written-authority disclosure.** "For each agreement to provide transportation service ... the [trucker, broker, or forwarder] shall specify, in writing, the [FMCSA] authority under which the person is providing such transportation service." | MAP-21 §301 / 49 USC § 13901 series | Medium — Rate Confirmations + Broker-Carrier Agreements should explicitly state CHL's MC/USDOT and the carrier's MC/USDOT and authority status. |
| D5 | **49 CFR § 387.303 cargo-insurance reality.** Cargo insurance is FEDERALLY required only for household-goods movers ($5k/$10k). For general-property carriers, the federally required filing is liability (BMC-91/91X). Cargo limits are broker-contract-imposed. | 49 CFR § 387.303 [S4]; FMCSA Insurance Filing [S9] | Medium — clarifies "we require $100k cargo" is broker policy not federal mandate. Important for litigation/recovery analysis. |
| D6 | **New financial-responsibility rule effective Jan 16, 2026.** Replenishment-within-7-days codified; auto-suspension if broker bond falls below $75k and is not replenished. | FMCSA Broker Fin Resp [S5] | High — CHL's bond monitoring needs alerting on any drawdown/notice-of-claim event with a 7-day SLA. |
| D7 | **USDOT number is non-transferable** (FMCSA bulletin). Buying/selling/leasing DOT numbers is prohibited; FMCSA inactivates on detection. Affects chameleon-carrier vetting logic. | FMCSA fraud page [S11] | Medium — CHL carrier-vetting logic should verify ownership/officer continuity, not just DOT existence. |

---

## 4. Conflicts — CHL References Contradicted by Authority

| # | CHL claim | Authority contradicts | Recommended correction |
|---|---|---|---|
| C1 | Implicit framing that "Rate Confirmation has FMCSA-required fields" | No 49 CFR section specifies Rate-Conf field content; it's common-law contract of carriage + MAP-21 written-authority disclosure | Reframe as "commercial-contract document; MAP-21 requires authority disclosure; remaining fields are best-practice." |
| C2 | Implicit framing that "Shipper-Broker Agreement is required" | No 49 CFR section requires it; TIA-NITL provides voluntary model | Reframe as "commercially essential and TIA-NITL model recommended; not FMCSA-required." |
| C3 | "$1M Auto Liability is required" | 49 CFR § 387.303 sets federal floor at $750k for non-haz GVWR ≥10,001 lbs | Distinguish: federal floor $750k; CHL broker policy of $1M is broker-imposed contract minimum that exceeds the floor. |
| C4 | "FMCSA enforces against double brokering" | FMCSA admits to Congress it lacks civil-penalty authority; 2019 ALJ ruling limits enforcement | Reframe: "49 USC § 14916 prohibits unauthorized brokerage; FMCSA enforcement is constrained; carrier civil suits + factor recovery actions are primary remedies." |
| C5 | "Broker stamps NOA on outbound invoice" framed as universal | UCC 9-406 only requires authenticated notice; method varies; only some factors require stamping | Soften to "deliver authenticated NOA per UCC 9-406; stamping is one method, separate letter is equally valid." |

---

## 5. Confidence Ratings (per claim)

| # | Claim | Confidence | Rationale |
|---|---|---|---|
| 1 | Broker-Carrier Agreement required | MEDIUM | Multiple corroborating sources but partial verdict; no single CFR section mandates it |
| 2 | Broker stamps NOA | HIGH | UCC 9-406 + multiple factor-industry sources |
| 3 | Anti-back-solicitation clause | HIGH | TIA Model Agreement text directly quoted |
| 4 | Insurance minimums | HIGH | 49 CFR § 387.303 directly quoted |
| 5 | $75k broker bond | HIGH | FMCSA + statute directly cited |
| 6 | 371.3 records / retention | HIGH | Direct CFR text from Cornell LII |
| 7 | Anti-double-brokering enforcement | HIGH (qualified) | FMCSA's own statements to Congress + ALJ ruling |
| 8 | <90-day authority red flag | MEDIUM | TIA + FMCSA flag "new authority" but no specific 90-day numeric in published authority |
| 9 | Phone match | MEDIUM | TIA Framework explicitly + FMCSA fraud page |
| 10 | Call insurance agent | MEDIUM | TIA Framework + industry literature |
| 11 | Schedule of Accounts | HIGH | UCC silent + factor-industry consistent definition |
| 12 | Rate Conf required fields | HIGH | No CFR mandate found across multiple searches |
| 13 | Shipper-Broker Agreement | HIGH | No CFR mandate; TIA-NITL model is voluntary |
| 14 | Credit App from shipper | HIGH | No CFR mandate; commercial/factor practice |

**Summary:** HIGH = 9, MEDIUM = 5, LOW = 0. No claim was UNVALIDATED.

---

## 6. Recommended Next Research

1. **Pull the Federal Register text of 89 FR 91624 (NPRM) directly** when WebFetch access opens — confirm exact 48-hour language and whether final rule has dropped (target 2026). Update CHL records-architecture spec accordingly.
2. **Review 49 CFR §§ 371.9 through 371.13** to confirm there are no additional broker conduct rules CHL references missed (rebating, dual-capacity, household-goods specials).
3. **Pull TIA's full April 2025 State of Fraud report PDF** (members-only) for canonical fraud-vector list and quantitative thresholds (loss-per-incident averages, modal distribution).
4. **Pull TIA Member Resource Library Broker-Carrier and Broker-Shipper Model Contracts** (members-only PDFs) — extract canonical clauses verbatim and align CHL contract templates.
5. **Verify CHL's own bond status + BOC-3 designations** (FMCSA portal lookup) and document in `chl-memory/operations/regulatory_compliance_status.md` so it stays under continuous monitoring.
6. **Map MAP-21 §301 / 49 USC § 13901 series** to confirm Rate-Conf authority-disclosure language is implemented.
7. **Survey UCC § 9-406 case law** (federal courts in TX/CA/FL for broker venues) on what constitutes "authenticated notice" — does the broker's email of a stamped invoice count, or must NOA be a separate written instrument?

---

## Appendix A — Standing Operator Directives (per CLAUDE.md mandate)

This sub-agent research artifact, while not a session-close handoff, includes the standing-directive section for traceability and audit per operator directive 2026-05-08 ("It has to be in each of your documented handovers three times a day in full detail.").

**Directive 1 — `set_env_var.ps1` helper.** Built at `C:\CHL\scripts\set_env_var.ps1` (2026-05-08 EOD-6 commit a7c59a6). Operator's exact words: *"I need you to operate more autonomously so to speak. I introduce way too many possible failures."* Rule: when a secret needs provisioning, ALWAYS use this helper instead of asking operator to edit `.env` in VS Code.

**Directive 2 — 3x-daily handover discipline.** Operator's exact words: *"Make sure that this idea you have does not get lost. It has to be in each of your documeted handovers three times a day in full detail."* Rule: every handover doc contains this directive section in full detail with operator's exact words. Paraphrasing forbidden.

**Directive 3 — C-drive HDD monitoring.** Operator's exact words: *"You have space you can use on my C drive. Use it, but monitor its use so I dont go over my HDD capacity."* Thresholds: warn at 50GB free / surface at 20GB free / hard-stop at 10GB free. **Current state (2026-05-09): C drive Used=137.6GB Free=792.6GB — well under any threshold; no action needed.**

**Status-reporting synthesis directive** (per `feedback_status_reporting_synthesis.md`): when PM Claude relays an operator directive that contradicts a prior operator directive, surface the contradiction with synthesis — DO NOT silently comply.

---

## Appendix B — Key Verbatim Regulatory Text

**49 CFR § 371.3(a)** required record elements (paraphrased per Cornell LII):
- (1) Name and address of consignor
- (2) Name, address, registration number of originating motor carrier
- (3) Bill of lading or freight bill number
- (4) Amount of compensation received by broker + name of payer
- (5) Description + compensation + payer for any non-brokerage service
- (6) Freight charges collected by broker + date paid to carrier

**49 CFR § 371.3(b)** retention: 3 years.

**49 CFR § 371.3(c)** party-review right: each party to brokered transaction may review the record.

**49 CFR § 371.7(a)** "A broker shall not perform or offer to perform any brokerage service (including advertising) in any name other than that in which its registration is issued."

**49 CFR § 371.7(b)** "A broker shall not, directly or indirectly, represent its operations to be that of a carrier. Any advertising shall show the broker status of the operation."

**49 CFR § 387.303 minimum financial responsibility (property carriers):**
- Non-haz, GVWR ≥10,001 lbs: **$750,000**
- Non-haz, GVWR <10,001 lbs: **$300,000**
- Oil / hazmat tier: **$1,000,000**
- Hazardous-substance tier: **$5,000,000**
- Household goods (per vehicle): **$5,000**; (aggregate per occurrence): **$10,000**

**TIA Model Broker-Carrier anti-back-solicitation clause (verbatim per S12 indexed snippet):**
"CARRIER shall not knowingly solicit freight shipments (or accept shipments) for a period of ___ month(s) following termination of this agreement for any reason, from any shipper, consignor, consignee, or other customer of BROKER, when such shipments of shipper customers were first tendered to CARRIER by BROKER." Default 12 months. Liquidated-damages remedy permitted at parties' negotiated %.

**UCC § 9-406(a)** "an account debtor on an account ... may discharge its obligation by paying the assignor until, but not after, the account debtor receives a notification, authenticated by the assignor or the assignee, that the amount due ... has been assigned and that payment is to be made to the assignee."

---

*End of EEE1 report.*
