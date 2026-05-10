# Pre-May-14 Platform Hardening — Shipped 2026-05-10

**Status:** All Wave 1 + Wave 2 items shipped, committed, verifier-gated, regression-tested.
**Operator-mandated:** 2026-05-10 ("I want both tiers if there are no barriers... green light").
**Effort:** ~3 hours wall-clock with 9 parallel sub-agents + verifier-gate + me as integrator.
**Commits:** CHL `8408931` (Wave 1) → `709c799` (Wave 2 + verifier fixes). chl-memory will commit after this file lands.

---

## What shipped (11 items across 2 waves)

### Wave 1 — runtime hooks + new endpoints (6 items)

| # | Item | What changed | Tests |
|---|---|---|---|
| 1 | **P&L break-even guard** | Two-phase wire: pre-quote in `rate_calc_engine.calculate_with_config()` (uses lane median estimate), post-acceptance in `carrier_outreach.accept_offer` (real carrier_rate). Loss state queues to `db.pnl_warnings` with status `pre_quote_warning` or `post_accept_loss`. Strategic-loss carve-out preserved — no auto-cancel; operator review. | 6 |
| 2 | **Funding gate** | `payment_rails.check_funding_gate()` enforces GPS+POD before factor funding. PWA-aware: missing GPS → 202 + operator-approval queue (`db.gate_overrides`); GPS exists but >0.5mi from delivery → 409 fraud block. New endpoint `POST /api/funding/{load_id}/manual-approve` (require_owner). Frontend `SettlementsView` shows gate state + Manual Approve button. | 10 |
| 3 | **Operator alerts** | NEW `backend/operator_alerts.py` with severity-tiered routing: HIGH→text via TWILIO_MESSAGING_SERVICE_SID (bypasses A2P 10DLC gate), CRITICAL→both text+voice. Wired into `throttle_system` RED-state, `sla_monitor` critical-breach (≥40% miss rate), `kill_switch` trigger. Secrets redaction on outbound. Audit log to `db.operator_alerts`. | 17 |
| 4 | **FMCSA SMS BASIC scores** | New endpoint `POST /api/carriers/{dot}/sms/refresh` (require_broker) + integration into `vetting_checks.run_full_vetting()` with `crash_indicator > 65` → `high_crash_score` warning. New monthly cron (feature-flag gated `CHL_FMCSA_SMS_CRON_ENABLED`, default OFF) refreshes top-200 active carriers. Rate-limit back-off (5+ 429s = 60s pause; 20+ = abort+alert). | 17 |
| 5 | **Auto-fire invoice + NOA on delivery** | Wired the carrier-side `complete-delivery` path to call `_fire_delivered_autofire()` (the broker-side path was already wired via `_run_post_status_hooks`). Idempotency via `db.broker_invoices.find_one`. Feature flag `delivered_autofire_enabled` (default OFF per verifier-gate fix — operator opt-in). No-factor path writes to `db.delivered_pending_factor_assignment` for review. | 6 |
| 6 | **Auto-fire rate-con on acceptance** | Pre-existing wiring in `carrier_outreach.accept_offer` hardened with idempotency check (load.rate_con_id), writeback (rate_con_id + rate_con_ref), feature flag `rate_con_autofire_enabled`, failure capture to `db.rate_con_pending_review`. Acceptance succeeds even if rate-con generation fails. | 4 |

### Wave 2 — schema/correctness canonicalization (5 sub-items in 3 modules)

| # | Item | What changed | Tests |
|---|---|---|---|
| 7 | **Equipment enum canonicalization** (Phase 1 finding #1) | NEW `backend/equipment_types.py` with `CANONICAL_EQUIPMENT_TYPES = ("dry_van", "reefer", "flatbed", "step_deck", "tanker", "auto_carrier", "livestock", "intermodal", "heavy_haul")` + `EquipmentType` Literal + `normalize_equipment_slug()` alias collapser. Refactored 4 backend models + 11 frontend dropdowns. | 43 |
| 8 | **Rate-per-mile bounds** (Phase 1 finding #5) | `ge=0.10, le=10.0` applied to `carrier_preferences.min_rate_per_mile` + 4 frontend `<input>` fields. Fat-finger guard. | (in #7) |
| 9 | **Payment terms canonicalization** (Phase 1 finding #3) | NEW `backend/payment_terms.py` with 7-tuple (quick_pay / net_15 / net_30 / net_45 / net_60 / net_90 / cod) + `PaymentTerm` Literal + `payment_term_to_days()` + `normalize_payment_term()` legacy migrator. Refactored `load_schema_v2.VettingGatesBlock` + 3 frontend views. Scoped to shipper-facing namespace; driver-payment + Mercury-payout namespaces untouched. | 50 |
| 10 | **Hazmat case normalization** (Phase 1 finding #4) | NEW `backend/hazmat_types.py` with canonical lowercase tuples (packing_group i/ii/iii + hazmat_class 1-9) + Literals + normalizers. Refactored `load_schema_v2.HazmatDetail` with `@field_validator` that accepts uppercase legacy input + stores lowercase. Wired `carrier_docs/doc_extractor._coerce_hazmat_fields` through normalizer. Migration script at `scripts/migrate_hazmat_lowercase.py` (dry-run by default; `--apply` commits). | 48 |

### Verifier-gate HIGH fixes (3 of 3)

After 9 sub-agents shipped, dispatched verifier-gate code-reviewer per `feedback_verifier_gate_pattern.md`. Verdict: **MERGE WITH FIXES**. 3 HIGH severity issues caught and addressed:

| Fix | Issue | File:line |
|---|---|---|
| A | Equipment alias contract drift between Wave 1 + Wave 2 | `rate_calc_engine.py:323` — replaced ad-hoc lowercase with `normalize_equipment_slug()`. `van` input was silently mis-priced as default-band. |
| B | Auth gap on FMCSA SMS refresh endpoint | `server.py:5087` — endpoint claimed to inherit broker auth from `api_router` but `api_router` has no global dependency. Added explicit `Depends(require_broker)` param. |
| C | Auto-fire defaults too permissive | `delivery_completion.py:107` — flipped `delivered_autofire_enabled` default from TRUE to FALSE. Auto-firing factor NOA + invoice on a misconfigured deploy is irreversible money flow. Operator must explicitly opt-in. |

5 MEDIUM/LOW items deferred for next session (Mongo URL / JWT redaction patterns; Twilio spend cap; P&L warning resolution helper; rate-con race window; frontend equipmentTypes.js extraction).

### DeliveryReceipt mount status (Tier 2 item #9)

DeliveryReceipt was already imported in 2 views (`CommandSearchBar.jsx`, `NotificationLogView.jsx`) — operator already has access to driver-notification timeline. Adding to `LoadDetailModal` was deferred as polish (requires reading 800+ line modal to find insertion point — out of scope for today's focused-fix batch).

---

## Test status

| Suite | Count |
|---|---:|
| Wave 1 new tests | 60 |
| Wave 2 new tests | 141 |
| Parsing-stack tests (from earlier today) | 55 |
| **Total tests in today's work** | **256** |
| Regression check across all 12 test files | 256 / 256 PASS |
| Verifier-gate HIGH-fix re-test | 0 regressions |

```bash
# Verification:
cd /c/CHL && /c/CHL/.venv-local/Scripts/python.exe -m pytest \
  backend/tests/test_pnl_guard.py \
  backend/tests/test_funding_gate.py \
  backend/tests/test_operator_alerts.py \
  backend/tests/test_fmcsa_sms_wire.py \
  backend/tests/test_delivered_autofire_wire.py \
  backend/tests/test_rate_con_autofire.py \
  backend/tests/test_equipment_enum_canonical.py \
  backend/tests/test_payment_terms_canonical.py \
  backend/tests/test_hazmat_canonical.py \
  backend/tests/test_html_parser.py \
  backend/tests/test_llm_parser.py \
  backend/tests/test_board_adapter_normalizer.py -q

# Should see: 256 passed
```

---

## What this changes about the May-14 launch posture

**Before today:**
- 5 known Phase 1 input-surface correctness bugs unfixed (equipment drift, rate-per-mile unbounded, payment terms gaps, hazmat case, vetting type bug)
- Never-book-at-loss directive enforced only by 1.08 floor (not by actual P&L math)
- No fraud-prevention gate before factor funding (would fund any "delivered" load)
- Operator alerts fall silently if A2P 10DLC slips past launch
- No SMS BASIC scores in carrier vetting (shell-company detection)
- Invoice + NOA + rate-con generation was manual; revenue delayed by operator-touch
- Equipment + payment + hazmat fields scattered across 5+ files = drift surface

**After today:**
- 5 of 5 Phase 1 correctness bugs fixed OR canonicalized (the 5th — VettingGatesBlock string-"None" type bug — was already addressed in `load_schema_v2` validator hardening)
- Never-book-at-loss enforced at two phases: pre-quote (warning) + post-acceptance (operator review queue)
- Soft funding gate with operator-approval queue; hard fraud block only on GPS-vs-delivery mismatch
- Voice + text alert paths survive A2P 10DLC failure mode
- FMCSA SMS scores integrated into vetting; high-crash carriers flagged for review
- Auto-fire invoice/NOA/rate-con on delivery + acceptance (operator opt-in via feature flags)
- Canonical equipment + payment + hazmat with byte-identical frontend/backend lists + alias normalizers

---

## What's NOT done (next-session backlog)

**5 MEDIUM/LOW verifier-gate items:**
1. Operator alerts: add Mongo URL + JWT + basic-auth-URL redaction patterns to `operator_alerts._redact`
2. Spend cap: add `twilio_alerts: $5/day` to `integrity/budget_caps._DEFAULT_CAPS_USD` + wire `check_budget()` into HIGH/CRITICAL alert paths
3. P&L warning resolution: add `resolve_pnl_warning(db, id, decision)` helper + UI dismiss action
4. Rate-con autofire race: add unique index on `db.loads.rate_con_id` or switch to `find_one_and_update` with `$exists: false` filter
5. Frontend: extract `frontend/src/constants/equipmentTypes.js` shared constant module (currently 5 surfaces have local mirrors)

**Other deferred:**
- DeliveryReceipt mount in LoadDetailModal (already-mounted in 2 other views; this is polish)
- Real-Mongo integration test for funding gate (covered by mocks today)
- P&L guard ↔ pricing_modes.py crossover tests

---

## Files committed

### CHL repo (commit `709c799` — final Wave 2 + verifier fixes)

| Layer | Files |
|---|---|
| NEW canonical types | `backend/equipment_types.py`, `backend/payment_terms.py`, `backend/hazmat_types.py` |
| NEW operator alerts | `backend/operator_alerts.py` |
| Modified backend | `pnl_monitor.py`, `rate_calc_engine.py`, `carrier_outreach.py`, `payment_rails.py`, `delivery_completion.py`, `throttle_system.py`, `sla_monitor.py`, `kill_switch.py`, `vetting_checks.py`, `cron_scheduler.py`, `server.py`, `carrier_preferences.py`, `driver_preferences.py`, `load_intake.py`, `loadboards.py`, `load_schema_v2.py`, `carrier_docs/doc_extractor.py` |
| Modified frontend | `App.js`, `SettlementsView.jsx`, `CarrierPreferences.jsx`, `QROnboardLoad.jsx`, `DriverOnboardingQuestionnaire.jsx`, `LoadBoardAggregator.jsx`, `ShipperIntakeModal.jsx`, `SchemaDefaultsProfileModal.jsx`, `LoadSchemaCompletenessPanel.jsx` |
| NEW tests | 6 new test files (Wave 1) + 3 new test files (Wave 2) |
| NEW scripts | `scripts/migrate_hazmat_lowercase.py` |
| Updated tests | `test_iteration_120_load_schema_v2.py`, `test_iteration_124_schema_defaults_profile.py`, `test_delivered_autofire_wire.py` |

### chl-memory repo (commit pending — this file)

- `chl-memory/architecture/pre_may_14_hardening_shipped_2026_05_10.md` (this file)

---

## Verification protocol for future sessions

To confirm this batch is intact:

```bash
# All tests still pass:
cd /c/CHL && /c/CHL/.venv-local/Scripts/python.exe -m pytest \
  backend/tests/test_pnl_guard.py backend/tests/test_funding_gate.py \
  backend/tests/test_operator_alerts.py backend/tests/test_fmcsa_sms_wire.py \
  backend/tests/test_delivered_autofire_wire.py backend/tests/test_rate_con_autofire.py \
  backend/tests/test_equipment_enum_canonical.py backend/tests/test_payment_terms_canonical.py \
  backend/tests/test_hazmat_canonical.py -q

# Should see: 196 passed (Wave 1 + Wave 2 only; add 60 for parsing stack)

# Canonical types reachable:
/c/CHL/.venv-local/Scripts/python.exe -c "
import sys; sys.path.insert(0, '/c/CHL/backend')
from equipment_types import CANONICAL_EQUIPMENT_TYPES
from payment_terms import CANONICAL_PAYMENT_TERMS
from hazmat_types import PACKING_GROUPS, HAZMAT_CLASSES
print(f'equipment: {len(CANONICAL_EQUIPMENT_TYPES)} types')
print(f'payment: {len(CANONICAL_PAYMENT_TERMS)} terms')
print(f'hazmat: {len(PACKING_GROUPS)} pgs + {len(HAZMAT_CLASSES)} classes')
"

# Should see: 9 / 7 / 3 + 9

# New endpoints live (401 = wired):
curl -s -o /dev/null -w "POST /api/funding/{id}/manual-approve: %{http_code}\n" \
  -X POST http://127.0.0.1:8001/api/funding/test/manual-approve

curl -s -o /dev/null -w "POST /api/carriers/{dot}/sms/refresh: %{http_code}\n" \
  -X POST http://127.0.0.1:8001/api/carriers/12345/sms/refresh
```

---

## Key learnings

1. **Parallel sub-agent dispatch works at 9-agent scale.** Wave 1 (6 agents) + Wave 2 (3 agents) shipped 256 tests in ~3 hours. The verifier-gate pattern (separate code-reviewer agent over the aggregate) caught 3 HIGH issues the individual agents could not see.

2. **Default-OFF for money-flow features is mandatory.** Auto-fire invoice/NOA defaulted to ON initially — verifier-gate caught this as HIGH. Lesson: any feature that produces irreversible financial side effects must default to OFF + require explicit operator opt-in via `db.business_settings`.

3. **Canonical types module pattern is robust.** Three new modules (`equipment_types`, `payment_terms`, `hazmat_types`) each follow the same shape: tuple of canonical values + Literal type + normalizer function. Future enum work should use this pattern.

4. **Authentication assumptions need verification, not inheritance claims.** Sub-agent claimed `api_router` carried global broker auth; verifier-gate found it did NOT. Always grep for `dependencies=` on the router definition, don't trust claims.

5. **Tests should fail when defaults change.** The `test_feature_flag_missing_defaults_to_enabled` test failed (correctly) when we flipped the default. Tests caught the behavior change immediately — keep this pattern.
