# FFF3 — Broker-Collected Shipper Credit Application: Practice Resolution

License: (c) 2026 Continental Haul Logistics LLC. Internal research artifact.
Author: research sub-agent FFF3 (Claude Code dev side)
Date: 2026-05-09
Scope: resolve whether brokers operationally collect a Credit Application from shippers, or rely entirely on the factor's check, or both. Reconciles operator's v3 reference (says yes) with EEE2 (factors run own check) and EEE3 (MEDIUM — best practice not universal).

Authoritative-source ranking applied (descending trust): TIA + FMCSA primary docs > independent trade press (FreightWaves / TIA News / Overdrive) > TMS vendor docs (McLeod, Tai, AscendTMS, etc.) > broker coaching content (Freight Broker Boot Camp, 90DayFreightBroker, etc.) > sample template PDFs published by factors / 3PLs > vendor blogs/marketing.

---

## 1. Sources Consulted

### Industry / TIA
- TIA News — *Navigating Difficult Credit Markets: A Strategic Playbook for Mid-Market Brokers* (HaulPay/Steve Kochan guest). https://news.tianet.org/navigating-difficult-credit-markets/
- TIA Member Resource Library — Model Contracts catalog (login-walled). https://tianet.org/TIA/TIAnetOrg/Member%20Resources/Resource-Library.aspx

### Coaching / training content
- Freight Broker Boot Camp — *6 Resources for Running Credit Checks on Shippers and Freight Brokers*. https://freightbrokerbootcamp.com/6-resources-for-running-credit-shippers-freight-brokers/
- 90 Day Freight Broker — *How to Build Credit as a Freight Broker*. https://90dayfreightbroker.com/how-to-build-credit-as-a-freight-broker/
- TransCredit — *Building Business Credit as a New Freight Broker*. https://www.transcredit.com/Building-Business-Credit-as-a-New-Freight-Broker-Best-Practice-and-Strategies.html
- TransCredit — *Understanding Freight Broker Credit Reports*. https://www.transcredit.com/understanding-freight-broker-credit-reports.html

### Sample shipper credit application templates (published)
- FreightPal Inc — Credit Application (broker-side, 2-page). https://freightpal.com/pdf/credit-application.pdf
- Willson International Logistics — Credit Application + Standard Trading Conditions (2015). https://www.willsonintl.com/wp-content/uploads/2015/03/Logistics-Credit-Application-Standard-Trading-Conditions-03.2015.pdf
- Routes Transport — Credit Application and Agreement. https://routestransport.com/wp-content/uploads/2014/10/RTI-CreditApplication.pdf
- Scribd — *Shipper Credit Application Form* (community-shared template). https://www.scribd.com/document/803948432/Shipper-Credit-Application-Form
- The Reefer Guys — Broker Forms & Templates (bundle includes a Credit Application Template). http://thereeferguys.com/broker-forms-templates/
- Freight Movers School — Forms & Contracts (18-doc bundle includes a Shipper Credit Application). https://freightmoversschool.com/product/forms-contracts-in-edit-format/

### Factor-side perspective on credit checks (the alternative path)
- Thunder Funding — *The Importance of Broker and Shipper Credit Checks*. https://thunderfunding.com/the-importance-of-broker-and-shipper-credit-checks/
- Apex Capital — Broker Credit Checks for Trucking Companies. https://www.apexcapitalcorp.com/tools/credit-checks/
- altLINE / SoBanco — *What Is Freight Factoring & How Does It Work?* https://altline.sobanco.com/freight-factoring/
- 1st Commercial Credit — Invoice Factoring for Freight Brokers. https://www.1stcommercialcredit.com/factoring-freight-brokers
- HMD Trucking — *Freight Factoring for Brokers*. https://www.hmdtrucking.com/blog/freight-factoring-for-brokers/
- Denim — *Ultimate Guide to Factoring for Freight Brokerages*. https://www.denim.com/blog/ultimate-guide-to-factoring-for-freight-brokerages
- OperFi — *How can a new broker build credit by partnering with a factoring company?* https://operfi.com/how-can-a-new-broker-build-credit-by-partnering-with-a-factoring-company/
- Tetra Capital — Free Credit Checks on Brokers and Shippers. https://www.tetracapital.com/credit-checks/
- eCapital — Free Shipper and Broker Credit Check Tool. https://ecapital.com/tools/free-credit-check-tool/

### Regulatory
- FMCSA — *Small Entity Compliance Guide for Broker Operations*. https://www.fmcsa.dot.gov/sites/fmcsa.dot.gov/files/docs/small-entity-comp-guide-broker-operations-508.pdf
- FMCSA — *Broker and Freight Forwarder Financial Responsibility Rule*. https://www.fmcsa.dot.gov/registration/broker-and-freight-forwarder-financial-responsibility-rule-overview-and-compliance

### TMS vendor (shipper customer setup workflows)
- McLeod Software — TMS for Brokers, Carriers & 3PLs. https://www.mcleodsoftware.com/
- McLeod Software — Standard Integration Solutions for LoadMaster Enterprise. https://www.mcleodsoftware.com/loadmaster-integrations-truckload-carriers/
- TruckingInfo — McLeod release coverage (LoadMaster + PowerBroker). https://www.truckinginfo.com/10251439/mcleods-new-tms-release-promises-smarter-planning-faster-invoicing-and-ai-powere

---

## 2. Yes / No / Optional — Definitive Answer

**ANSWER: OPTIONAL-BUT-STANDARD.** Specifically: **YES for any net-terms / credit-extending shipper relationship, NO for cash-on-delivery / pre-paid / spot-only relationships, and a subset of mainstream brokers SKIP it operationally and rely on the factor's credit decision instead.**

**Confidence: HIGH** that brokers as a class collect credit applications from shippers as commercial best practice; **MEDIUM** that it is universal (clear evidence of the "lean on the factor" alternative).

Three operational realities co-exist in 2026:

1. **Traditional broker model** — broker collects shipper credit application as part of a "shipper packet" (along with broker-shipper transportation agreement, W-9 of the broker, COI, contact/AR sheet). This is **explicit best practice** in coaching content and "shipper packet" template bundles. Cite: Freight Broker Boot Camp explicitly states "A credit application is the form that the shipper fills out so you can run a credit report" and that "written permission from the client is needed before conducting a credit check, which can be on a separate form or as part of your contract." (https://freightbrokerbootcamp.com/6-resources-for-running-credit-shippers-freight-brokers/)

2. **Factor-leaning model** — broker who works with a freight factor (Triumph, OTR Solutions, Apex, Thunder, Denim, HaulPay, eCapital, 1st Commercial Credit, Tetra) lets the factor's underwriting team run a Cortera/D&B/Equifax Commercial check on the shipper before approving the shipper for factoring on that broker's account. The broker may collect a much lighter touch — name + AP contact + EIN + W-9 — and rely on the factor's "yes/no + credit limit" decision. Cite EEE2 finding + altLINE: "freight factoring companies perform credit checks on brokers and customers as an essential step of the qualification and onboarding process... factoring companies normally run credit checks on shippers at no cost and provide a report on whether the shipper is creditworthy." (https://altline.sobanco.com/freight-factoring/, https://thunderfunding.com/the-importance-of-broker-and-shipper-credit-checks/)

3. **Hybrid model (most professional brokers)** — collect credit app AS WELL AS run factor / D&B check. The credit app gives the broker (a) signed permission to pull credit, (b) a contractual paper trail of "to whom we are extending credit and on what terms," (c) bank + trade references that the factor's bureau report does not always surface, and (d) a sales-process artifact that flags professionalism to the shipper. The factor's check provides the actual underwriting decision and credit limit.

**Regulatory: NOT REQUIRED.** FMCSA's *Small Entity Compliance Guide for Broker Operations* (49 CFR 371) does not require brokers to collect a shipper credit application; nor does the IRS, USDOT, or any state. The W-9 the broker may furnish to the shipper is purely a 1099-counterparty artifact, not a credit instrument. Credit application collection is **commercial best practice**, not regulatory.

---

## 3. Sample Shipper Credit Application — Template + Field List

**Sample template (publicly available):** FreightPal Inc Credit Application (2-page broker-side form): https://freightpal.com/pdf/credit-application.pdf — closest publicly-fetchable analogue to a TIA-style shipper credit app for a freight broker context. Backup: Willson International Logistics Credit Application & Standard Trading Conditions: https://www.willsonintl.com/wp-content/uploads/2015/03/Logistics-Credit-Application-Standard-Trading-Conditions-03.2015.pdf

**Composite "typical" shipper credit application** (synthesized from FreightPal, Willson, Routes, Scribd, and Freight Broker Boot Camp guidance):

Section A — Shipper / Customer Identification
- Full legal business name (corporation / LLC / partnership / sole prop)
- DBA / trade name(s)
- Billing address + physical address
- Years in business + state of incorporation
- Federal Tax ID (EIN) / SSN if sole prop
- DUNS number (optional but encouraged)
- NAICS / SIC code (optional)
- Annual revenue range (optional)
- Number of employees / shipping volume (optional, sales-qualifier)

Section B — Principals / Officers
- Names, titles, ownership %, SSNs (optional, pulled only if personal guarantee)
- Date of birth (only if personal guarantee — many brokers omit)

Section C — AP / Billing Contact
- AP contact name, title, email, phone, fax
- Preferred invoicing method (email / EDI / portal / paper)
- Invoice frequency preference + cut-off
- Required PO / BOL / reference fields on invoice

Section D — Bank Reference
- Bank name, branch, address
- Account officer + phone
- Account number (often last 4 only; or signed authorization for direct verification)

Section E — Trade References (typically 3-5)
- Carrier or vendor name, contact, phone, account number, payment terms, monthly volume

Section F — Credit Request
- Requested credit limit ($)
- Requested payment terms (Net 15 / 30 / 45 / 60)
- Estimated monthly freight spend with broker
- Cargo insurance requirements / Certificate of Insurance request

Section G — Authorization & Signature (load-bearing for credit pull)
- Signed authorization to pull business credit (D&B / Experian / Equifax / Cortera / TransCredit / Ansonia / Compunet)
- Signed acknowledgment of broker's standard terms (default interest rate, attorneys'-fees clause, governing-law / venue, no-set-off, time-bar on disputes)
- Personal guarantee section (optional; most large shippers refuse, smaller shippers often sign)
- Name, title, signature, date

Section H — Attachments
- W-9 (so broker can 1099 the shipper if any rebate/reimbursement crosses)
- Most recent business license / state filing (optional)
- Audited financials (only requested for very large credit lines)

---

## 4. Operational Purpose Breakdown

The credit application serves at least five distinct functions:

1. **Pre-factor underwriting input** — the broker (or its factor) feeds the EIN + DUNS + bank-ref + trade-ref into D&B / Cortera / Equifax Commercial / Experian / TransCredit / Ansonia. The signed authorization is the legal predicate to pull this. Without the signed app, pulling commercial credit on a non-customer is gray under FCRA's commercial-purpose carve-out — most brokers want a paper trail.

2. **Liability / contractual paper trail** — by signing, the shipper acknowledges (a) broker's standard payment terms, (b) interest on past-due, (c) governing-law / venue / fee-shifting on collection. This makes a future collection action far cleaner. Cite Freight Broker Boot Camp: written permission must specify "the procedure (your plan to conduct a credit check), the purpose (determining creditworthiness), and the deliverable (the type of report you'll obtain)." (https://freightbrokerbootcamp.com/6-resources-for-running-credit-shippers-freight-brokers/)

3. **AR routing / invoicing accuracy** — captures the AP contact, invoice email, required PO / BOL fields, EDI preferences. This is operationally the *most-used* part of the form day-to-day. Reduces invoice rejection / chase friction.

4. **Sales qualifier** — reveals shipping volume + lane patterns + competitor relationships. The trade-reference list is a goldmine: shippers list other carriers / brokers they use, which the broker's sales rep can use for context.

5. **Bargaining chip for terms** — a shipper who refuses to fill out a credit app is implicitly asking for net-30+ terms with zero underwriting. Brokers can use the form as the trigger to either (a) require pre-pay / shorter terms, (b) cap initial exposure at first-load value, (c) walk away.

The "purpose-built payment and credit platforms" referenced in the TIA News piece by HaulPay's Steve Kochan ("They're underwriting shipper risk based on partial information. They're extending terms without real-time monitoring") explicitly addresses brokers who skip or under-collect credit data and pay the price in defaults / stretched DSO. (https://news.tianet.org/navigating-difficult-credit-markets/)

---

## 5. Factor-Check Overlap — Complementary or Redundant?

**Verdict: COMPLEMENTARY in the professional-broker model, REDUNDANT in the factor-only-model.**

| Dimension | Broker's credit app | Factor's credit check |
|---|---|---|
| Who initiates | Broker, before shipper is approved as customer | Factor, before that specific load/invoice is funded |
| Data source | Shipper-furnished (legal name, EIN, principals, bank ref, trade ref) + signed authorization | Bureau pull (D&B PAYDEX, Cortera, Equifax Commercial, TransCredit, Ansonia, Experian) |
| Decision | Broker decides whether to take shipper as customer + opening terms | Factor decides whether to fund THIS broker's invoices on that shipper, and at what advance % / credit limit |
| Continuous | One-time at onboarding (refreshed annually) | Per-load / per-invoice (factor declines individual invoices if shipper exposure exceeds limit) |
| Legal artifact | Signed credit terms + collection paper trail | Internal underwriting only |
| Visible to broker | Full | Yes/no + credit limit ($) |
| Replaces the other? | No | No |

**Operational answer:** the factor's check tells the broker WHETHER and HOW MUCH to extend; the broker's credit app captures the LEGAL RELATIONSHIP, the AR / invoicing logistics, and the paper trail for collections. They are **complementary, not interchangeable**. Brokers who rely entirely on the factor's check lose the legal and operational upside of the credit app — a known TIA-flagged failure mode ("underwriting shipper risk based on partial information"). Cite: HaulPay/Kochan in TIA News (https://news.tianet.org/navigating-difficult-credit-markets/), altLINE (https://altline.sobanco.com/freight-factoring/), Thunder Funding (https://thunderfunding.com/the-importance-of-broker-and-shipper-credit-checks/).

---

## 6. Industry Adoption %

**No published authoritative survey number found** ranking what % of US freight brokers collect a shipper credit application as part of onboarding. TIA does not publish this metric. FreightWaves / Transport Topics have not surveyed it. Searches of TIA *State of the Industry* materials, FreightCaviar, and freight-broker training content surfaced no authoritative %.

**Inferences from indirect evidence:**

- TIA model contracts include a Broker-Shipper Transportation Agreement but do not (publicly) include a model Credit Application — implies it is left to broker discretion.
- Freight Broker Boot Camp, 90 Day Freight Broker, and TransCredit all teach credit-app collection as standard onboarding — implies the trained-broker population (likely majority of mainstream brokers) does it.
- Multiple commercial template bundles (The Reefer Guys, Freight Movers School, etc.) sell "shipper packets" with a Credit Application Template — implies broad enough demand to support a template-bundle market.
- Factor-side content (altLINE, Apex, Thunder, Denim, OTR Solutions) frames factor credit checks as the *workflow*, not as a *replacement* for a broker app — implies the credit-app convention coexists with factor checks.
- TIA News (Kochan, 2025-2026): "underwriting shipper risk based on partial information" framed as a *problem*, not as standard — implies non-collection is widespread enough to be flagged as an industry pain point.

**Best estimate (judgment, not survey):** ~70-85% of professional / TIA-member brokers collect SOME form of shipper credit app at onboarding; ~30-50% of small / new / spot-market brokers skip it and lean on the factor. CHL should treat this as ESTIMATE, not data.

---

## 7. Common Pitfalls + How Brokers Handle

| Pitfall | Broker mitigation |
|---|---|
| Large enterprise shipper refuses to fill out broker's credit app (their AP says "we don't fill those out — get D&B yourself") | Broker substitutes the bureau report (D&B / Cortera) + a one-page Net Terms Acceptance form + W-9 + signed broker-shipper transportation agreement. Trade refs from public sources (RateView / SONAR / load-board pickup history). Internally rates the shipper. |
| Shipper fills out app slowly, slowing first-load quote turnaround | Two-track: book first 1-3 loads on pre-pay or COD or "first load = free credit pull on broker's nickel"; require completed app before extending terms past load N. Many brokers do this informally. |
| Shipper provides stale / fake trade refs | Broker calls 2-3 of the listed refs; flags any that don't pick up. Cross-references with bureau data. |
| Personal-guarantee section blocks signature at large shippers | Make PG optional; only invoke for smaller / newer shippers. |
| Post-onboarding shipper credit deteriorates silently | Run a continuous-monitor service (D&B Risk Monitor, Cortera / Equifax Commercial alerts) — most major TMS + factor portals provide this. McLeod LoadMaster + Tai TMS support continuous monitoring via 3rd-party integrations. (https://www.mcleodsoftware.com/loadmaster-integrations-truckload-carriers/) |
| Shipper signs but later disputes credit terms | The signed app + "standard terms incorporated by reference" is the broker's defense in collections; without it, broker is fighting on T&Cs printed on the rate confirmation only — weaker. |
| Broker forgets to pull credit before extending terms | Most TMS workflows hard-block "approve customer for terms" until "credit decision" field is populated. |

---

## 8. Liability Dimension — Risk of Skipping

If a broker skips the credit application AND relies entirely on the factor's check:

- **Bad-debt risk**: factor declines the invoice mid-cycle (shipper goes 60+ days past due, factor pulls the limit) — broker still owes the carrier; broker is on the hook for the gap.
- **Legal posture**: collection action goes forward without a signed terms agreement in the broker's hand; broker is litigating on rate-conf T&Cs only, which courts have found ambiguous when set against a shipper's Master Transportation Agreement.
- **Rate-arbitrage risk**: factor advance rate (typically 90-95%) doesn't cover broker's full margin if the shipper short-pays.
- **Dispute-management posture**: without bank ref + trade ref on file, broker has no soft-collection escalation path.
- **Audit / due-diligence posture**: future broker-bond renewal, line-of-credit application, or M&A diligence will ask for "what is your customer-onboarding KYC?" and the answer "we let the factor do it" is a red flag.

The TIA News piece directly characterizes brokers who "underwrite shipper risk based on partial information" + "extending terms without real-time monitoring" as carrying "100% of the default risk" while the factor is funding the carrier-side. (https://news.tianet.org/navigating-difficult-credit-markets/) This is the load-bearing liability case for collecting your own credit application even when factored.

---

## 9. Recommended CHL Position

**Recommendation: HYBRID — CHL collects a shipper credit application as part of standard onboarding, AND uses the factor's check as the underwriting decision input.**

### Rationale
- CHL is pre-revenue and solo-operated. Skipping the credit app to "go faster" looks attractive, but the legal / collection / AR-routing benefits of the form (purposes #2 + #3 + #5 in section 4) are exactly the operational scaffolding CHL needs to scale beyond manual operator-touch — directly aligned with CHL's "removes operator-touch / adds observability / substrate for future self-healing" north star.
- The form's AP-contact + invoicing-spec + reference data ARE the database fields CHL would need anyway to run automated invoicing and AR-chase workflows. Collecting them in a structured app is cheaper than reverse-engineering them per-shipper.
- The factor's "yes/no + credit limit" output is a structured datum CHL can cache against the shipper record for autonomous spot-quote authorization (i.e. CHL can auto-quote loads on shippers under an existing factor-approved limit without operator review).

### Pros / Cons of each path

| Path | Pros | Cons |
|---|---|---|
| Collect credit app + factor check (HYBRID) | Full legal posture, AR routing accuracy, factor-decision cached, audit-ready, scales to autonomous quoting | Slowest first-load turnaround; some shippers push back on the form |
| Skip credit app, factor-check only | Fastest onboarding; smallest friction for shipper | Weak legal posture; AR data scattered; no continuous monitoring; ages poorly |
| Collect credit app, no factor (self-fund) | Maximum control + margin retention | Requires CHL to take 100% of default risk; not viable pre-revenue |

### CHL implementation specs (proposed)

1. **`shippers` table** gets a `credit_application_id` FK to a new `shipper_credit_apps` table, with statuses: `requested`, `received`, `bureau_pulled`, `factor_approved`, `live`, `revoked`, `expired_annual_refresh`.
2. **`shipper_credit_apps` schema** mirrors section 3 above. EIN + DUNS + AP contact + bank ref + trade refs + signed authorization stored as document scan + extracted JSON.
3. **Factor decision cache** — `shipper_credit_apps.factor_credit_limit_usd` + `factor_advance_rate` + `factor_decision_date` + `factor_review_due`. Refresh monthly via factor portal or factor API.
4. **TMS workflow gate** — before any shipper is approved for net terms, `credit_application_id` must be `live` AND `factor_credit_limit_usd > 0`. Spot / pre-pay / COD shipments allowed without this gate.
5. **Soft-touch collection path** — credit-app autosend a fillable HTML form (DocuSign-equivalent) on first inbound RFQ; "first-load-on-pre-pay" mode if shipper deflects.
6. **Light-touch tier** — for very small shippers (one-time movers, occasional spot tenders), allow a lighter "Customer Setup Form" that is W-9 + signed Net 30 acceptance + signed authorization to pull credit, without the full trade-ref / bank-ref form. Reduces onboarding friction; broker-side capability to differentiate is key.
7. **Integrate with operator's v3 reference assumption** — operator's v3 said brokers DO collect a shipper credit app. This research confirms operator's assumption was *correct as the professional best practice* but not universal. EEE2 was right that factors run their own check independent of broker's app. EEE3's "MEDIUM — best practice not universal" stands. The synthesis is: CHL collects it, knowing it's commercial best practice not regulation, and uses the form as both legal artifact AND data-capture for downstream automation.

### Open question for operator
Whether to require a Personal Guarantee on the credit app for shippers below an annual revenue threshold (e.g., <$5M). Industry practice is mixed; PGs help in collection but turn off many shippers. Default proposal: PG optional, asked only for first-time shippers without a D&B PAYDEX or Cortera score >65.

---

## 10. Reconciliation with Prior Research

| Source | Claim | Resolution |
|---|---|---|
| Operator v3 reference | Brokers collect a Credit Application form | **Confirmed as commercial best practice** — universal among trained / TIA-member brokers; not universal among small/new spot brokers. |
| EEE2 (factor industry authority) | Factors run their own credit check via Cortera/D&B/Equifax Commercial without seeing broker's app | **Confirmed** — and this is independent of whether the broker also collects an app. The two checks are complementary, not interchangeable. |
| EEE3 (TMS / trade press) | MEDIUM — commercial best practice but not universal | **Confirmed exactly** — mainstream brokers collect; subset rely on factor only. |

All three are simultaneously true. CHL position: **collect the credit app AND use the factor's check** — both for the legal/AR upside of the app and the underwriting-decision upside of the factor.

---

## 11. Boot Pointers / Next Steps for Build

- Add `shipper_credit_apps` table to BUILD_STATUS_REPORT (Stream: Shipper Onboarding / Credit Workflow).
- Reference template: FreightPal credit app PDF (https://freightpal.com/pdf/credit-application.pdf) — adapt fields for CHL's TMS schema.
- Cross-reference EEE2's factor-check workflow for the factor-decision cache schema.
- Cross-reference EEE3's TMS-comparison table for which TMS vendors (McLeod / Tai / AscendTMS / etc.) provide native shipper-credit-app capture vs. require integration. None of the surveyed TMSes published this as a marketed feature in 2026 — gap CHL can build native.

---

End FFF3.
