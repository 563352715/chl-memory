# HHH1 Addendum — Direct ecfr.gov API access + § 371.10 correction

**Date:** 2026-05-09 (post-HHH2)
**Trigger:** Operator asked whether ecfr.gov was actually accessible. HHH1 had used Cornell LII mirror because the public eCFR HTML page returned a 302 redirect to `unblock.federalregister.gov`. This addendum documents the working API path + a correction to HHH1.

---

## Working access path

The ecfr.gov **public HTML** path (`/current/title-49/...`) returns 302 → unblock.federalregister.gov (anti-scrape captcha). However the ecfr.gov **API** endpoint works without redirect when given a valid date:

```
https://www.ecfr.gov/api/versioner/v1/full/<YYYY-MM-DD>/title-49.xml?part=371
https://www.ecfr.gov/api/versioner/v1/full/<YYYY-MM-DD>/title-49.xml?part=371&section=371.10
```

A future-dated query (e.g., 2026-05-09) returns 404. Use a published date such as `2024-01-01` or `2025-12-01`. The eCFR is updated daily so any past date within publication history works.

This API surface is the canonical authoritative source — use it for any future regulatory deep-reads.

---

## Correction to HHH1 — § 371.10 is ACTIVE, not reserved

HHH1 listed sections 371.4 / .5 / .6 / .8 / **.10** / .11 / .12 as "reserved/not assigned in current text." Direct ecfr.gov API confirms **§ 371.10 IS in current text and binding on CHL**.

**Verbatim § 371.10 — Duties and obligations of brokers:**

> *"Where the broker acts on behalf of a person bound by law or the FMCSA regulation as to the transmittal of bills or payments, the broker must also abide by the law or regulations which apply to that person."*

**Citation:** 45 FR 68943, Oct. 17, 1980, as amended at 62 FR 15421, Apr. 1, 1997.

### Compliance analysis for CHL

§ 371.10 applies when CHL acts as **agent for billing/payment transmittal**. Does CHL?

**YES — at least three triggering scenarios identifiable in CHL's flows:**

1. **Carrier-side NOA routing (DDD4 commit `288383d`)** — when a carrier sends NOA to CHL, we route payment to the carrier's factor's lockbox. CHL is acting on behalf of the carrier (the assignor) for the limited purpose of payment transmittal. UCC § 9-406 already governs this; § 371.10 adds an FMCSA layer requiring CHL to abide by any law/regulation applicable to the carrier in that payment chain.

2. **Broker-side NOA stamping (GGG3 commit `15c34ac`)** — CHL stamps NOA on the shipper invoice directing payment to CHL's factor. CHL is the assignor here, factor is assignee, shipper is debtor. Less clear if § 371.10 applies (since CHL is the principal not the agent), but worth flagging.

3. **Factor schedule submission (DDD5 commit `b3e276e`)** — CHL submits invoice + supporting docs to factor portal as part of the funding cycle. CHL is acting on its own behalf (selling its own receivable); not § 371.10 territory unless interpreted broadly.

The cleanest case is #1 (carrier-side NOA). The DDD4 NOA-detection module already routes per UCC § 9-406; § 371.10 doesn't add operational steps but DOES add a regulatory-citation layer that should be referenced in our internal compliance docs.

### Verdict

✅ **COMPLIANT** in operational behavior — DDD4 + GGG3 + DDD5 already implement the lawful payment-transmittal mechanics. No code changes needed.

🟡 **PARTIAL** in documentation — HHH2's `chl-memory/operations/shipper_broker_agreement_template_2026_05_09.md` and `promotional_items_policy_2026_05_09.md` should add a § 371.10 reference where bill/payment transmittal is described. Small operator-side documentation update.

**Action:** Add to the shipper-broker agreement template a clause acknowledging CHL's § 371.10 obligation when handling bill/payment transmittal. Recommend ~3 sentences. Defer to operator's legal counsel review along with the rest of the template.

---

## Verbatim § 371.3 — confirmed via direct API (matches HHH1 reconstruction)

**Subsection (a) — required record elements (verbatim):**

> *"Brokers shall keep a record of each transaction. For purposes of this section, brokers may keep master lists of consignors and the address and registration number of carriers, rather than repeating this information for each transaction. The record shall show:*
> 
> *(1) The name and address of the consignor;*
> *(2) The name, address, and registration number of the originating motor carrier;*
> *(3) The bill of lading or freight bill number;*
> *(4) The amount of compensation received by the broker for the brokerage service performed and the name of the payer;*
> *(5) A description of any non-brokerage service performed in connection with each shipment or other activity, the amount of compensation received for the service, and the name of the payer;*
> *(6) The amount of any freight charges collected by the broker and the date of payment to the carrier."*

**Subsection (b) — retention:**

> *"Brokers shall keep the records required by this section for a period of three years."*

**Subsection (c) — party review right:**

> *"Each party to a brokered transaction has the right to review the record of the transaction required to be kept by these rules."*

HHH1's Cornell-LII-mirrored text matches the API verbatim text. No correction needed for § 371.3.

---

## Updated Part 371 Subpart A scoreboard

| Section | HHH1 verdict | Post-HHH2 | Post-this-addendum |
|---|---|---|---|
| § 371.1 Applicability | ✅ COMPLIANT | ✅ | ✅ |
| § 371.2 Definitions | ✅ COMPLIANT | ✅ | ✅ |
| § 371.3 Records | 🟡 PARTIAL (3 sub-gaps) | ✅ COMPLIANT | ✅ |
| § 371.7 Misrepresentation | ✅ COMPLIANT | ✅ | ✅ |
| § 371.9 Rebating | 🟡 PARTIAL (2 sub-gaps) | ✅ COMPLIANT | ✅ |
| **§ 371.10 Duties/obligations** | (mis-listed reserved) | (mis-listed reserved) | ✅ COMPLIANT operationally / 🟡 documentation |
| § 371.13 Accounting | ✅ COMPLIANT | ✅ | ✅ |

Subpart A: **6 of 7 fully COMPLIANT + 1 (§ 371.10) operationally compliant with documentation gap.** HHH1's "16/16 sections" total was based on counting subsections; the corrected count of distinct active section numbers in Subpart A is **7** (not 6 as HHH1 implied by listing 371.10 as reserved).

---

## Operator-action item added

In addition to HHH1's "off-repo § 371.7 marketing-asset sweep," operator should:

**8. Add § 371.10 acknowledgment to shipper-broker agreement template** — when legal counsel reviews the existing template language (HHH2 commit `3010b36`), include a clause stating CHL acknowledges its § 371.10 obligation when transmitting bills/payments on a shipper's or carrier's behalf. Approx 2-3 sentences. Coordinate with the existing § 371.9(a) COI representation block.

---

## Recommendation

This addendum supersedes HHH1's "section reserved" entries for §§ 371.4, .5, .6, .8, .10, .11, .12 in scope of § 371.10 only. The other sections (§§ 371.4 / .5 / .6 / .8 / .11 / .12) remain reserved per the canonical eCFR API text — confirmed via direct fetch on 2026-05-09.

Future regulatory deep-reads should default to the API path (`/api/versioner/v1/full/<date>/title-49.xml`) rather than scraping the HTML page. The Cornell LII mirror remains a reliable secondary source.
