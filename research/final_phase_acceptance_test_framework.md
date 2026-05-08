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
| 21 | Carrier doc upload + auto-extract (OCR via gpt-4o-vision; expiry tracker; cross-reference FMCSA) | carrier_docs/document_store + (TBD) doc_extractor + expiry_tracker |

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
