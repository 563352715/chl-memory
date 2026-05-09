# Canonical broker-workflow reference (operator-supplied 2026-05-09)

**Source:** Operator-shared during the post-CCC2 build review on 2026-05-09. This is the load-bearing reference for what CHL must execute end-to-end — supersedes prior research-agent's looser walkthrough where they conflict.

**Why this matters:** This is a more granular, more authoritative checklist than what the web-research agent assembled. Each line item is an automation target.

---

## Phase 1: Load Sourcing & Setup

1. **Secure the Load** — receive a Load Tender from the shipper (origin, destination, weight, commodity).
2. **Credit Check** — run a credit check on the shipper (you OR your factoring company) BEFORE moving forward. If the shipper is not factor-approved, you (the broker) take the risk of not getting paid.

## Phase 2: Posting & Carrier Vetting

Fraud prevention is the #1 activity on public load boards.

1. **Post the Load** to DAT / Truckstop / 123Loadboard.
2. **Constraint:** Never list shipper's exact name or address — prevents back-solicitation.
3. **Carrier Inquiry** — carrier calls/emails interest. Request their MC#.
4. **Vetting Checklist (CRITICAL):**
   - **FMCSA validation** — SAFER Web confirms Active Authority + Safety Rating.
   - **Insurance verification** — DO NOT TRUST the PDF they send. **Call the insurance agent listed on FMCSA** to confirm policy active. Auto Liability ≥ $1M, Cargo ≥ $100K.
   - **Red Flag Checks** — double-brokering indicators:
     - Is authority less than 90 days old?
     - Does the supplied phone number match FMCSA filing?
     - Recent inspections? (No inspections = often a shell company.)
5. **Onboarding** — send the Carrier Packet:
   - Signed Broker-Carrier Agreement
   - W-9
   - Carrier's Authority Certificate
   - Copy of Insurance

## Phase 3: Booking & Dispatch

1. **Rate Confirmation** — broker sends Rate Con. Carrier signs + returns.
2. **Dispatch** — broker sends specific pickup address + pickup numbers to driver.
3. **Tracking** — verify driver arrived, loaded, and departed.
4. **Document Created:** BOL signed by driver and shipper at the dock.

## Phase 4: Delivery & Documentation

1. **Delivery** — driver arrives at receiver and unloads.
2. **POD Collection** — receiver signs the BOL → POD.
3. **Carrier Invoice** — carrier sends their invoice + signed POD/BOL.
4. **Note:** if carrier ALSO uses a factoring company, the invoice will include a **Notice of Assignment (NOA)** instructing broker to pay the carrier's factor's bank, NOT the carrier directly. Paying the carrier when an NOA exists is a high-loss broker error.

## Phase 5: The Factoring Cycle (Financial Close)

1. **Create Invoice** — broker generates invoice for the shipper (e.g., $2,000).
2. **Submit Schedule** — upload Schedule of Accounts (batch of invoices) to factor's portal.
3. **Submit Documents** — upload "clean" packet to factor:
   - Broker's invoice (to shipper)
   - Signed POD (no POD = no funding)
   - Rate Confirmation signed by carrier
4. **Verification** — factor may email/call shipper to verify load delivered without damage.
5. **Funding (Advance)** — factor deposits 80–95% of invoice value, often within 24 hours. Example: $2,000 invoice → ~$1,800 immediate.
6. **Carrier Payment** — broker uses funds to pay carrier's invoice.
7. **Collection** — factor collects full $2,000 from shipper in 30–60 days.
8. **Rebate (Settlement)** — once shipper pays, factor sends remaining balance minus fee. Example: $200 reserve − 3% fee = ~$140 rebate.

---

## Critical Documents Reference

| Document | Purpose | Provided by |
|---|---|---|
| Broker-Carrier Agreement | Legal contract defining terms | Broker |
| Certificate of Insurance (COI) | Proof of carrier coverage | Carrier's insurance agent |
| Rate Confirmation | Agrees on price + lane | Broker |
| Bill of Lading (BOL) | Receipt of freight at pickup | Shipper |
| Proof of Delivery (POD) | Proof job is done (signed BOL) | Receiver / Carrier |
| Notice of Assignment (NOA) | Redirects payment to carrier's factor | Carrier's factoring company |

---

## Use this document as

The **gold-standard test** for whether CHL's platform autonomously executes the full broker workflow. For every line item above, the question is: "Does our software do this without operator touching it?" If no, it's a software-design gap (not a vendor-key gap).

The 10 gaps surfaced by this checklist (vs my prior audit) are documented inline in `chl-memory/research/chl_capability_map_2026_05_09.md` revision delta + the post-May-14 fix priority list in dev Claude's session response 2026-05-09.
