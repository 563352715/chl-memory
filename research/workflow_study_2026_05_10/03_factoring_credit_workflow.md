# Factoring & Credit Workflow Study — Freight Broker Lifecycle

**Date:** 2026-05-10
**Scope:** How factoring companies fit into the freight-brokerage lifecycle. Verifying the operator's instinct that "submit to factoring" is step 2. Mapping who-checks-whom in pre-load credit checks. Distinguishing broker-factor / carrier-factor / shipper-credit-service. TriumphPay workflow. Margin-determination timing.

---

## 1. TL;DR — Correcting the Operator's Instinct

**The operator's instinct is partially correct but mislocated in the lifecycle.** Three distinct things get conflated under the phrase "submit to factoring":

| Phase | What happens | Operator's question maps to... |
|---|---|---|
| **A. Pre-load credit qualification** (before booking) | Broker checks the **shipper's** credit — often via the broker's factor or via a credit bureau like Ansonia | (a) — the broker checking the shipper's credit |
| **B. Pre-load carrier vetting** (before dispatching) | Carrier checks the **broker's** credit via the carrier's own factor (Apex, RTS, OTR) | (b) — but it's the carrier doing this, not the broker |
| **C. Post-delivery invoice factoring** (after POD) | Broker submits the completed invoice + POD + rate-con to its factor to get advanced cash | The textbook "factoring" step |

**"Submit to factoring" as step 2 is wrong** if it means submitting the invoice — that's a step-7-ish post-delivery action. But **a pre-booking credit check against the broker's factor IS effectively step 2** of a competent broker's workflow, and that's almost certainly what the operator means. The correct framing is:

> Step 2 is **"Get shipper credit cleared by your factor (or credit bureau)"** — NOT "submit the load to factoring." The actual invoice submission is post-delivery.

The Denim factoring guide states this directly: *"Your customers request a shipment, and oftentimes your factor will perform a credit check on the customer to ensure their load qualifies for factoring"* — this happens **before** the broker commits to haul, which is why it functionally lives early in the lifecycle.

---

## 2. The Three Factoring Relationships (Rigorously Distinguished)

### 2a. Broker's Factor (finances the broker's receivables)
- **Customer:** The freight broker.
- **What it buys:** The broker's invoice to the **shipper** (e.g., Walmart owes Continental Haul Logistics $1,000 for a Houston→San Antonio load — the factor advances ~95% of that to CHL same-day).
- **Credit risk evaluated:** The **shipper's** ability to pay (not the broker's).
- **Pre-load function:** Most broker-factors pre-clear shippers — they tell the broker "yes, we'll buy your invoice to Acme Corp up to $X" or "no, Acme is non-creditworthy, don't haul for them."
- **Examples:** Denim, OTR Solutions (brokerage factoring product), eCapital, altLINE, 1st Commercial Credit, Triumph Business Capital.
- **Key OTR product mechanic:** OTR funds the broker's **margin** within 24 hours of broker submitting invoice+POD+rate-con, then OTR pays the carrier on defined terms and collects from the shipper on the back end (OTR Solutions docs).

### 2b. Carrier's Factor (finances the carrier's receivables)
- **Customer:** The trucking company / owner-operator.
- **What it buys:** The carrier's invoice to the **broker** (e.g., Joe's Trucking hauls a CHL load for $850 — Apex Capital advances ~95% of that to Joe).
- **Credit risk evaluated:** The **broker's** ability to pay (not the carrier's).
- **Pre-load function:** Carrier-factors offer real-time broker credit checks. Apex's published workflow: *"the exact dollar amount we will purchase for every broker through a real time broker credit check so you can book loads with confidence."*
- **Examples:** Apex Capital, RTS Financial, OTR Solutions (carrier factoring product), Love's Financial, Compass Funding, Tetra, TAFS, Riviera Finance.
- **Why this matters to CHL:** Every carrier dispatching for CHL is almost certainly running CHL through their factor's broker-credit tool before accepting the load. CHL's Ansonia/Compass days-to-pay score directly determines whether carriers will haul.

### 2c. Shipper-Credit Service / Broker-Credit Bureau (information only, no money)
- **What they sell:** Information, not financing. Scores, days-to-pay, fraud alerts.
- **Primary players:**
  - **Ansonia** — the dominant broker-credit bureau (0-100 scale; 87+ is lowest-risk; primary source per Denim).
  - **Compass Funding's credit-check side / Compass Business Solutions**
  - **TransCredit** — proprietary broker scores.
  - **Carrier411** — broker safety + credit signals.
  - **Highway (formerly Truckstop Credit)** — broker credit scoring.
  - **DAT iQ Credit Score** (DAT's Ansonia-powered offering).
- **Used by:** Both sides — brokers vet shippers, carriers vet brokers, factors aggregate all of it.

---

## 3. The Actual Pre-Load Credit-Check Sequence (Who Checks Whom, In What Order)

```
T-0  Shipper posts load to load board / sends RFP
       |
T+1  BROKER → checks SHIPPER credit
       Tools: broker's factor (free shipper credit check),
              or direct Ansonia/Compass/TransCredit lookup
       Question: "Will my factor buy this invoice? At what limit?"
       OUTCOME: shipper credit-cleared → load is factorable.
       |
T+2  BROKER determines margin / sets buy-rate target
       (cost-plus calc, lane data, fuel, accessorials — independent of factor)
       |
T+3  BROKER posts on load board / sources carrier
       |
T+4  CARRIER → checks BROKER credit
       Tools: Apex AMP, RTS Pro, OTR portal, Highway, Carrier411
       Question: "Will my factor buy this broker's invoice? At what limit?
                  What is broker's days-to-pay?"
       OUTCOME: broker credit-cleared → carrier accepts the load.
       |
T+5  Rate confirmation signed by both sides
       |
T+6  Carrier hauls; BOL → POD chain
       |
T+7  Delivery; POD obtained
       |
T+8  CARRIER → submits invoice + POD to carrier's factor (Apex, RTS, etc.)
       Carrier-factor advances 90-95% to carrier within 24h.
       Carrier-factor sends Notice of Assignment to broker (pay us, not carrier).
       |
T+9  BROKER → submits invoice + POD + rate-con to broker's factor
       Broker-factor advances 90-95% of MARGIN (or full invoice, depending on product).
       Broker-factor invoices shipper.
       |
T+10 Shipper pays broker's factor (30-90 days standard terms).
T+11 Broker's factor remits remaining 5-10% to broker minus fees.
T+12 Broker's factor (or broker directly) pays carrier's factor on the receivable.
```

**Two independent credit checks happen at two different stages** — one at T+1 (broker→shipper), one at T+4 (carrier→broker). These are bilateral, not transitive. The shipper is never checked by the carrier; the carrier is never checked by the broker for credit purposes (vetting carrier is about safety/insurance/MC age, not days-to-pay).

---

## 4. TriumphPay Workflow — The Dominant Broker→Carrier Payment Network

**TriumphPay is NOT a factor itself** — it is a **payment network** built by Triumph Financial (parent of Triumph Business Capital, the factor). TriumphPay sits between brokers, carriers, and factors as the rails for invoice presentment, audit, and settlement. Triumph Financial acquired Advance Business Capital (rebranded Triumph Business Capital) 2012-2016 and then incubated TriumphPay as a separate-but-integrated payments arm.

**Three TriumphPay portals matter:**
1. **Broker portal** — receives carrier invoices, runs automated audit (POD matching, rate-con matching, fraud screen), schedules settlement.
2. **Carrier portal** — submits invoice+POD, sees payment status, can elect QuickPay or StandardPay.
3. **Factor portal** — for carrier-factors holding the carrier's Notice of Assignment; receives the settlement on the carrier's behalf.

**Workflow per TriumphPay's published TMS-integration docs (Tai Software knowledge base):**

1. **Payee Profile Sync** — every 15 min, broker's TMS pushes carrier records (MC#, DOT#, contact, remit address) to TriumphPay.
2. **Shipment Sync** — when a shipment status moves to READY or DELIVERED in broker's TMS, the shipment data syncs to TriumphPay automatically. Log line: *"Load information sent over to TriumphPay."*
3. **Carrier submits paperwork** — carrier uploads invoice + signed POD + rate-con through TriumphPay's carrier portal (or via fax/email; TriumphPay's POD detection / Workflow AI auto-classifies).
4. **Automated Audit** — TriumphPay matches POD to BOL/rate-con, runs fraud signals, flags discrepancies.
5. **Invoice Draft Sync** — daily 6 PM PST, TriumphPay pulls audited invoices back into broker's TMS as bill drafts.
6. **Broker approves** — releases payment.
7. **Settlement** — StandardPay (broker's contractual terms, e.g., net-30) or QuickPay (carrier gets faster pay for a small discount; LoadPay integration gives carrier funds in minutes 24/7/365).
8. **Factor of Record check** — if carrier has a factor on file at TriumphPay, payment routes to factor's bank account, not carrier's. TriumphPay holds payments where broker-carrier records show a factor conflict.

**Critical for CHL:** TriumphPay claims $100B+ payment volume and $114M prevented losses. Thousands of brokers route through it. If CHL processes carrier payments outside TriumphPay's network, CHL will face higher verification friction with TriumphPay-onboarded carriers (which is most large fleets).

---

## 5. When Does the Broker "Engage" Their Factor?

**Three distinct engagement moments — not one:**

1. **Once, at company startup:** Broker applies for a factoring facility, submits articles of incorporation, MC authority, surety bond, AR aging report, customer list. Factor underwrites the broker and approves a facility (advance rate, recourse vs. non-recourse, customer concentration limits).

2. **Per-shipper, ongoing:** Factor pre-approves each new shipper customer. Broker can request unlimited shipper credit checks (free at most factors). Factor returns a credit limit per shipper. *This is where the operator's "step 2 = submit to factoring" instinct actually sits — not the invoice but the shipper-credit-clearance step.*

3. **Per-load, post-delivery:** Broker submits invoice + POD + signed rate-con. Factor advances cash within 24-48 hours. **This is the only step where factoring "moves money on this load."** Documents required at this stage (per altLINE, Denim, OTR docs):
   - Signed rate confirmation
   - Carrier invoice (broker uses it to construct shipper invoice)
   - Proof of delivery (signed BOL or POD)
   - Broker's own invoice to the shipper
   - Optionally: lumper receipts, accessorial backup

---

## 6. Margin-Determination Timing — Independent of Factoring

**Industry sources are unanimous: margin is determined BEFORE booking, independent of the factor.** Freight 360 and Cargo Chief both frame it as cost-plus or markup against the **buy rate** (cost from carrier), not against any factor advance rate. Tai Software's broker rate calculator and the Cargo Chief margin-vs-markup analysis show the formula sits entirely in the broker's pricing layer:

```
margin = (sell_rate − buy_rate) / sell_rate
```

The factor's role in margin is purely a **financing cost**, not a pricing input — the 1.5%-5% factor fee comes out of margin, but the decision to book happens before any factor interaction on a given load.

**Where factoring DOES touch margin decisioning:**
- If the factor refuses to buy invoices from shipper X (bad credit), broker may decline the lane entirely or price in non-recourse risk premium.
- If the factor caps purchases at $Y per shipper per month, broker plans volume accordingly.
- The factor's advance rate (e.g., 95% vs 90%) affects working capital but not unit-load margin.

**For CHL specifically:** the pnl_monitor `compute_load_pnl` floor is **upstream of factoring** — the load must clear margin BEFORE the factor question even comes up. The "never book at a loss" rule operates at the pricing layer (rate_calc_engine + margin_config), and the factor question is a separate "is this load also factorable?" gate that runs in parallel.

---

## 7. Recourse vs. Non-Recourse — Why It Matters Here

- **Recourse factoring:** Broker bears default risk. Cheaper fees (~1.5-3%). If shipper doesn't pay in 60-90 days, factor charges back to broker.
- **Non-recourse factoring:** Factor bears default risk in specific cases (typically only shipper bankruptcy, NOT slow-pay). Higher fees (~3-5%). Requires high shipper credit ratings.

Most "non-recourse" products are narrowly scoped — they cover bankruptcy only, not normal slow-pay or disputes. The marketing implies broader coverage than the contract actually delivers (altLINE notes this explicitly).

---

## 8. Corrected Step-2 for the CHL Workflow

**Operator's instinct ("step 2 = submit to factoring") restated correctly:**

> Step 2 of the broker workflow is **"Pre-clear the shipper through your factor's credit check"** — NOT "submit the invoice to factoring" (that's post-delivery). The pre-clearance answers: *Will my factor buy the receivable from this shipper, and at what limit?* If yes, the load is factorable and broker continues to step 3 (margin determination + carrier sourcing). If no, broker either declines, demands prepayment, or self-finances.

For CHL's input-surface SOP alignment work, this means **two distinct factor touchpoints** must be modeled in the workflow engine:
- **Pre-load gate:** shipper-credit-check API call to broker's factor → returns approval + limit. (Likely integrate with Denim API, OTR portal, or direct Ansonia/DAT iQ feed.)
- **Post-delivery action:** invoice submission package (rate-con + POD + invoice) to broker's factor → returns advance.

Plus a **third, mostly-passive concern:** carriers running CHL through their own factor's broker-credit tool. CHL's days-to-pay score on Ansonia/Compass/TransCredit/Highway is what determines whether carriers accept loads from CHL. This is a reputational asset CHL must actively manage, not a workflow step.

---

## Sources

- Denim — *Ultimate Guide to Factoring for Freight Brokerages*
- altLINE (Southern Bank) — *Get Your Invoices Paid Faster with Freight Broker Factoring*
- OTR Solutions — *Freight Factoring for Brokers* + *Broker and Shipper Approvals*
- Apex Capital — *Broker Credit Checks for Trucking Companies*
- RTS Financial / RTSinc — *What Is Freight Factoring?* + *The Importance of Broker Credit Checks for Carriers*
- TriumphPay — Broker FAQ, *Factor of Record Holds*, Tai Software TriumphPay knowledge base
- Triumph Financial / Triumph Business Capital — corporate history
- Freight 360 — *How Important are Freight Broker Margins?*
- Cargo Chief — *Pricing with Margins, not Markups*
- Tai Software — *How Does a Freight Broker Calculate Rates?*
- Denim — *Understanding Freight Broker Credit*
- DAT iQ — *Credit Score Ansonia FAQs*
- TIA — *Understanding Credit Reporting for Freight Brokers*

---

**Word count:** ~1,650 words.
