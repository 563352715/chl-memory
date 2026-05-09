# Canonical broker-workflow reference v2 (operator-supplied 2026-05-09 PM)

**Source:** Operator's second-pass research on broker-with-factoring workflow. Refines the v1 reference (`broker_workflow_canonical_reference_operator_2026_05_09.md`) with more granular phase 1 (pre-booking factor credit check) + phase 4 (NOA stamp on broker invoice) + phase 5 (reserve release mechanics).

**Status:** Single-source — needs additional agent validation before acting on the new gaps it surfaces. Per `feedback_validate_findings_before_acting.md`: only act when operator AND agent reports agree.

---

## Phase 1: Pre-Booking & Factor Credit Approval

1. Secure Load Details — receive load specs, commodity, rate from shipper
2. **Submit Factoring Request** — broker logs into factor portal, inputs shipper's MC/DOT/legal business name for credit check
3. **Factor Performs Credit Check** — analyzes payment history, days-to-pay, financial stability
4. **Factor Issues Approval/Denial**
5. **RULE:** if factor denies, broker cancels load or finds another shipper. Factor will not buy this invoice.

## Phase 2: Carrier Sourcing & Vetting

1. Post Load on DAT/Truckstop (only AFTER factor approves shipper)
2. Vet the Carrier (FMCSA authority + insurance + fraud + double-brokering)
3. Execute Carrier Packet (Broker-Carrier Agreement + W-9 + COI)
4. Issue Rate Confirmation (broker sends; carrier signs + returns)

## Phase 3: In-Transit & Delivery

1. Dispatch & Pick Up — driver arrives at shipper; shipper loads truck + issues BOL; driver signs BOL
2. Track Shipment — check calls or digital tracking
3. Delivery & Unloading — driver arrives at receiver; receiver unloads + signs BOL → BOL becomes POD

## Phase 4: Invoice Collection & Factoring Submission

1. Receive Carrier Documents — carrier invoice + signed POD + accessorial receipts (lumper, detention)
2. Generate Shipper Invoice — Carrier Pay + Broker Margin = total
3. **Apply Notice of Assignment (NOA)** — broker STAMPS or adds text on shipper invoice: payment must be sent directly to broker's factoring company's bank account / lockbox, NOT to broker.
4. Create Funding Request — open new "Schedule of Accounts" batch in factor portal for this load
5. Upload Document Packet ("clean packet"):
   - Broker invoice to shipper (NOA-stamped)
   - Signed Rate Confirmation (showing what broker owes carrier)
   - Signed POD (proving delivery)
   - Any accessorial receipts

## Phase 5: Funding & Financial Closeout

1. Factor Verification — factor reviews packet; may call/email shipper to verify freight arrived safely with no damages or shortages
2. Factor Deposits Advance — upon approval (typically within 24h), factor buys invoice + deposits Advance Rate (85-95% of total) to broker bank account
3. Pay the Carrier — broker uses advance funds to pay carrier per agreed terms
4. Shipper Pays Factor — shipper pays factor 100% of invoice within 30-60 days
5. Factor Releases Reserve — once factor collects, releases remaining 5-15% (Reserve) to broker MINUS factoring fee (1.5-5% of total invoice). Transaction closes.

---

## DELTA vs v1 reference + DDD-batch implementation

### NEW gaps surfaced (NOT YET BUILT — pending validation)

1. **Broker → Shipper NOA stamp on outbound invoice** (Phase 4.3). This is DIFFERENT from DDD4's carrier → broker NOA detection. DDD4 handles INBOUND (carrier sends NOA to us); this requires OUTBOUND (we stamp NOA on shipper-invoice we generate). Without it: shipper might pay us directly, our factor still claims the invoice → we owe them.
2. **Pre-booking factor credit check workflow** (Phase 1.2-1.5). Sequencing: factor approval gates load posting. Was on prior gap list; now explicit rule.
3. **Rate Confirmation signed-and-returned tracking** (Phase 2.4). DDD2 auto-sends dispatch packet, doesn't gate pickup on signed rate-conf return.
4. **BOL signed at pickup** (Phase 3.1) vs **POD signed at delivery** (Phase 3.3) — currently conflated; pickup BOL signature isn't tracked separately.
5. **Carrier invoice + accessorial receipts line-item ingestion** (Phase 4.1). Partial — `inbound_email` parses but lumper/detention not pulled as structured line items.
6. **Document packet completeness check** (Phase 4.5). DDD5 batches but doesn't verify each entry has all 4 required docs (broker-invoice-with-NOA + rate-conf + POD + accessorial receipts).
7. **Factor verification call event hook** (Phase 5.1). No system awareness when factor calls shipper. Damage/shortage discovery may delay funding without us knowing.
8. **Reserve release tracking + factoring fee accounting** (Phase 5.5). Net broker margin = advance + reserve - fee. Not currently computed end-to-end.

### Deltas from prior v1 reference

- v1 said "credit check on shipper" generically. v2 makes explicit: it's done THROUGH the factor portal, and factor has go/no-go authority.
- v1 only mentioned NOA in Phase 4 (carrier-side). v2 introduces broker-side NOA as a separate step in Phase 4.3.
- v1 was vague on Phase 5. v2 specifies advance % (85-95%), reserve % (5-15%), factoring fee % (1.5-5%), and reserve-release timing.

### Aligned with DDD-batch shipped this session

- ✅ Phase 2.2 vet carrier — DDD1 (authority-age + phone-FMCSA cross-check) + carrier_vetting
- ✅ Phase 3.3 POD red-flag escalation — DDD3
- ✅ Phase 4.4 Schedule of Accounts — DDD5 (but missing packet-completeness check per Phase 4.5)
- ✅ Phase 5.2 Factor advance trigger — DDD6 reads `funded_at`
- ✅ Phase 5.3 Pay carrier (NOA-aware) — DDD4 + DDD6

---

## Validation status

This document is the v2 canonical reference. It supersedes v1 where they conflict. But the NEW gaps listed above (1-8) are SINGLE-SOURCE pending agent validation per the validate-tomorrow rule. Tomorrow's collaborative review walks through each:

- For each new gap: validate (proceed in B.2/B.3) / defer (out of scope) / reframe (operator clarifies)
- Items already on the prior gap list (pre-booking credit check, reserve release) are now strengthened by v2 explicit detail — those move closer to "validated."
- The broker-side NOA stamp is the most-likely-validated item because it's structurally symmetric to DDD4's carrier-side NOA detection — same fraud-loss surface, opposite direction.
