# CHL Synthetic Test — Auto-Fix Log

**Started:** 2026-05-09
**Policy** (operator-mandated 2026-05-09):
- Real fixes only — no simulated/stubbed fixes
- Each detected bug: classify, fix or escalate, document, commit, verify
- Auto-fixable (clear pattern, low risk): I commit immediately
- Operator-review (ambiguous, architectural, security, money-affecting, schema): write to `db.dev_review_queue`
- Critical failsafe miss: `db.urgent_alerts`
- Operator action needed: `db.operator_push_queue`

**Format per entry:**
```
## YYYY-MM-DD HH:MM UTC · <commit-hash> · <severity>
**Detected by:** <scenario name + telemetry collection row>
**Bug:** <what was wrong, file:line>
**Root cause:** <why>
**Fix applied:** <what I did, diff or summary>
**Verification:** <re-ran scenario, result>
```

---

## Bugs queued from prior sub-agent reports (pending verification + fix)

### LL_LOGIC_BUG_001 · Reachability bug in weight bracket
- **File:** `backend/server.py` (rate-calculator weight branching)
- **Detection:** rate-calc agent (sub-agent C) flagged in design phase: `if w > 40000 ... elif w > 45000` — second branch unreachable. Once `w > 40000` is true, the elif never fires.
- **Risk:** loads at >45,000 lbs may not get the heavier-weight rate factor they should.
- **Severity:** auto-fixable (clear bug, low risk)
- **Status:** PENDING — will fix on first scenario hit

### LL_PLATFORM_GAP_001 · No equipment-type rate premium calculator
- **Detection:** rate-calc agent flagged 8 scenarios OPEN
- **Status:** OPERATOR_REVIEW (architectural — needs design decision on premium %s per equipment type)
- **Routing:** dev_review_queue with proposed default schedule

### LL_PLATFORM_GAP_002 · No commodity surcharge / blocking calculator
- **Detection:** rate-calc agent flagged 9 scenarios OPEN (HAZMAT, hi-value, oversize, livestock)
- **Status:** OPERATOR_REVIEW

### LL_PLATFORM_GAP_003 · No special-condition uplift calculator
- **Detection:** rate-calc agent flagged 7 scenarios OPEN (team, TWIC, HAZMAT-endorsed, tarped, etc.)
- **Status:** OPERATOR_REVIEW

### LL_PLATFORM_GAP_004 · Limited accessorial calculator
- **Detection:** Only detention + TONU exist as proper calculators. Lumper, layover, stop-off, driver-assist, liftgate, inside-delivery, reweigh are freeform line items with no native calculation.
- **Status:** OPERATOR_REVIEW

### LL_PLATFORM_GAP_005 · Missing reject validators
- **Detection:** No validators for: negative weight, negative rate, HAZMAT class 1 without driver endorsement, oversize without state permit, reefer without temp set-point.
- **Status:** OPERATOR_REVIEW

### LL_AUTH_GAP_001 · Stripe webhook signature lock blocks shipper-pay simulation
- **Detection:** multi-actor synthesizer (sub-agent D) — Stripe webhook returns 401 on synthetic payload. Real factor payment uses portal polling, not Stripe.
- **Severity:** intentional security posture (correct), but blocks synthetic testing of payment flow
- **Status:** OPERATOR_REVIEW — proposed `/api/_test/stripe-webhook-replay` (require_owner) endpoint that signs replay payloads. Operator decides if this is in scope.

### LL_PLATFORM_GAP_006 · No shipper-side dispute endpoint
- **Detection:** multi-actor synthesizer probed `/api/public/shipper/dispute`, `/api/shipper/disputes`, `/api/public/loads/dispute` — all 404. Factor-side dispute resolver exists but is broker-authenticated.
- **Status:** OPERATOR_REVIEW — proposed `POST /api/public/shipper-claim` endpoint

### LL_FAILSAFE_GAP_001 thru LL_FAILSAFE_GAP_007 · 7 untested compliance gates (May-14 risk)
- **Source:** `chl-memory/research/failsafe_coverage_matrix_2026_05_09.md`
- C17: D&A Clearinghouse status check
- C18: HOS (Hours of Service) compliance
- C19: HAZMAT endorsement
- C20: California CARB compliance
- C21: state UCR/IRP/IFTA
- F31: cash valve
- F27: auto_credit_check
- **Status:** OPERATOR_REVIEW — some may be intentional pre-launch scope cuts (PWA-primary tracking memory)

---

## Fixes applied during this run

### 2026-05-10 02:30 UTC · LL_FAILSAFE_GAP_POD · severity=auto-fixable

**Detected by:** SOP orchestrator scenario `fi_pod_missing_on_deliver` — expected `consignee_confirmation_required`, got `actual=None`. Driver advanced to `delivered` without primary_pod upload.

**Bug:** State machine `delivered` transition had no precondition check for POD presence. The platform's existing POD gate at `factor_refusal_guard.py:448` correctly blocks factor submission downstream, but the load could still flip to `delivered` status, generating draft invoices + leaving the load in a "delivered but unfundable" workflow-stuck state.

**Root cause:** `backend/load_state_machine.py` `perform_transition()` validated chain order but had no precondition layer for transition-specific requirements (POD on `delivered`, etc).

**Fix applied:**
- Added `PreconditionRequiredError` (HTTP 412 `precondition_required`)
- Added `_check_preconditions(db, load_id, new_status)` async helper
- For `new_status=='delivered'`: query `db.pod_uploads` for `{load_id, kind: 'primary_pod'}`. If missing, raise 412 with `next_action=upload_pod` hint for the driver UI.
- Synthetic loads honor the gate same as production (no bypass) — defense-in-depth means test path mirrors prod.

**Verification:** `scripts/smoke_pod_gate.py` end-to-end:
- Step 5: delivered without POD → 412 `primary_pod` ✅ blocked
- Step 7: POD uploaded → delivered → 200 ✅ allowed

**Commit:** (next push)
**Files:** `backend/load_state_machine.py` (+39/-3 lines)
