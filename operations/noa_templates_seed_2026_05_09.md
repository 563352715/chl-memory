# NOA Templates Seed -- Broker-Side Outbound (GGG3)

**Date:** 2026-05-09
**Owner:** Continental Haul Logistics LLC
**Loaded into:** `db.noa_templates` collection via `scripts/load_noa_templates.py`
**Consumed by:** `backend/noa_outbound.py::get_template()`

---

## Purpose

Per `validated_truth_synthesis_2026_05_09.md` Section 2 + Section 3 NEW
finding #10:

  UCC § 9-406 governs ALL NOAs in BOTH directions
  (carrier->broker AND broker->shipper).

When CHL the **broker** invoices a shipper, the invoice MUST carry NOA
language redirecting payment to CHL's factor's lockbox -- OR ship a
separate factor letter -- OR both. This file is the **operator-editable
source of truth** for the per-factor stamp text + letter body that the
broker invoice rendering path overlays onto outbound invoices.

The exact factor-specific text is being researched by sub-agent FFF2 in
parallel. Until FFF2 lands, the system falls back to the **UCC § 9-406
baseline** which is legally sufficient under the statute. Operator
populates the factor-specific sections below as FFF2's outputs arrive
and re-runs `scripts/load_noa_templates.py --apply`.

---

## How to load templates into the database

### Dry run (default)
```powershell
python C:\CHL\scripts\load_noa_templates.py
```
Reports what WOULD be upserted. NO writes.

### Apply (writes to db.noa_templates)
```powershell
python C:\CHL\scripts\load_noa_templates.py --apply
```

### Apply just one factor
```powershell
python C:\CHL\scripts\load_noa_templates.py --apply --factor "Apex Capital"
```

The loader is idempotent -- re-running upserts by `factor_name`. Edits
to this MD file flow into Mongo on the next `--apply`.

---

## UCC § 9-406 BASELINE (always available; no DB load required)

Hard-coded in `backend/noa_outbound.py`. Used when no factor-specific
template is on file. Format-string substitution: `{factor_name}`.

**Stamp:**
> NOTICE OF ASSIGNMENT -- This account has been sold and assigned to
> {factor_name}. To ensure timely payment, all remittance must be made
> directly to {factor_name} at the address shown. Payment to any other
> party will not discharge this obligation. (UCC § 9-406)

**Letter:** see `UCC_9_406_BASELINE_LETTER` in `noa_outbound.py`.

---

## Per-factor templates

Each section below corresponds to ONE row in `db.noa_templates`.
Required fields: `factor_name`, `stamp_text`, `letter_text`,
`lockbox_address`, `last_updated`. Optional: `factor_phone`,
`factor_email`, `template_version`.

Sections marked **PENDING FFF2 RESEARCH** ship with placeholder text;
operator replaces the placeholders with the factor's verified
NOA-stamp wording on letter-of-assignment templates / web pages once
FFF2 surfaces the canonical phrasing.

---

### Apex Capital Corp

**Status:** PENDING FFF2 RESEARCH; POPULATE WHEN AVAILABLE
**factor_name:** `Apex Capital`
**lockbox_address:** `Apex Capital Corp, P.O. Box 961029, Fort Worth TX 76161`
**factor_phone:** `(817) 870-2422`
**factor_email:** `lockbox@apexcapitalcorp.com`
**last_updated:** `2026-05-09`
**template_version:** `factor_specific:apex:placeholder_v0`

**stamp_text (placeholder until FFF2):**
> NOTICE OF ASSIGNMENT -- This invoice has been sold and assigned to
> Apex Capital Corp. Payment must be made directly to Apex Capital's
> lockbox at the address shown. Payment to any other party will not
> discharge this obligation. (UCC § 9-406)

**letter_text:** use baseline `UCC_9_406_BASELINE_LETTER` until FFF2
provides the factor-specific letter wording.

---

### RTS Financial Service, Inc.

**Status:** PENDING FFF2 RESEARCH; POPULATE WHEN AVAILABLE
**factor_name:** `RTS Financial`
**lockbox_address:** `RTS Financial Service Inc, P.O. Box 840267, Kansas City MO 64184`
**factor_phone:** `(800) 945-1138`
**factor_email:** `lockbox@rtsinc.com`
**last_updated:** `2026-05-09`
**template_version:** `factor_specific:rts:placeholder_v0`

**stamp_text (placeholder until FFF2):**
> NOTICE OF ASSIGNMENT -- This account has been sold and assigned to
> RTS Financial Service, Inc. Payment must be sent to RTS Financial's
> lockbox to ensure timely payment. Payment to any other party will not
> discharge this obligation. (UCC § 9-406)

**letter_text:** baseline until FFF2.

---

### Triumph Business Capital

**Status:** PENDING FFF2 RESEARCH; POPULATE WHEN AVAILABLE
**factor_name:** `Triumph Business Capital`
**lockbox_address:** `Triumph Business Capital, P.O. Box 610028, Dallas TX 75261`
**factor_phone:** `(214) 513-3333`
**factor_email:** `lockbox@triumphbcap.com`
**last_updated:** `2026-05-09`
**template_version:** `factor_specific:triumph:placeholder_v0`

**stamp_text (placeholder until FFF2):**
> NOTICE OF ASSIGNMENT -- This invoice has been assigned to Triumph
> Business Capital. Remit all payments to Triumph at the address shown.
> Payment to any other party will not discharge this obligation.
> (UCC § 9-406)

**letter_text:** baseline until FFF2.

---

### OTR Capital, LLC

**Status:** PENDING FFF2 RESEARCH; POPULATE WHEN AVAILABLE
**factor_name:** `OTR Capital`
**lockbox_address:** `OTR Capital, LLC, P.O. Box 935475, Atlanta GA 31193`
**factor_phone:** `(800) 491-2235`
**factor_email:** `lockbox@otrcapital.com`
**last_updated:** `2026-05-09`
**template_version:** `factor_specific:otr:placeholder_v0`

**stamp_text (placeholder until FFF2):**
> NOTICE OF ASSIGNMENT -- This account has been sold and assigned to
> OTR Capital, LLC. All payments must be made directly to OTR Capital
> via ACH or check at the lockbox shown. Payment to any other party
> will not discharge this obligation. (UCC § 9-406)

**letter_text:** baseline until FFF2.

---

### TBS Capital Funding

**Status:** PENDING FFF2 RESEARCH; POPULATE WHEN AVAILABLE
**factor_name:** `TBS Capital Funding`
**lockbox_address:** `TBS Capital Funding, P.O. Box 205154, Dallas TX 75320`
**factor_phone:** `(800) 207-7661`
**factor_email:** `lockbox@tbsfactoring.com`
**last_updated:** `2026-05-09`
**template_version:** `factor_specific:tbs:placeholder_v0`

**stamp_text (placeholder until FFF2):**
> NOTICE OF ASSIGNMENT -- This invoice has been assigned to TBS Capital
> Funding. Effective immediately, all payments must be made to TBS
> Capital Funding's lockbox. Payment to any other party will not
> discharge this obligation. (UCC § 9-406)

**letter_text:** baseline until FFF2.

---

## Operator notes

- Lockbox addresses above are the **public** PO Boxes carriers receive
  on inbound NOAs (cross-checked with backend/noa_detector.py constants).
  ABA / account numbers are deliberately NOT included in this MD file
  (those are factor-internal and shouldn't sit in a chl-memory commit
  per the secrets-local-only directive).
- When FFF2 surfaces the canonical stamp phrasing for a factor, REPLACE
  the placeholder `stamp_text` block + bump `template_version` from
  `placeholder_v0` to `verified_v1` (or higher) + update `last_updated`
  + re-run the loader script with `--apply`.
- The audit log `db.noa_outbound_log` records `template_version` per
  stamped invoice -- so a future audit can confirm exactly which
  template was active when each invoice was stamped.
