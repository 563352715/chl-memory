# Phase 1 — Input-Surface Inventory

**Generated:** 2026-05-10 ~14:45 CDT (19:45 UTC)
**Initiative:** Operator-mandated 2026-05-10. See `~/.claude/projects/c--CHL/memory/project_input_surface_sop_alignment.md`.
**Status:** Phase 1 complete. Phase 2 (entity-to-input mapping) awaits operator review.

This directory enumerates every place the platform accepts a selection or input. It's the foundation for Phase 2 (mapping inputs to synthetic entities), Phase 3 (SOP gap analysis), and Phase 4 (regulatory alignment).

---

## Files in this directory

| File | What's in it | Counts |
|---|---|---|
| [endpoints.md](endpoints.md) | Every HTTP endpoint exposed by the backend | **839** endpoints / 9 webhook receivers / 21 token-public |
| [pydantic_models.md](pydantic_models.md) | Every Pydantic model + enum/Literal fields with allowed values | **249** models / 15 Python Enums / 17 Literal/Enum fields |
| [frontend_forms.md](frontend_forms.md) | Every form, dropdown, and choice array on the frontend | **12** major forms / 14 choice arrays / 140+ fields |
| [configurables.md](configurables.md) | Every operator-tunable setting (business_settings, env vars, module constants) | **42** business_settings keys / 65+ env vars / 150+ module constants |

---

## Headline numbers

| Surface | Count |
|---|---|
| HTTP endpoints | **839** |
| Pydantic models | **249** |
| Frontend forms | **12** |
| Choice arrays (frontend dropdowns) | **14** |
| Pydantic Literal/Enum fields (selection inputs) | **17** |
| Python Enum classes | **15** |
| `db.business_settings` keys | **42** |
| Environment variables | **65+** |
| Module constants | **150+** |
| **TOTAL distinct selectable / configurable inputs** | **~1,500+** |

---

## Cross-cutting findings (issues to address in Phase 2/3/4)

### Critical — affects platform correctness

1. **Equipment-enum drift across frontend forms.** `CarrierPreferences` allows 9 equipment types (tanker / livestock / intermodal / heavy_haul). `QROnboardLoad` and `LoadBoardAggregator` only allow 4-8. Drivers selecting tanker on one form may not match backend enums.

2. **`VettingGatesBlock.carrier_min_safety_rating` type bug.** Uses string `"None"` instead of null. Likely causes false negatives on vetting checks.

3. **`payment_terms` missing industry standards.** Schema only includes `net_30`. Net-60 / Net-90 are common; absence of these forces fallback paths that may mis-classify.

4. **Hazmat case inconsistency.** Hazmat uses uppercase `"I"`, `"II"`, `"III"`. Most other enums lowercase. String comparisons across modules will silently mismatch.

5. **Rate-per-mile unbounded input.** `AddDriverModal` and `CarrierPreferences` accept `$0` to `$1M+` with no min/max validation. Operator could fat-finger a rate and the platform accepts.

### High — affects test surface coverage

6. **17 Literal/Enum fields scattered across 5 modules** with no single source of truth. Phase 2 mapping will need to merge by hand or refactor to a central enums module.

7. **839 endpoints / only 25 have frontend forms.** ~814 endpoints are API-only or backend-internal. These are all candidates for full-input synth coverage but currently exercised only by unit tests.

8. **9 PricingScheme options defined but explicitly "not wired."** Already documented in `feedback_pricing_toolkit_full_catalog.md` memory, but worth flagging here too — risk of backlog loss.

9. **12 Stripe endpoints still in code.** Operator confirmed 2026-05-10 that Stripe is NOT load-bearing (factoring is the revenue mechanism). These endpoints are candidates for removal in cleanup phase.

### Medium — config / wiring debt

10. **Dual-layer config pattern.** Env vars seed `db.business_settings` at boot. Creates two-source-of-truth for the same value. Good for vault migration; bad for "what's the current value of X?" questions.

11. **Mixed authentication patterns.** `EmptyTruckPostForm` uses public token-based endpoints; all admin forms use `withCredentials`. Worth a security audit during Phase 4 regulatory alignment.

12. **Phase-2 feature gates exist** (`operating_model_enabled`, `pricing_modes_enabled`, `global_pricing_mode_override`) — multi-model + dynamic pricing modes are wired but feature-flagged. Need explicit operator GO before flipping.

---

## Selectable-input catalog (the answer to "what can each entity choose?")

Selectable inputs grouped by what KIND of entity selects each. This is the seed for Phase 2 (entity-to-input matrix).

### Inputs the **broker (operator)** selects

- All 42 `db.business_settings` keys (margin floor, broker premium default, daily load cap, banking auto-sweep %, etc.)
- All admin-form fields (Add Driver, Add Load, Schema Defaults Profile, Carrier Preferences, etc.)
- All `require_owner` / `require_broker` endpoint payloads (234 + 316 = 550 endpoints)

### Inputs the **carrier** selects

- Cold-outreach offer accept payload: `name`, `mc_number`, `email`, `phone` (4 fields, no enum constraint)
- PIN-on-delivery: 6-digit numeric only
- Driver-onboarding form: equipment type (1 of 8), CCPA GPS opt-in, contact method preference
- Carrier-preferences form: equipment (1 of 9), home state, max radius, lane preferences, rate-per-mile floor
- Load status transitions: `at_pickup` → `loaded` → `en_route` → `at_delivery` → `unloaded` → `delivered`
- Document upload types: BOL / POD / Rate Confirmation signature

### Inputs the **shipper** would select (when integrated)

- EDI 204 fields (load tender) — currently NOT wired
- Rate-request email content — currently parsed by `email_ingestion.py` heuristics
- Cancellation reasons — currently free-text on broker side
- Multi-stop sequences — currently NOT modeled

### Inputs the **factor** would provide (when integrated)

- Submission acknowledgement (status: pending / approved / rejected)
- Approved amount / fee / advance %
- Rejection reason codes
- Chargeback notifications
- Funding ETA
- Currently MOCKED via internal test functions; no real factor API wired

### Inputs the **load board** would provide (when integrated)

- Load posting payload (origin, destination, equipment, rate, weight, pickup window, contact)
- Equipment-type taxonomy (DAT vs Truckstop vs 123 may differ)
- Posting freshness markers (TTL, last-updated)
- Currently NOT WIRED. NCS-1 (`board_adapter_normalizer.py` + `market_loads_observed.py`) is code-ready but never seen real data.

### Inputs **regulators** would provide (FMCSA / IRS / state SOS)

- FMCSA SAFER nightly poll output (REAL, cached in `db.fmcsa_lookup_cache`)
- Authority status changes (active / pending / revoked / suspended)
- Insurance certificate updates (BMC-91 / BMC-32)
- IRS notices — currently NOT modeled
- State SOS records — currently NOT modeled

### Inputs **driver / consignee** (downstream of carrier)

- GPS pings (PWA driver app — currently faked in synth)
- BOL / POD photo uploads (file)
- Rate confirmation signature (digital)
- Detention timer events (auto-detected)
- Consignee PIN entry (6 digits)

---

## Phase 2 prerequisites (what's needed before kicking off)

1. **Operator review of this Phase 1 inventory** (you reading this now)
2. **Operator scope decisions** for Phase 2:
   - Which entity types are in-scope for the matrix? (carrier / shipper / factor / load-board / regulator / driver / consignee — or just a subset?)
   - For "would-provide" inputs (shipper EDI, factor API, load-board API), do we author hypothetical input shapes from API docs, or wait until real integrations land?
   - Output format for Phase 2: a single matrix file, or per-entity files?

---

## Maintenance protocol

This inventory is auto-regenerable. Sub-agent prompts saved in commit history; re-run them periodically (weekly during active build, monthly after) to detect:
- New endpoints added without a corresponding form
- New enum values added without test coverage
- Removed endpoints with stale references in docs
- Drift between business_settings keys and env-var defaults
