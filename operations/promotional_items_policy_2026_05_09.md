<!--
Continental Haul Logistics LLC -- Promotional-Items Policy
Stream HHH2 (HHH1 follow-up #4, 2026-05-09)
Owner: Jason Aaron Meyer (operator)
Status: STARTING POLICY -- legal counsel review recommended before
        first hire (sales rep, broker agent, etc.) takes effect.
License: Internal CHL working document. Quotes 49 CFR sec 371.9 and
        related federal statutes verbatim from public-domain US Code
        of Federal Regulations.
-->

# Continental Haul Logistics LLC -- Promotional-Items Policy

**Effective date:** 2026-05-09
**Regulatory authority:** 49 CFR sec 371.9(b) (anti-rebating + gift
prohibition; FMCSA-administered).
**Internal enforcement:** `backend/integrity/promotional_items_log.py`
(per-item cap + per-recipient annual cap + cash-equivalent ban + ledger
write to `db.promotional_items_log`).

---

## 1. Regulatory citation (verbatim)

49 CFR sec 371.9(b):

> "A broker shall not give or offer to give anything of value to any
> shipper, consignor or consignee (or their officers or employees) except
> inexpensive advertising items given for promotional purposes."

The regulation is silent on the definition of "inexpensive." This policy
sets concrete dollar caps + an item-type allow-list per industry custom
+ IRS / Bank Secrecy Act guidance on cash equivalents.

---

## 2. Caps

| Cap | Amount | Scope |
|---|---|---|
| **Per-item cap** | **$25 USD** | Any single promotional item given to any single recipient |
| **Per-recipient annual cap** | **$50 USD** | Aggregate USD value of all items given to one recipient in one calendar year |

The per-recipient cap aggregates across roles -- if "Recipient X" is the
ops manager AND the company owner, the $50 floor is the same number. CHL
does not split caps by hat.

---

## 3. Allowed item types

The following item types are permitted under sec 371.9(b) "inexpensive
advertising items given for promotional purposes":

| Item type | Description | Examples |
|---|---|---|
| `branded_merchandise` | Items bearing the CHL logo / brand | Mugs, pens, notebooks, hats, t-shirts, tote bags, branded keychains |
| `promo_sample` | Promotional product samples (CHL-branded or vendor-supplied) | Branded calendars, promotional swag |
| `modest_meal_at_event` | Modest meals at trade events (under per-item cap) | Lunch at booth, snacks at trade-show conference room |

---

## 4. Strict prohibitions

The following item types are **categorically prohibited** -- not subject
to a "but it's only $X" carve-out. Cash + cash-equivalent prohibitions
are codified per 31 CFR 1010.330 (Bank Secrecy Act cash-equivalent
definition) + IRS Pub 535 anti-rebate stance:

- **Cash** (US dollars or any currency)
- **Gift cards** (Visa / MasterCard / Amazon / restaurant / store-branded -- any amount)
- **Prepaid debit cards** (any issuer)
- **Gift certificates redeemable for cash equivalents** (incl. fuel cards beyond carrier-fleet operational scope)
- **Sports tickets / concert tickets** (regardless of face value)
- **Lavish entertainment** (golf outings, expensive dinners over the per-item cap, hotel stays)
- **Anything conditioned on traffic award, rate adjustment, or rebate**

Backend enforcement: `backend/integrity/promotional_items_log.py` rejects
`item_type IN {cash, gift_card, prepaid_debit, gift_certificate}` at
insert time with `ValueError`. Future expansion to ticket / entertainment
types should be added to `DISALLOWED_ITEM_TYPES` per amendment to this
policy.

---

## 5. Documentation requirement

**Any item over $0.00 to any recipient must be logged** in
`db.promotional_items_log` with:

- `date` -- ISO-8601 timestamp the item was given
- `recipient_name` -- individual or company that received the item
- `recipient_company` -- shipper / consignor / consignee company name (optional but recommended)
- `item_description` -- free-text description of the item
- `item_type` -- one of the allow-list values in sec 3 above
- `cost_usd` -- per-item USD cost (CHL's cost basis, not retail value)
- `reason` -- free-text business justification ("Trade show booth giveaway", "Customer relationship maintenance", etc.)
- `given_by` -- operator email or 'auto' (CHL is currently solo-operator; pre-populate with operator email when hires occur)

The backend `log_item()` helper validates against caps + types BEFORE
inserting. Operators MUST use `log_item()` rather than direct Mongo
writes -- direct writes bypass enforcement and create audit-trail gaps.

---

## 6. Disclosure requirement

Items provided to shippers, consignors, consignees, carriers, or their
officers / employees must NOT be conditioned on:

- Traffic award (e.g., "give us this load and we'll send you a $20 mug")
- Rate adjustment (e.g., "accept our $1.85/mile rate and have a coffee mug on us")
- Rebate / discount inducement (any pay-for-play structure)
- Volume commitments
- Routing preferences

Items must be of the kind that would be given in the **ordinary course
of relationship maintenance** -- a holiday calendar to a long-standing
shipper is fine; a $24.99 mug delivered the day before a rate
negotiation is not.

---

## 7. Annual review

Each calendar year (target date: January 31 of year N+1, after Q4
operations close):

1. Operator (or designated Compliance role when CHL hires for it) runs
   `full_year_ledger(db, year=N)` to pull the complete annual log.
2. Reviews each row for:
   - Recipient cap compliance (running annual sum <= $50 per recipient)
   - Item-type compliance (no entries with disallowed types -- defense
     in depth even though insert path rejects them)
   - Reason justification quality (every row has a non-empty `reason`)
3. Spot-checks 5-10% of rows for:
   - Whether the item was actually given (matching receipt / shipping
     confirmation in CHL's accounting system)
   - Whether the recipient was in fact a shipper / consignor / consignee
     party at the time
4. Files the review in `chl-memory/operations/annual_review_<year>.md`
   with sign-off line.

---

## 8. Future expansion

When CHL hires its first sales representative or broker agent (per the
EEE1 backlog -- Tier 3 hire), this policy must be updated to:

- Onboarding checkbox: each new hire signs off on this policy.
- Per-employee promo budget cap (separate from per-recipient cap).
- Quarterly mini-review by employee in addition to annual operator
  review.
- Expense-report routing: any promo item > $25 (over per-item cap)
  requires operator pre-approval + amendment to this policy if the
  pattern recurs.

---

## 9. Cross-references

- **49 CFR sec 371.9** -- full sec text in
  `chl-memory/research/hhh1_49_cfr_part_371_compliance_map_2026_05_09.md`
- **Backend module** -- `backend/integrity/promotional_items_log.py`
- **Backend tests** -- `backend/tests/test_promotional_items_log.py`
- **HHH1 compliance map** --
  `chl-memory/research/hhh1_49_cfr_part_371_compliance_map_2026_05_09.md`
  (Gap #4 in section 5)

---

*End of Promotional-Items Policy 2026-05-09.*
