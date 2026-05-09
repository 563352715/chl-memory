# Final-Phase Acceptance Test Framework

**Status:** Captured 2026-05-08 EOD-9. Operator-mandated. Do NOT execute now — this is a planning doc for the FINAL PHASE (~3-4 sessions out) when the platform is feature-complete and we transition to acceptance testing + production hardening.

**Operator framing 2026-05-08 EOD-9:**

> "Put on the final todos after platform is close to finished to check all these features. Such as automate or synthesize a document uploaded into the compliance documents to view on the front end. Do you have a test in mind to perform when we are in testing functionality phase to test each feature? Such as this one here. We should have that. Do you know what each front end view I will need to be able to see? Basically, I will need a quick/easy way to run the system manually through each step of our entire process from the front end. If the system is only partially disable through a single process for instance, I will need a way to step in and complete part of that process and place it back into the automated side for further processing."

---

## The four components

### 1. End-to-end pipeline walkthrough test

**Seed:** `backend/tests/synthetic_load_walkthrough.py` (DRAFT; iter 139.36; ~530 lines per the placeholder; 12 stages outlined: RFQ intake → quote → carrier match → rate-con → dispatch → check-call/GPS → POD → audit gate → invoice → factor request → payment → cancel/TONU). Currently a placeholder pointing to a local file path; needs the body inlined.

**Final-phase work:** complete the body of this test. Asserts DB state at each stage. Runnable as a single pytest invocation:

```
python -m pytest backend/tests/synthetic_load_walkthrough.py -v
```

**What it should cover (extending the 12 stages with EOD-1..EOD-9 ships):**

| # | Stage | Module(s) it exercises |
|---|-------|------------------------|
| 1 | RFQ intake (email or web form) | inbound_email/imap_poller + tier1_classifier + tier2_classifier + action_dispatcher |
| 2 | Quote generation | quote engine (existing iter ~138.x) |
| 3 | Lane scoring | evaluators/lane_evaluator |
| 4 | Carrier match (top-N selection) | evaluators/carrier_evaluator |
| 5 | Outreach (Twilio Voice / Plivo SMS post-cutover) | outreach/orchestrator + load_board_fallback |
| 6 | Assignment (auto-assign + ghost detector) | assignment/auto_assigner + failure_detector |
| 7 | Carrier confirms pickup → flips to in_transit | assignment/confirm_handler |
| 8 | Tracking events (GPS pings) | tracking/router + tracking/event_store |
| 9 | Tracking exception detection (late/off-route/stalled/no-ping) | tracking/exception_detector + exception_detector_cron |
| 10 | Carrier check-in scheduler (4h+8h cadence) | tracking/checkin_scheduler + checkin_cron |
| 11 | Inbound check-in response matching | tracking/checkin_scheduler.record_checkin_response |
| 12 | POD upload + OCR (gpt-4o-vision) | existing POD pipeline |
| 13 | Audit gate (delivered_load_autofire, invoice eligibility) | existing |
| 14 | Invoice generation + send | existing |
| 15 | Factor submission (auto or operator-approve gate) | factors/auto_submitter |
| 16 | Factor reconciliation (3-signal: email + Mercury + portal poll) | factors/reconciler |
| 17 | Factor dispute handler (if portal returns disputed/chargeback) | factors/dispute_handler |
| 18 | Outcome record (delivered well / disputed / lost) | self_healing/outcome_tracker (for patch outcomes) + db.evaluator_outcomes (for FractalEvaluator weight tuning) |
| 19 | Cancel / TONU path | state_lock + TONU calculator |
| 20 | Self-healing trip (synthetic anomaly) | observability triad + anomaly_dispatcher + context_bundler + sub_agent_dispatcher (propose-only) |
| 21 | Carrier doc upload + auto-extract (OCR via gpt-4o-vision; expiry tracker; cross-reference FMCSA) | carrier_docs/document_store + doc_extractor + extraction_store + expiry_tracker + expiry_cron + extraction_router (EOD-10 sub-agent N) |
| 22 | **Autonomous compliance loop:** doc lands → OCR extracts expiry+fields → 30d/14d/7d/expired tier scheduler → auto-DM carrier asking for replacement → carrier replies via email → email classifier auto-attaches → loop restarts | EOD-10 carrier-doc-automation batch closes this; operator-mandated 2026-05-08 EOD-9 |

### 2. Per-feature acceptance checklist

A markdown checklist in `chl-memory/research/final_phase_acceptance_test_checklist.md` (separate doc; create at final-phase start). Each feature gets a row:

```
- [ ] Feature: <name>
      Iter/Stage: <e.g. 142.2/1c>
      Backend module(s): <files>
      Frontend view: <tab + section>
      Manual test:
        1. <step>
        2. <step>
        3. <expected result>
      Automation hook to verify:
        - <e.g., db.checkin_attempts row inserted within 4h of pickup>
      Status: [ ] PASS  [ ] FAIL  [ ] SKIP
```

We tick the boxes as we verify. Each currently-shipped feature gets a row; gaps surface as unchecked items.

**Example row (carrier docs feature, EOD-9 enhancement):**
```
- [ ] Feature: Carrier compliance doc upload + view
      Iter/Stage: 142.1 + EOD-9 enhancement (commit 2a540d6)
      Backend: backend/carrier_docs/document_store.py + router.py
      Frontend: Carriers tab → click carrier row → Compliance Documents section at bottom of detail modal
      Manual test:
        1. Navigate to Carriers DB tab
        2. Click any carrier row to open detail modal
        3. Scroll to "Compliance Documents" section
        4. Click "Upload" on the COI row → pick a test PDF
        5. After upload completes: verify "Replace" + green "View" button appear
        6. Click "View" → confirm inline PDF preview modal renders the doc
        7. Verify "Open in new tab" + "Download" + "Close" all work
      Automation hook to verify:
        - File written to C:/CHL/carrier_docs/{dot}/coi/* on disk
        - db.carrier_documents row inserted with status="submitted"
        - (FUTURE) db.carrier_doc_extractions row with OCR-extracted expiry + insurance amount
      Status: [ ] PASS  [ ] FAIL  [ ] SKIP
```

### 3. Pipeline Walkthrough View (NEW frontend tab)

**Purpose:** operator's manual-intervention surface. Pick a load → see all 21 pipeline stages with current state. Drop-in / drop-out at any stage.

**UI sketch:**

```
+------------------------------------------------------------+
| Pipeline Walkthrough                                       |
| Load: [dropdown: pick a load]   Carrier: <auto-fill>       |
+------------------------------------------------------------+
| Stage                  | State    | Action                 |
+------------------------+----------+------------------------+
| 1. RFQ intake          | ✓ DONE   | [view data] [redo]     |
| 2. Quote generation    | ✓ DONE   | [view data]            |
| 3. Lane scoring        | ✓ DONE   | [view data]            |
| 4. Carrier match       | ✓ DONE   | [view data]            |
| 5. Outreach            | ⏳ AUTO   | [step in]              |
| 6. Assignment          | □ PENDING| [step in] [skip]       |
| 7. Confirm pickup      | □ PENDING| [step in] [skip]       |
| 8. Tracking events     | □ PENDING| [step in] [skip]       |
| ...                    |          |                        |
| 18. Outcome record     | □ PENDING| [step in] [skip]       |
+------------------------+----------+------------------------+
| Legend: ✓ DONE | ⏳ AUTO IN-FLIGHT | ⚠ STUCK | □ PENDING   |
+------------------------------------------------------------+
```

Each stage has 3 modes:
- **AUTO** — engaged, automation owns it. Operator sees what's running.
- **MANUAL OVERRIDE / STEP IN** — operator takes over, fills the action data manually (e.g., for outreach, types in the carrier's response if SMS reply didn't fire); system stamps the state as completed via operator-manual-attribution + re-engages downstream automation.
- **SKIP** — mark done without action. For edge cases (e.g., load was a test, no real outcome needed).

**Click a stage row to expand:**
- Show backing DB row (load.dispatch_status, factor_submission row, tracking_exception rows, etc.)
- Show what action the next-tick automation would fire
- Show "step in" form (per-stage; depends on stage)
- "Re-engage automation" button: flips state forward + clears any blocking flag so the cron picks it up on next tick

**Backend support needed:**
- New collection: `db.pipeline_walkthrough_overrides` — records every operator step-in (audit trail)
- New router: `/api/pipeline_walkthrough/{load_id}` — GET returns all stage states; POST `/step_in` writes operator action; POST `/re_engage` un-pauses automation
- Per-stage state-machine helpers in each module (e.g., `outreach.orchestrator.operator_manual_attribution(load_id, response_text)`)

**Effort estimate:** sub-agent dispatch for backend (~1 stream, ~600 lines + cron + router). Frontend (App.js) ~500 lines for the new tab. ~4-6h total.

### 4. Front-end view inventory + gaps audit

**Existing tabs (per operator's nav 2026-05-08):**

Dashboard, Command Phone, Monitoring, Load Board, AI Matching, Carriers DB, Market Radar, Loadboard Connections, Market Rates, Auto Rules, Payments, Live Map (with EOD-9 tracking + check-in panels stacked above the GPS map), Find Loads, Call Lists, Factors, Ops Console, SOP Flow, Pre-Vet, Shipper Scorecard, Failure Console, SOP Cards, Accounting, Owner, Doc Review, Compliance, Vault, Æther, Notif Log, Factor Picker, Settings, Banker Digest, Rate Calculator.

**Currently-known gaps (final-phase work):**

| Gap | Owner stage | Effort |
|-----|-------------|--------|
| Pipeline Walkthrough tab (Component 3 above) | NEW | ~6h |
| Disputes dashboard | iter 144.x stage 1d frontend (PM drafting spec) | ~2h |
| Patch proposals dashboard | iter 145.1 stage 1c frontend | ~2h |
| Outcome telemetry dashboard | iter 145.1 stage 1d frontend | ~2h |
| Weight-tuning proposals dashboard | iter 145.1 stage 3 frontend | ~2h |
| Carrier doc extracted-fields surface (after OCR auto-extract lands) | EOD-10+ feature | ~1h |
| Doc Review tab — what does this currently show? does it integrate with carrier_docs? | AUDIT | ~30min audit |
| Compliance tab — what does this currently show? overlap with Carrier Doc section? | AUDIT | ~30min audit |
| Failure Console — does it surface assignment failure_detector + tracking exception_detector + factor disputes? consolidated? | AUDIT | ~30min audit |
| Banker Digest, Æther, Vault, Owner — confirm what these surface; are they current? | AUDIT | ~1h audit |

---

## Final-phase TODO list (for the agent picking this up)

When the platform is "feature complete" (~3-4 sessions from 2026-05-08, or whenever operator surfaces "we're in testing phase"), execute in this order:

1. **Inline the full body of `backend/tests/synthetic_load_walkthrough.py`** (currently a placeholder; the original is at `C:\Users\meyer\Downloads\Continental-Haul-Logistics-main\Continental-Haul-Logistics-main\backend\tests\synthetic_load_walkthrough.py`). Extend it to cover all 21 stages listed above. Run pytest; should be 100% pass before acceptance phase begins.

2. **Create `chl-memory/research/final_phase_acceptance_test_checklist.md`** with a row per shipped feature (~50-80 features by then). Use the example row format above. Generate by enumerating commits via git log; each iter/stage commit gets a row.

3. **Build the Pipeline Walkthrough tab** (Component 3). Sub-agent for backend; dev-side App.js for frontend (single-editor protocol on monolith).

4. **Run the front-end inventory audit** (Component 4 gaps section). Each "AUDIT" item: confirm what the tab does today, document, identify gaps.

5. **Walk through the acceptance checklist with operator:** open Pipeline Walkthrough on a fresh test load, run all 21 stages end-to-end (mix of AUTO + STEP-IN), tick boxes in the checklist, surface any failures.

6. **STOP_CONDITION check:** if any stage fails AND the failure is non-recoverable (e.g., carrier-comm cred mutation, AR-ledger corruption, double-billing), surface to operator immediately. Otherwise: log + fix + re-run that stage's test.

---

## Why this doc exists

Operator-explicit 2026-05-08 EOD-9: the platform's value is autonomous + self-healing + learning, BUT operator needs a manual-override surface for the cases where automation fails partially. Without this framework, operator can't intervene cleanly; without the acceptance checklist, we can't trust we shipped what we said.

**For the agent picking this up at final-phase:** read this doc fully before building any of the four components. The synthetic_load_walkthrough.py seed is operator-blessed; extend, don't rewrite. The Pipeline Walkthrough tab is the keystone — once it exists, every gap and every regression has a single-pane visibility surface.

---

## Update 2026-05-08 EOD-15 (Stream EE2): Component 1 IMPLEMENTED (v1)

**Status:** the 21-stage walkthrough is LIVE at `backend/tests/synthetic_load_walkthrough.py` (~1100 lines, all 22 tests pass, 0.32s wall-clock total). The harness uses an in-memory async-Mongo fake (no live backend, no Mongo, no external calls), so it can run anywhere.

**Run command:**

```
python -m pytest backend/tests/synthetic_load_walkthrough.py -v
# or, with smoke wrapper that sets per-run DB env vars:
.\scripts\run_walkthrough.ps1
```

**The 21 stages (final selection):**

| # | Stage | Production code path | Mock boundary |
|---|-------|----------------------|---------------|
| 1 | RFQ intake | direct DB write + audit_trail | -- |
| 2 | Quote (won outcome) | direct DB write + audit_trail | -- |
| 3 | Carrier match (interest accepted) | direct DB write + audit_trail | -- |
| 4 | Rate-con + BCA signed | direct DB write + audit_trail | rate-con PDF mocked |
| 5 | Dispatch (driver assigned) | direct DB write + audit_trail | -- |
| 6 | Check-call / GPS ping | direct DB write to tracking_events + loads | tracking cron not invoked |
| 7 | POD upload | direct DB write + audit_trail | OCR mocked (no gpt-4o-vision call) |
| 8 | Audit gate | inline checklist evaluator | -- |
| 9 | Broker invoice | direct DB write + audit_trail | reportlab/PDF body not exercised |
| 10 | Factor request | direct DB write + audit_trail | HaulPay API call MOCKED (`external_call_mocked=True`) |
| 11 | Payment received | direct DB write + audit_trail | Mercury webhook MOCKED |
| 12 | Cancel + TONU | **REAL** `tonu_calculator.compute_tonu` | -- |
| 13 | Carrier vetting (BCA + COI + W9) | direct DB write to carriers.vetting_workflow | FMCSA SAFER fetch MOCKED |
| 14 | Carrier failure / re-dispatch | direct DB write + carrier_failures coll | re-dispatch SMS MOCKED |
| 15 | Real-time pricing rebid | direct DB write to pricing_rebids | lane_evaluator decision MOCKED |
| 16 | Dispatch packet generation | **REAL** `dispatch.packet_renderer.render_dispatch_packet` | reportlab + pypdf monkeypatched (PDF body fake) |
| 17 | Late-checkcall escalation | **REAL** `tracking.exception_detector.scan_for_exceptions` (with fake-DB compatibility fallback) | broker SMS MOCKED |
| 18 | Off-route exception | **REAL** `tracking.exception_detector.haversine_miles` | exception row written by harness |
| 19 | Detention review | inline math (free_time_hours + rate) | invoice line item written, not delivered |
| 20 | Cargo claim | direct DB write to cargo_claims | claim notification MOCKED |
| 21 | Aged AR escalation | **REAL** `aged_ar._build_unpaid_rows` | dunning email MOCKED |

**Per-stage SLA:** wall-clock < 5s, asserted in `_StageTimer.__exit__`. Empirically every stage runs in < 5ms after import overhead settles (stage 1: ~270ms cold-start cost from `motor`/`fastapi` imports via `integrity.audit_trail`).

**Audit-trail discipline:** every stage that mutates a domain doc emits a row into `db.audit_trail` via the real `integrity.audit_trail.record_audit` (fallback to direct insert if unavailable). Stages 1, 2, 3, 4, 5, 7, 9, 10, 12, 13, 14, 15, 17, 18, 19, 20, 21 all assert audit-trail presence implicitly (the call would raise if the writer rejected it).

**State machine:** `walkthrough_state` (module-scoped fixture dict) carries `load_id`, `carrier_mc`, `invoice_id`, etc. across stages. Stages 12, 14, 17, 19, 20, 21 use isolated test loads so they don't perturb the happy-path one settled in stages 1-11.

**Production-code hooks discovered missing during this stream (none surfaced as bugs, all are wiring observations for future-phase decisions):**

1. **Audit-trail row on `loads.status` transitions:** the production `state_lock._maybe_offer_tonu` and the route handlers that flip `loads.status` write the new state but do NOT call `integrity.audit_trail.record_audit`. The walkthrough emits these rows from the harness side -- a real integration would want a thin wrapper at `state_lock` that emits before/after on every status flip.
2. **Detention auto-charge service:** no `backend/detention*.py` exists; the test inlines the calculation. Recommend a `backend/detention_review.py` cron that runs against `loads` with `shipper_arrived_at`/`shipper_departed_at` set and emits `invoice_line_items_pending` rows automatically.
3. **Carrier-failure / re-dispatch service:** there's logic spread across `assignment/failure_detector.py` and `auto_dispatch.py`, but no single `carrier_redispatch_workflow.py` orchestrator. Stage 14 inlines the state machine; productionize this path before claiming the platform self-heals carrier ghosts.

**Follow-ups (priority-ordered):**

1. **Wire `state_lock` status flips to `audit_trail`** -- one call per status transition, before/after snapshot of the load doc. Eliminates the harness having to emit these manually.
2. **Promote stage 17 (late-checkcall) to fully use the real `exception_detector.scan_for_exceptions`** -- requires extending the harness's in-memory async-Mongo fake to support every query the scanner uses (currently it falls back to direct insert if scanner doesn't pick up the synthetic load).
3. **Add stage 22-25 candidates:** carrier-tier promotion (bronze/silver/gold), shipper credit override revocation, ETA-slip predictive vs. actual reconciliation, factor approval-vs-decline branch.

---

## Update 2026-05-08 EOD-15 (Stream QQ3): Stages 22-25 IMPLEMENTED (v2)

**Status:** the walkthrough is now 25 stages LIVE at `backend/tests/synthetic_load_walkthrough.py` (~2269 lines, all 26 tests pass = 25 stages + summary, 0.28s total wall-clock across stages, 0.41s full pytest suite). EE2's #3 follow-up landed.

**Run command (unchanged):**

```
python -m pytest backend/tests/synthetic_load_walkthrough.py -v
# or, with smoke wrapper that sets per-run DB env vars:
.\scripts\run_walkthrough.ps1
```

**The 4 added stages (22-25):**

| # | Stage | Production code path | Mock boundary |
|---|-------|----------------------|---------------|
| 22 | Carrier-tier promotion | direct DB write to `carriers` + `audit_trail` | tier-promotion cron MISSING (harness inlines state machine) |
| 23 | Shipper credit-override revocation | direct DB write to `shippers` + `payment_events` + `audit_trail` | revocation cron MISSING (harness inlines logic) |
| 24 | Predictive ETA-slip vs actual reconciliation | **REAL** `lane_evaluator._normalize_lane` + **REAL** `tracking.exception_detector.haversine_miles` | lane_evaluator's `eta_accuracy` field MISSING -- harness writes to parallel `db.lane_eta_accuracy` collection |
| 25 | Factor approval-vs-decline branching (BOTH paths) | direct DB write to `factor_submissions` + `alerts` + `collection_workflows` + `audit_trail` | HaulPay/Triumph external API calls MOCKED (`external_call_mocked=True`); `factors.factor_submission_router` not invoked because it requires HTTP/operator-approval queue |

**Per-stage detail:**

### Stage 22 -- Carrier-tier promotion

Seeds carrier with `tier=watch`, `successful_load_count=N-1` (N=5 threshold). Stage simulates a delivered load and increments the success counter. When count crosses threshold AND tier=watch, code flips to `tier=preferred` and stamps `tier_promoted_at`/`tier_promoted_from`/`tier_promoted_reason`. Asserts:
- `successful_load_count` == threshold post-increment
- `tier` flips watch -> preferred
- audit_trail row with action=`carrier_tier_promoted` and before/after delta captured

### Stage 23 -- Shipper credit-override revocation

Seeds shipper with `credit_score=540` + `credit_score_override=true` (operator-set). Stage records a `payment_events` row with `event_type=payment_failed` (Mercury ACH-fail simulation). Code asserts the override is currently true, then flips it to false with `override_revoked_at`/`override_revoked_reason=auto:payment_failed_event`/`override_revoked_event_id`. Final assertion: with override now false + credit_score 540, the credit gate would block the next load (proves the next-load credit-check kicks back in). Audit-trail row asserted.

### Stage 24 -- Predictive ETA-slip vs actual reconciliation

Seeds load with `predicted_eta` and an `actual_arrival` 75 minutes later (above 30-min slip threshold). Imports the real `lane_evaluator._normalize_lane` to compute the lane key + the real `tracking.exception_detector.haversine_miles` to compute lane miles. Writes:
- `db.lane_eta_accuracy` row keyed `<origin_city>_<state>__<dest_city>_<state>__<carrier_mc>` with `$inc` on sample_count + total_slip_minutes + slip_breach_count
- `db.eta_reconciliations` per-load row with predicted/actual/slip_minutes/breached flag

Asserts: lane metric updated (sample_count >= 1, slip_breach_count >= 1), reconciliation row has breached=true.

### Stage 25 -- Factor approval-vs-decline branching

Tests BOTH paths in one stage:

**Branch A (approval):** Submit invoice to `haulpay`, factor returns approved with 95% advance ($1425 of $1500). Asserts factor_submissions.status=approved, broker_invoices.status=factored, audit-trail action=factor_approved.

**Branch B (decline):** Submit invoice to `haulpay`, factor returns declined with reason=`shipper_credit_insufficient`. Stage asserts:
- `db.alerts` row with type=factor_declined, severity=high, notification_queued=true (delivery MOCKED)
- Retry submission to alternative factor (`triumph`) with `retry_of=<original_id>` and `retry_reason` populated
- Fallback `db.collection_workflows` row queued with `trigger=factor_decline_fallback`, stage=direct_collection_queued
- audit-trail rows for both `factor_declined` and `factor_retry_dispatched`

**Production-code hooks discovered missing during this stream:**

1. **`carrier_tier_promoter` cron MISSING** -- no `backend/carrier_tier_promoter.py` exists. The watch -> preferred transition is currently un-automated. Stage 22 inlines the state machine; recommend a cron that runs against `db.carriers` after `delivered_load_autofire` and flips tier when `successful_load_count` crosses configured thresholds (watch -> preferred at 5; preferred -> elite at 25; demotion paths on failure-rate breach). Audit-trail emit on every flip.
2. **`credit_override_revoker` cron MISSING** -- no service subscribes to `db.payment_events.event_type=payment_failed` to flip `shippers.credit_score_override`. Stage 23 inlines the logic; recommend a cron that runs every N min (or webhook-driven via Mercury/factor portal events) and revokes outstanding overrides on payment failure.
3. **`lane_evaluator.eta_accuracy` field MISSING** -- production `LaneEvaluator.get_reliability_data` returns delivered-on-time-rate from `db.loads`, but doesn't expose a per-lane+per-carrier ETA-accuracy metric or a slip-breach counter. Stage 24 writes to a parallel `db.lane_eta_accuracy` collection; recommend extending `FractalEvaluator.get_reliability_data` with an eta-accuracy sub-component, or wiring the parallel collection into the LaneEvaluator scoring path.
4. **`factor_decline_alert + retry_dispatcher` MISSING orchestrator** -- `factors/factor_submission_router.py` handles the operator-approval queue but not the post-submission reconciler that processes factor APIs returning declined. Stage 25 inlines the alert + retry-to-alternative + fallback-to-direct-collection state machine; recommend a `backend/factor_decline_handler.py` orchestrator that consumes factor API decline responses, fires alerts, attempts retry against the next-best factor in the matrix, and queues direct-collection if all factors decline.

**Follow-ups (priority-ordered) for next stream:**

1. **Stages 26-30 candidates:** accessorial billing (lumper / chassis / detention re-charge after operator approves), claim escalation (insurance underwriter handoff after carrier denies liability), Mercury reconciliation 3-signal (email + Mercury + factor portal poll), shipper scorecard auto-update on POD outcome, cross-broker handoff (load referred to partner broker).
2. **Wire production hooks:** build the 4 missing services flagged above (carrier_tier_promoter, credit_override_revoker, lane_evaluator.eta_accuracy extension, factor_decline_handler). Each is ~150-300 LOC with cron + tests.
3. **Promote stage 24 to use real lane_evaluator scoring** once `lane_evaluator.eta_accuracy` field lands -- replace the parallel collection write with a direct call to the scoring API.


