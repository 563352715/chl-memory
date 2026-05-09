# Canonical broker-workflow document reference v3 (operator-supplied 2026-05-09)

**Source:** Operator's third-pass research, document-axis. Complementary to v1 (workflow-axis) + v2 (factoring-flow-axis). All three references coexist; v3 specifically catalogues the documents required to manage a freight-brokering transaction end-to-end.

**Status:** Single-source where v3 introduces NEW items; validated where v3 aligns with v1 + v2. Per `feedback_validate_findings_before_acting.md`: act on aligned items, defer single-source until tomorrow's review.

---

## 16 documents, organized by phase

### Phase 1: Customer Onboarding & Quoting

1. **Shipper-Broker Agreement** — master legal contract between broker + shipper. Defines payment terms, liability limits, cargo claim protocols.
2. **Credit Application** — form filled out BY the shipper, used to pull their business credit reports + financial history.
3. **Spot Quote / Contract Rate Sheet** — written/digital file confirming pricing for a specific lane or lane network.

### Phase 2: Carrier Onboarding & Vetting

4. **Broker-Carrier Agreement** — master legal contract between broker + trucking company. Hauling rules, payment terms, anti-back-solicitation.
5. **Form W-9 (or W-8BEN)** — taxpayer ID for annual 1099 reporting (W-8BEN for non-US carriers).
6. **Certificate of Insurance (COI)** — issued by carrier's insurance agent. Active Auto Liability (≥ $1,000,000) + Cargo Insurance (≥ $100,000).
7. **FMCSA Operating Authority (MC/DOT Certificate)** — government-issued certificate granting carrier the legal right to haul interstate freight.

### Phase 3: Booking & Dispatch

8. **Load Tender** — digital offer from shipper confirming they're awarding the specific shipment to broker at agreed rate.
9. **Rate Confirmation (Rate Con)** — binding document broker→carrier with payout, pickup/delivery addresses, appointment times, specific load instructions (reefer temps, tarping). Carrier MUST sign and return before loading.

### Phase 4: In-Transit & Delivery

10. **Bill of Lading (BOL)** — master receipt issued by shipper at loading dock. Commodity details, weight, piece count, hazmat info. Signed by shipper + driver.
11. **Lumper / Scale Receipts** — optional. Driver-paid receipts for third-party unloading service (lumper) or weight verification at certified CAT scale.
12. **Proof of Delivery (POD)** — signed BOL returned by receiver at destination dock. Most critical document for getting paid.

### Phase 5: Accounting & Factoring

13. **Notice of Assignment (NOA)** — legal notification from broker's factoring company that broker places on broker invoice. Instructs shipper to send payment to factor's bank lockbox.
14. **Carrier Invoice** — bill sent to broker by trucking company demanding payment for freight services.
15. **Broker Invoice** — bill broker generates and sends to shipper (via factoring company portal) demanding payment for the load.
16. **Schedule of Accounts / Funding Request** — formal batch submission inside factor portal bundling invoice + POD + Rate Con for immediate payout.

---

## Comparison vs v1/v2/CHL state

### Validated (3+ sources agree OR v3 sharpens an item v1/v2 had loosely)

| Doc | Validation |
|---|---|
| Broker-Carrier Agreement | v1 ⊕ v2 ⊕ v3 |
| W-9 | v1 ⊕ v2 ⊕ v3 (W-8BEN is new in v3) |
| COI | v1 ⊕ v2 ⊕ v3 + capability audit confirmed |
| FMCSA Authority Certificate | v1 ⊕ v3 (v2 implicit) |
| Load Tender | v1 ⊕ v2 ⊕ v3 |
| Rate Confirmation | v1 ⊕ v2 ⊕ v3 + DDD2 auto-sends |
| BOL (pickup) | v1 ⊕ v2 ⊕ v3 (still conflated with POD in CHL — gap) |
| POD | v1 ⊕ v2 ⊕ v3 + DDD3 red-flag escalation |
| **Broker-side NOA stamp on shipper invoice** | v2 ⊕ v3 (v1 only had carrier-side NOA) — **biggest validated gap** |
| Carrier Invoice | v1 ⊕ v2 ⊕ v3 |
| Broker Invoice | v1 ⊕ v2 ⊕ v3 + broker_invoice.py |
| Schedule of Accounts | v1 ⊕ v2 ⊕ v3 + DDD5 just shipped |

### NEW in v3 (single-source — defer until tomorrow)

| Doc | Status |
|---|---|
| Shipper-Broker Agreement | NEW — broker-side onboarding artifact CHL doesn't auto-handle |
| Credit Application (shipper-filled) | NEW — possible conflict with v2 (which says factor runs check via portal). Likely BOTH are true: broker collects credit app for sales/vetting; factor runs separate check via D&B/Cortera. Need validation. |
| Spot Quote / Contract Rate Sheet (lane-network) | NEW — distinct from one-off quote draft. We have draft generation; structured rate-sheet storage is gap |
| W-8BEN | NEW — non-US carriers (niche, but real if CHL sources Mexican/Canadian) |
| Lumper / Scale Receipts | NEW — sharpening of v2 "accessorial receipts" generic group |

---

## Document gaps in CHL today

Ranked by leverage (revenue × ease):

1. **Broker-side NOA stamp** (validated v2+v3) — DDD4 follow-on. Stamp NOA text on broker invoice generation in `broker_invoice.py`. SMALL effort. **Closes outbound pay-the-wrong-party fraud.**
2. **Rate Confirmation signed-and-return tracking** (v1+v2+v3) — gate dispatch on signed-return. SMALL effort.
3. **BOL pickup-side disambiguation from POD** (v1+v2+v3) — track pickup signature event separately. SMALL effort. Audit trail clarity.
4. **Schedule-of-Accounts packet completeness check** — verify each batch entry has: broker-invoice-with-NOA + signed rate-conf + signed POD + accessorial receipts. MEDIUM effort. DDD5 follow-on.
5. **Lumper/scale receipt line-item extraction** from carrier invoice — extend inbound_email parser. MEDIUM effort.
6. **Shipper-Broker Agreement onboarding flow** (NEW v3) — auto-send template + auto-collect signed return. MEDIUM effort.
7. **Spot Quote / Rate Sheet structured storage** (NEW v3) — extend quotes_inbound to store as named rate-sheets with lane scope. MEDIUM effort.
8. **Credit Application collection** (NEW v3, conflicts v2) — needs operator clarification on whether broker collects own app or relies on factor's check.

### NOT immediate priority

- W-8BEN — only relevant when CHL sources non-US carriers.
- FMCSA Authority Certificate PDF storage — we verify via API which is operationally equivalent to having the cert; PDF-archive could come later.

---

## Tomorrow's collaborative review agenda

Walk these in order:
1. Broker-side NOA stamp — VALIDATED, dispatch as B.2 immediately on confirm
2. Rate-conf signed-return tracking — VALIDATED, dispatch as B.2 quick-win
3. BOL/POD disambiguation — VALIDATED, small refactor
4. SoA packet-completeness — VALIDATED, DDD5 closure
5. Shipper-Broker Agreement — NEW, validate practice with industry research
6. Credit Application — NEW + v2 conflict, validate whether broker actually collects this
7. Spot Quote / Rate Sheet — NEW, validate scope (per-load vs. per-lane-network)
8. Lumper receipt extraction — VALIDATED gap from v2 already

Defer: W-8BEN, FMCSA cert PDF storage, accessorial-detail breakdown beyond lumper/scale.
