# Validated-Truth Synthesis — broker workflow + factoring + documents

**Date:** 2026-05-09
**Sources synthesized:**
- v1 canonical reference (operator-supplied AM): `broker_workflow_canonical_reference_operator_2026_05_09.md`
- v2 canonical reference (operator-supplied PM): `broker_workflow_canonical_reference_v2_2026_05_09.md`
- v3 document reference (operator-supplied evening): `broker_documents_canonical_reference_v3_2026_05_09.md`
- EEE1 (commit `ba83990`) — TIA + FMCSA + 49 CFR Part 371 regulatory authority
- EEE2 (commit `5c84b97`) — IFA + SFNet + Big-5 transportation factors + UCC § 9-406
- EEE3 (commit `e30a222`) — DAT + Truckstop + Highway + RMIS + TMS vendors + FreightWaves/TT trade press

**Purpose:** Produce a defensible knowledge registry. Every claim cited HIGH/MEDIUM/LOW/CONTRADICTED based on cross-source agreement.

---

## Confidence taxonomy

- **HIGH** = 2+ authoritative sources agree (regulatory + industry, OR industry + factor, etc.)
- **MEDIUM** = 1 authoritative source confirms
- **LOW** = CHL operator-inferred only; no external authority confirms
- **CONTRADICTED** = authoritative source contradicts CHL's earlier claim
- **QUALIFIED** = partially correct; needs scope-narrowing

---

## Section 1 — Workflow Phases (v1/v2 axis)

### Phase 1: Pre-Booking & Factor Credit Approval

| Claim | Verdict | Authority |
|---|---|---|
| Broker receives Load Tender from shipper | **HIGH** | EEE3 (TMS standard); v1+v2+v3 |
| Broker submits shipper to factor for credit check BEFORE posting | **HIGH** | EEE2 (Big-5 portal lookup; factor decisions before invoice purchase); v2+v3 |
| If factor denies shipper, broker cancels/finds another | **HIGH** | EEE2 (factor will not buy denied invoices); v2+v3 |
| Broker collects own Credit Application from shipper | **MEDIUM** | EEE1+EEE3 mention as commercial-best-practice; not regulatorily required. v3-only conflict with v2 RESOLVED: both can coexist (broker's app for sales/vetting; factor's lookup is separate). |

### Phase 2: Carrier Sourcing & Vetting

| Claim | Verdict | Authority |
|---|---|---|
| Post load on DAT/Truckstop public boards | **HIGH** | EEE3 (DAT/Truckstop primary load-board canon); v1+v2+v3 |
| Don't reveal shipper name on board posts (anti-back-solicit) | **HIGH** | EEE1 (TIA Model Broker-Carrier Agreement explicit anti-back-solicit clause, default 12 months); v3 |
| FMCSA SAFER active authority + safety rating verification | **HIGH** | EEE1 (49 CFR mandates broker due diligence) + EEE3 (DAT/Truckstop SOP); v1+v2+v3 |
| Auto Liability ≥ $1M | **CONTRADICTED → QUALIFIED** | EEE1: Federal floor is **$750k** per 49 CFR § 387.303 (non-haz ≥10,001 lbs); $1M is broker-imposed contract minimum, NOT regulatory. CHL must be clear which it's enforcing. |
| Cargo Insurance ≥ $100k | **MEDIUM** | EEE1: Federal floor varies by commodity; $100k is industry common practice |
| **Insurance: call the agent, don't trust the PDF** | **CONTRADICTED → QUALIFIED** | EEE3: Partially obsolete in 2026. Industry standard is now API-direct-from-insurer (Certificial / RMIS). Manual call = fallback only. CHL's "call agent" build target should be replaced with API-direct integration. |
| **Authority age <90 days = red flag** | **QUALIFIED** | Concept HIGH (EEE3: "new authority = risk" universal); specific 90-day threshold is NOT industry standard — CHL policy. The 90-day number happens to map to BOC-3 filing window (different concept). Cite as CHL POLICY, not industry rule. |
| Phone-FMCSA cross-check fraud filter | **HIGH** | EEE3 (DAT explicit + Highway productized via VoIP); v3 |
| Recent inspections check (no inspections = shell company) | **HIGH** | EEE3 (DAT/Truckstop standard; Highway monitors); v3 |
| Carrier packet: Broker-Carrier Agreement + W-9 + COI + Authority cert | **HIGH** | EEE1 (TIA Model + 49 CFR § 371.3 retention); v1+v2+v3 |
| Carrier411/FreightGuard as ground-truth gate | **CONTRADICTED** | EEE3: Use as signal, never as gate. $458k defamation verdict Oct 2023; OOIDA "blackballing on hearsay" critique. Legal exposure. |

### Phase 3: Booking & Dispatch

| Claim | Verdict | Authority |
|---|---|---|
| Rate Confirmation broker→carrier with required fields | **MEDIUM-QUALIFIED** | EEE1: NO 49 CFR section mandates field content; commercial common law + MAP-21 written authority disclosure only. Industry consensus on fields exists (EEE3: TMS-canonical) but isn't federally required. |
| Carrier signs and returns Rate Con before loading | **HIGH** | EEE3 (TMS standard; e-sign mainstream — DocuSign / Aljex ARC / AscendTMS); v1+v2+v3 |
| Dispatch packet sent to driver post-booking | **HIGH** | EEE3 (TMS standard); v1+v2+v3 + DDD2 just shipped |
| Track shipment via check-calls or digital tracking | **HIGH** | EEE3 (universal); v1+v2 |
| Driver signs BOL at pickup | **HIGH** | EEE1 (commercial law + UCC); v1+v2+v3 |
| BOL (pickup) and POD (delivery) are distinct documents | **HIGH** | All 6 sources |

### Phase 4: Delivery & Documentation

| Claim | Verdict | Authority |
|---|---|---|
| POD = signed BOL by receiver | **HIGH** | All 6 sources |
| Vision-OCR POD validation | **HIGH** | EEE3 (mainstream TMS feature 2024+); already shipped via DDD3 |
| Auto-flag damage/refusal/short on OCR | **HIGH** | EEE3 + DDD3 |
| Carrier sends invoice + POD + accessorial receipts | **HIGH** | EEE2+EEE3; v1+v2+v3 |
| Lumper / scale receipts as discrete document types | **MEDIUM** | EEE3 (TMS handle as accessorial line items); v3 names them explicitly |

### Phase 5: Factoring Cycle

| Claim | Verdict | Authority |
|---|---|---|
| Carrier-side NOA (carrier→broker) requires payment redirect to carrier's factor | **HIGH** | EEE2 (UCC § 9-406; Apex/Triumph/OTR/RTS docs); v1+v2+v3 + DDD4 just shipped |
| **Broker-side NOA** (broker→shipper) on broker invoice | **HIGH** | EEE2 (UCC § 9-406; Triumph explicit; eCapital Master Agreement Sec 7.7(d) power-of-attorney); v2+v3. **NOT BUILT — gap remains.** |
| NOA stamp REPLACES factor letter | **CONTRADICTED** | EEE2: Three options coexist (stamp, letter, or both). CHL must support all three. |
| Schedule of Accounts batch submission | **MEDIUM-QUALIFIED** | EEE2 confidence 0.65 — transport vendors often use "funding schedule" instead of "Schedule of Accounts." Both terms valid; format is factor-portal-specific. DDD5 shipped. |
| Submit clean packet (broker invoice + signed Rate Con + signed POD + accessorial receipts) | **HIGH** | EEE2 (Big-5 packet requirements converge); v2+v3 |
| Factor verifies invoice (calls/emails shipper) | **HIGH** | EEE2: VERIFICATION OF EVERY INVOICE IS UNIVERSAL — not sampling. Phone + email + portal lookup. |
| Factor advances 80-95% (recourse) / 92-96% (non-recourse) | **HIGH** | EEE2 across Big-5 |
| Reserve 5-20% | **CONTRADICTED → QUALIFIED** | EEE2: Industry actual is 5-10% recourse / **0% on true non-recourse**. CHL's prior 5-20% range was loose. |
| Factor fee 1.5-5% | **HIGH** | EEE2 (Big-5 1.5-5%, central band 2-4%); v2+v3 |
| Factor releases reserve minus fee on collection | **HIGH** | EEE2; v2+v3 |
| 90-day default chargeback period | **HIGH** | EEE2 (Big-5 standard) |
| First-time invoice premium fee | **CONTRADICTED** | EEE2: NO evidence. Origination fees exist; per-invoice fee is constant. CHL should not assume. |

---

## Section 2 — Documents (v3 axis)

| Document | v3 says | Validated by EEE | CHL state |
|---|---|---|---|
| Shipper-Broker Agreement | NEW v3 | **MEDIUM** (EEE1 + EEE3 commercial-best-practice; not regulatory) | NOT BUILT |
| Credit Application | NEW v3 | **MEDIUM** (EEE3 sales tool; factor's own check is separate per EEE2) | NOT BUILT |
| Spot Quote / Rate Sheet (lane-network) | NEW v3 | **MEDIUM** (EEE3: TMSes maintain rate sheets) | Partial — quote drafting only |
| Broker-Carrier Agreement | All 3 ops | **HIGH** (EEE1 TIA Model + 49 CFR retention) | Partial — vault stores, auto-send/auto-collect-signed is gap |
| W-9 / W-8BEN | All 3 ops | **HIGH** (EEE1 IRS) | W-9 stored; W-8BEN unhandled |
| COI | All 3 ops | **HIGH** (EEE1 + EEE3) | Tracked + expiry monitored |
| FMCSA Authority Cert | v1 + v3 | **HIGH** (EEE1) | Verified via API; PDF storage gap |
| Load Tender | All 3 ops | **HIGH** | RFQ ingestion exists; tender = award flow implicit |
| Rate Confirmation | All 3 ops | **HIGH** | DDD2 auto-sends; signed-return tracking gap |
| BOL (pickup) | All 3 ops | **HIGH** | Conflated with POD; pickup signature event gap |
| Lumper / Scale receipts | v3 only explicit | **MEDIUM** (EEE3 TMS line-item) | Detention tracked; lumper line-item gap |
| POD | All 3 ops | **HIGH** | DDD3 + OCR + red-flag escalation |
| **Broker-side NOA on outbound invoice** | v2 + v3 | **HIGH** (EEE2 UCC § 9-406; Big-5 explicit) | NOT BUILT — biggest validated gap |
| Carrier Invoice | All 3 ops | **HIGH** | Inbound parser; accessorial line-item extraction gap |
| Broker Invoice | All 3 ops | **HIGH** | broker_invoice.py + assemble_billing_packet |
| Schedule of Accounts | All 3 ops | **MEDIUM-QUALIFIED** (EEE2: term varies; format factor-specific) | DDD5 shipped |

---

## Section 3 — NEW findings the EEE batch surfaced (CHL didn't have)

### Regulatory (EEE1)

1. **49 CFR § 371.7 misrepresentation rule** — broker must NOT hold itself out as a carrier. Affects email signatures + branding. CHL audit needed.
2. **NPRM 89 FR 91624 — Transparency in Property Broker Transactions** — proposes electronic records + 48h party-request response + ban on contractual waivers. Final rule expected 2026. **CHL records architecture should target this NOW, not retrofit later.**
3. **BOC-3 Process Agent designation** — required for every broker, every state where contracts written (49 CFR Part 366). Should be in CHL self-compliance audit.
4. **Federal Auto Liability floor is $750k** (49 CFR § 387.303), not $1M. CHL should distinguish federal floor from broker-imposed minimum.
5. **FMCSA cannot civil-enforce double-brokering** — must refer to DOJ. Software-side fraud defense is even more critical than assumed.
6. **Jan 16, 2026 BMC-84 replenishment SLA** — 7-day rule effective. CHL renewal calendar (KK1) must track.
7. **FMCSA Oct-2025 MC→USDOT consolidation** — CHL should canonicalize USDOT as primary key; MC as legacy alias.

### Factoring (EEE2)

8. **Debtor411** — transportation-factor private credit DB. Factors weekly-report AR-aging on payor identities. **CHL's pay-to-carrier behavior gets reported.** Slow-pay = blacklisted across all factors. Operational reputation surface to protect.
9. **eCapital Broker Factoring Master Agreement** publicly readable at `ecapital.com/transportation/broker-terms/` — Section 7.7(d) NOA power-of-attorney. Tier-1 publicly-accessible legal sample.
10. **UCC § 9-406 governs ALL NOAs** — both directions (carrier→broker, broker→shipper). Same legal mechanism, different artifacts. Debtor entitled to demand "seasonable proof."

### Operational (EEE3)

11. **Outbound-comms integrity** — criminals pivoting from carrier-impersonation toward intercepting/redirecting loads tendered to legitimate carriers via spoofed dispatcher inboxes (CargoNet Q3 2025). Need SPF/DKIM/DMARC + recipient-domain-binding + dispatcher-identity verification on rate-con replies.
12. **ELD-vs-claimed-fleet-size discrepancy check** — if carrier claims 30 trucks but only 3 ELDs report, fraud signal. Highway pattern; becoming table-stakes.
13. **Continuous monitoring (not one-time vetting)** — RMIS / Highway / DAT all real-time-monitor carrier risk with daily change-alerts. CHL's per-load re-vetting should extend to monitoring (insurance lapse, authority change, ownership change, ELD count change) between loads.
14. **2024-2026 fraud-loss scale** — TIA April 2025: 22% of brokers report >$200k losses in 6 months; CargoNet 2025: ~$725M aggregate (+60% YoY); avg cargo theft $273,990 (+36%); avg fictitious-pickup $365k. Justifies heavy fraud-defense investment.
15. **Carrier411 / FreightGuard legal exposure** — defamation/tortious-interference verdict $458k Oct 2023. CHL must use these as SIGNAL, never as GATE.

---

## Section 4 — Validated build priorities (revised post-EEE)

### Tier 1 — Validated by 2+ EEE sources, ship now

1. **Broker-side NOA on outbound invoice** (broker_invoice.py edit + factor letter generation + 3-mode support stamp/letter/both) — fraud-critical, structurally symmetric to DDD4
2. **Rate-confirmation signed-return tracking + dispatch gate** — TMS-mainstream practice
3. **API-direct insurance verification** (Certificial / RMIS integration) — replaces manual call-the-agent
4. **Continuous carrier monitoring** — recurring re-vet vs onboarding-only
5. **Schedule-of-Accounts packet completeness check** (DDD5 closure: verify all 4 docs in batch entry)
6. **NPRM 89 FR 91624 records architecture** — electronic records + 48h party-request response

### Tier 2 — Validated, larger effort

7. **BOL (pickup) vs POD (delivery) state-machine disambiguation**
8. **Carrier accessorial line-item extraction** (lumper, detention, scale)
9. **Outbound-comms integrity** (SPF/DKIM/DMARC + dispatcher-identity binding on rate-con replies)
10. **ELD-vs-fleet-size discrepancy check** (Highway-style fraud signal)
11. **49 CFR § 371.7 misrepresentation audit** — email signatures, marketing copy, frontend labeling
12. **BOC-3 process-agent compliance** in CHL renewals (KK1)
13. **FMCSA USDOT-canonical migration** (MC as legacy alias)

### Tier 3 — Validated but lower priority

14. **Shipper-Broker Agreement onboarding flow**
15. **Spot Quote / Rate Sheet structured storage**
16. **W-8BEN handling** (only for non-US carriers)
17. **Debtor411 reputation monitoring** (read-only ingest)

### Items to RECLASSIFY based on EEE findings

- **Authority-age <90 days check (DDD1)** — keep, but cite as CHL POLICY not industry rule. Don't represent to operators or anyone external as "industry standard."
- **Insurance-call-the-agent (was on backlog)** — DEMOTE in favor of API-direct integration.
- **Carrier411/FreightGuard integration (was on backlog)** — REMOVE as gate; allowed as signal-only.
- **First-time invoice premium fee (was speculation)** — REMOVE; doesn't exist.
- **Reserve 5-20% range** — TIGHTEN to 5-10% recourse / 0% non-recourse.

---

## Section 5 — Confidence summary

| Confidence | Count |
|---|---|
| HIGH (2+ sources agree) | 38 claims |
| MEDIUM (1 source) | 11 claims |
| LOW (CHL inferred) | 0 claims (after EEE validation) |
| QUALIFIED (partial / scope-narrowing) | 6 claims |
| CONTRADICTED (must revise) | 5 claims |

**Most important contradictions to absorb:**
1. $1M Auto Liability is broker-policy not federal (federal = $750k)
2. Reserve range tighter than v1/v2 said (5-10% recourse / 0% non-recourse)
3. NOA stamp doesn't replace factor letter (three options coexist)
4. Authority <90 days is CHL policy, not industry standard
5. Carrier411 as gate is legal exposure (signal-only)

---

## Section 6 — Tomorrow's collaborative review agenda (revised)

Operator + dev walk through this synthesis. For each Tier 1 item: confirm dispatch-now or defer. For each contradiction: confirm CHL's revised position. For each NEW finding: validate operational priority.

**Expected outcome:** B.2 batch dispatch list (probably ~10 items) ready for execution after May 14 platform-readiness milestone clears.
