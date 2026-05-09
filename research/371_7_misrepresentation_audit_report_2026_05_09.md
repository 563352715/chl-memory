# 49 CFR § 371.7 Misrepresentation Audit Report

**Date:** 2026-05-09
**Sub-agent:** GGG5
**Closes:** EEE1 finding D1 (commit `ba83990`).

## Regulatory citation

49 CFR § 371.7(a): "A broker shall not perform or offer to perform any
brokerage service (including advertising) in any name other than that
in which its registration is issued."

49 CFR § 371.7(b): "A broker shall not, directly or indirectly,
represent its operations to be that of a carrier. Any advertising
shall show the broker status of the operation."

CHL holds BROKER authority only (MC-1817555 / USDOT-4569843, BMC-84
$75k bond). Customer-facing surfaces must therefore identify CHL as
a broker.

## Tiering

- **Tier A** -- clear violations (auto-fixable or remediation-required).
- **Tier B** -- ambiguous; operator review.
- **Tier C** -- safe (carrier-facing dialog or operator-internal UI).


## Tier A violations (clear -- remediate or auto-fix)

_None found._


## Auto-fixes applied (--fix-easy mode)

- `backend\load_intake.py` -- 2 replacement(s)


_Pre-fix Tier-A findings (for audit trail):_

- `backend\load_intake.py:464` [chl_can_move] `f"{broker_name} — CHL can move this {origin} → {dest} load for "`

- `backend\load_intake.py:470` [we_move_at] `f"CHL Counter — {origin}→{dest}. We move at ${target:,.0f} ({rpm_target or '-'}/mi). "`


## Tier B items for operator review

- `backend\investor_one_pager.py:398` [we_carry_receivable]
  - Matched: `factor (3% fee). When confidence is high, we carry the receivable on`
  - Operator review: Financial term (not freight). Safe in investor context but flag to operator.

- `backend\server.py:6715` [freight_movement]
  - Matched: `"<b>Mission:</b> Deliver safe, on-time, accountable freight movement while giving carriers faster payment "`
  - Operator review: Operator review -- borderline framing; consider 'freight brokerage' instead.

- `frontend\src\App.js:15116` [tab_label_loads]
  - Matched: `<span>Loads</span>`
  - Operator review: Operator-internal UI label -- low risk; not customer-facing.

- `frontend\src\views\BulkCarrierHunter.jsx:398` [tab_label_loads]
  - Matched: `<th className="px-2 py-2 text-right">Loads</th>`
  - Operator review: Operator-internal UI label -- low risk; not customer-facing.

- `frontend\src\views\BulkCarrierHunter.jsx:455` [tab_label_loads]
  - Matched: `<th className="px-2 py-2 text-right">Loads</th>`
  - Operator review: Operator-internal UI label -- low risk; not customer-facing.

- `frontend\src\views\CarrierHub.jsx:995` [tab_label_loads]
  - Matched: `<th className="text-right px-3 py-2">Loads</th>`
  - Operator review: Operator-internal UI label -- low risk; not customer-facing.


## Tier C documentation (safe -- not flagged)

- **Carrier-facing outreach** (`backend/bulk_outreach_blast.py`, `backend/bulk_followup_sequencer.py`, `backend/consent_capture.py`, `backend/carrier_outreach.py`) -- broker writing to a carrier about putting loads on the carrier's trucks. Phrases like 'your trucks', 'your fleet', 'put loads on your trucks' are broker-correct framing.
- **Operator-internal dashboards** (`frontend/src/views/*` behind `require_owner` -- e.g., DashboardView, OperatorConsoleView) -- internal UI labels 'Loads' / 'Carriers' / 'Drivers' are operator-only, not customer-facing.
- **Broker-Carrier Agreement template** (`backend/broker_carrier_agreement.py`) -- Section 1 SCOPE explicitly identifies CHL as 'federally-authorized property broker'. Compliant.
- **Public CompliancePages** (`frontend/src/views/CompliancePages.jsx`) -- explicitly states 'CHL is a freight brokerage... we do not operate trucks or employ drivers ourselves'. Compliant.
- **3rd-party names** (England Carrier Services, RTS Carrier Services, MoDOT motor carrier services) -- not CHL self-description; safe.


## Contract template audit (operator review only -- never auto-edited)

- `backend/broker_carrier_agreement.py` -- BCA template. Section 1 SCOPE: 'Broker is a federally-authorized property broker' -- compliant.
- Shipper-Broker Agreement template -- NOT BUILT (per validated_truth_synthesis_2026_05_09.md Tier 3 backlog).
- Rate Confirmation template (referenced via `backend/load_intake.py` + dispatch flow) -- audit recommends operator confirm MAP-21 written-authority disclosure (CHL MC# + carrier MC# + 'transportation service is provided under broker authority MC-1817555').


## Operator action items (numbered for tracking)


1. Review Tier B flags (above) and decide which need rewording.
2. Decide whether LandingPage footer should switch from '(Pending)' MC# / USDOT# to live values 'MC-1817555 / USDOT-4569843' once `live_mode=True` flip happens in `backend/company_identity.py`.
3. Confirm whether to build a Shipper-Broker Agreement template (Tier 3 backlog).
4. Confirm Rate Confirmation template includes MAP-21 written-authority disclosure (broker MC# + carrier MC# + explicit 'broker' framing).
5. Sweep marketing assets outside the repo (slide decks, PDFs, social posts) for the same Tier A patterns -- audit script only covers repo content.
