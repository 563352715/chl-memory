# Iter 144.x Phase 6 Factoring & Settlement — Preliminary Scope Sketch

> **Source:** PM Claude (`@pm-lead`) drafted 2026-05-07 EOD-3 per @dev-engineer task brief during autonomous-continue mode (post-iter-141.1-close + iter-142.1-Phase-1+2+4+5-shipped + iter-141.3-stage-1a-blocked).
> **Status:** Preliminary sketch (~1 page). Not a full agenda. Operator + dev review; full agenda by @pm-lead when operator authorizes iter 144.x to open.
> **Strategic phase:** `CHL_STRATEGIC_PLAYBOOK.md` Phase 6 (Factoring & Settlement). Milestone: "Cash flow happens autonomously" — RFQ-to-paid loop closes without operator intervention on routine flow.

> **⚠ NAMING COLLISION (read this carefully):** Two distinct workstreams both reference "Phase 6":
> - **iter 144.x = strategic-playbook Phase 6** (factoring/settlement; cash-flow loop closure). **THIS DOC.**
> - **iter 145.1 = self-healing-foundation "Phase 6+"** (anomaly dispatcher / patch proposer / outcome-feedback; substrate for autonomous platform improvement). See `phase_6_self_healing_roadmap.md`.
> Cross-refs in both directions: 144.x outcomes feed iter 145.1's `FactorEvaluator` re-tuning; iter 145.1 anomaly-dispatcher diagnoses 144.x failure modes (factor wire failed, dispute spike, etc.). Different scopes, complementary loops. Use the iter number to disambiguate, not "Phase 6" alone.

---

## 1. Goal + scope

**Concrete framing:** shift from today's *"Apex + Triumph SFTP live; everything else manual operator-touch"* state to *"fully automated factor selection + submission + reconciliation + dispute-handling for the top 4-6 factor providers in freight brokerage."* The cash-flow loop closes without operator intervention on routine flow; operator-touch reserved for exceptions (disputes, orphan deposits, multi-factor anomalies).

**North-star pillar #1 alignment (removes operator-touch):** Phase 6 is the **highest operator-touch surface today** in the load lifecycle — every load delivered today requires manual factor-selection + manual invoice-submission + manual reconciliation against bank deposits. Each step is a 5-15 min operator action × 50-200 loads/month at v1 scale = 4-50 hours/month of operator-touch in cash-flow alone. Phase 6 collapses this to 0 min on routine + ~5 min/exception.

**North-star pillar #2 alignment (adds observability surface):** factor-payment-latency tracker is a **new observation surface** (per iter 145.1 Phase 6+ self-healing roadmap §1 Detection Layer "Gaps — proposed new primitives"). Tracks per-factor `{factor_id, invoice_submitted_at, payment_received_at, latency_hours, amount}` events; surfaces degrading-factor-relationship anomalies (e.g., factor company starting to slow-pay → predictive of upcoming financial stress).

**North-star pillar #3 alignment (substrate for self-healing):** iter 144.x v1 ships hard-coded factor-selection rules + canned dispute-response templates; iter 145.1+ replaces with `FactorEvaluator` (FractalEvaluator subclass at meso scale, parameters: cost / latency / reliability / dispute_rate) + LLM-classified dispute-resolution suggestions. Phase 6 is the substrate; self-healing iters refine.

---

## 2. 4-Stage Breakdown

### Stage 1a — Stub-factor completion (RTS + OTR + 2-3 others)

- **What's live today:** Apex Capital + Triumph Business Capital SFTP live in `backend/factor_matrix.py`.
- **What this stage adds:** complete the stub integrations for RTS Financial + OTR Capital + 2-3 additional factors based on operator's actual carrier base. Candidates: TBS Factoring, HaulPay, Compass Funding Solutions, Single Point Capital, Riviera Finance, Porter Freight Funding — see Open Question #1.
- Each factor: SFTP-or-API integration (depending on what each provider exposes), credentials in vault entry "Factor — {Name} Credentials", connection health-check registered with `/api/health/system`.
- **Auto-detect which factor a carrier uses** from `db.carriers.factor_provider` field (populated via stage 1b vetting workflow + operator-edit + carrier-self-report) + lane-history fallback (if 70% of past loads with this carrier went to Factor X → assume Factor X for new loads pending operator override).
- Per-factor capability matrix in `db.factors`:
  ```
  {
    factor_id: str (e.g., "apex", "triumph", "rts", "otr", "haulpay"),
    name: str,
    integration_type: "sftp" | "api" | "manual",
    submission_endpoint: str,
    auth_credentials_vault_ref: str,
    typical_funding_latency_hours: int,
    haircut_pct: float (cost factor takes off the top),
    dispute_contact_email: str,
    dispute_contact_phone: str,
    supports_quick_pay: bool,
    quick_pay_haircut_extra_pct: float,
    health_status: "healthy" | "degraded" | "unhealthy",
    last_successful_submission_at: datetime,
  }
  ```

### Stage 1b — Auto-submission pipeline

- **Trigger:** `delivered_load_autofire.py` already fires invoice on `db.loads.dispatch_status: "delivered"` + invoice signed. Hook the real factor-submitter at the existing stub point.
- `auto_submit_to_factor(load_id)`:
  1. Look up load's assigned carrier → `db.carriers.factor_provider`
  2. Look up factor capability in `db.factors`
  3. Generate evidence bundle: rate-con + signed invoice + POD + BOL (already produced by `automations.py` + `load_completion_workflow.py`)
  4. Submit via factor's preferred channel (SFTP file drop / API POST / email-with-attachments fallback)
  5. Write `db.factor_submissions` row with `{load_id, factor_id, submitted_at, evidence_bundle_paths, idempotency_key, submission_status: "pending" | "accepted" | "rejected" | "wire_initiated" | "wire_failed"}`
- **Retry/backoff:** SFTP/API failures retry 3x with exponential backoff (1m / 5m / 30m); on 3rd failure, write `db.failure_events` row + operator-DM
- **Idempotency:** `idempotency_key` = SHA-256 of `(load_id, factor_id, evidence_bundle_sha)`; prevents duplicate submissions on retry
- **Trust-gate posture for v1:** auto-fire on first 10 loads → operator-approve mode, then auto-fire if no operator-rejections in those 10 (see Open Question #2).

### Stage 1c — Reconciliation

- **Combines THREE input streams:**
  1. **Email-ingestion factor-wire-success/failure events** (per iter 143.1 spec Layer 4 hooks) — factor sends "wire confirmed" or "wire failed: BSA flag" email
  2. **Mercury bank-deposit events** (per iter 143.1 spec Layer 4 hooks) — Mercury sends "Deposit of $2,850 received from Triumph Factor" email
  3. **Factor-portal status polls** — for factors with API integration, periodic poll for submission status (every 6h, throttle-respecting)
- `reconcile_invoice(invoice_id)`:
  1. Match incoming wire/deposit to `db.factor_submissions` by amount + memo (factor reference number)
  2. On match → flip `db.invoices.{id}.status: "submitted" → "funded"` + `funded_at: now` + write `db.factor_payment_events` row for latency tracking
  3. On no-match within 7 days → flag as `unreconciled`, surface to operator queue
- **Surfaces unreconciled wires** (orphan deposits — money received but no matching submission) to operator queue
- **Surfaces missing wires** (submission older than `factor.typical_funding_latency_hours * 1.5` with no payment) to operator queue
- **Ties into the 3 E2E_LOAD_LIFECYCLE_AUDIT director-decisions:**
  - **Mercury webhook vs email-ingestion:** stage 1c uses email-ingestion (per iter 143.1 spec); webhook is post-revenue optimization
  - **Auto-payout** (operator-side carrier payment after factor wire confirmed): out-of-scope for 1c (that's iter 144.x stage 1d or separate iter — operator pays carriers via Stripe Connect per existing `stripe_connect.py`); 1c just confirms operator received money from factor
  - **Factor latency alarm:** built into 1c via "missing wires" surface + per-factor latency tracking

### Stage 1d — Dispute / chargeback handling

- **Trigger:** factor flags a load (e.g., "POD missing", "rate dispute", "carrier MC mismatch", "duplicate submission")
- **Tier 1 rule-based classifier** (mirrors email-ingestion spec §3 Tier 1 pattern):
  - `pod_missing` → check `db.loads.{id}.pod_url` + `db.dispatch_packets.{load_id}.bca_url` to verify operator-side has POD; if yes, re-send to factor
  - `rate_dispute` → pull rate-con + invoice; if amounts match, send canned "rates verified, please re-process" reply
  - `carrier_mc_mismatch` → pull `db.loads.{id}.assigned_carrier_mc`; verify against factor record; if mismatch, route to operator (high-stakes; could indicate carrier-MC change mid-haul or data error)
  - `duplicate_submission` → check `db.factor_submissions` idempotency; if confirmed dup, send "please use idempotency key X" reply
- **Tier 2 LLM classifier** (mirrors email-ingestion spec §3 Tier 2): for disputes that fail Tier 1, use gpt-4o via existing `EMERGENT_LLM_KEY` AsyncOpenAI wrapper to classify dispute reason + suggest resolution
- **Trust-gate posture:** disputes are HIGH-stakes (getting wrong = losing factor relationship + cash). v1: all disputes route to operator queue with one-click resolution suggestions; auto-respond ONLY for `pod_missing` + `duplicate_submission` Tier 1 cases (LOW-risk, well-understood). v2 (post-30-day track record): expand auto-respond to additional Tier 1 cases (see Open Question #3).
- **Carrier-side resolution coordination** — connect to `backend/carrier_outreach.py` (iter 142.1 stage 1d) for carrier-side dispute resolution (e.g., "factor flagged your POD as missing; please re-upload" SMS/voice/email). Keeps disputes inside the platform's automation surface.

---

## 3. Per-Stage Cost Estimates + Smoke Battery

### Stage 1a (~6h dev, ~15 min operator)

- **What operator does (~15 min):**
  1. Provision SFTP/API credentials per factor (~10 min for 4 factors; some operator-already-has, some need signup)
  2. Vault entries: "Factor — {RTS|OTR|TBS|HaulPay|...} Credentials" (~5 min)
  3. Sign-off on factor capability matrix defaults (haircut, latency expectations)

- **What dev does (~6h):**
  1. Per-factor SFTP/API client (modeled after Apex + Triumph patterns in `backend/factor_matrix.py`)
  2. `db.factors` collection + capability matrix
  3. Auto-detect logic for `db.carriers.factor_provider` (operator-edit + carrier-self-report + lane-history fallback)
  4. Per-factor health-check registration

- **Smoke battery (5 tests, 2 real-data):**
  1. **REAL-DATA:** Look up real db.carriers entries (~10 sample) → `factor_provider` populated (or null with fallback path)
  2. SFTP connection to RTS sandbox: returns directory listing
  3. Same for OTR sandbox + 2-3 additional factors
  4. Factor capability matrix returns all expected fields per factor
  5. **REAL-DATA:** Health-check registration: `/api/health/system` shows new factor-integration modules (~4 new entries)

### Stage 1b (~5h dev, ~10 min operator)

- **What operator does (~10 min):**
  1. Review evidence-bundle template + idempotency-key format
  2. Sign-off on trust-gate posture: auto-fire after first-10 operator-approved (or override to all-auto-fire if confident)

- **What dev does (~5h):**
  1. `auto_submit_to_factor(load_id)` function
  2. Hook into `delivered_load_autofire.py` at existing stub point
  3. `db.factor_submissions` collection + indexes (compound on `load_id` + `factor_id`)
  4. Retry/backoff + idempotency
  5. Operator-DM on 3rd-retry-failure
  6. SLA target `factor_submit` registered with `target_ms=10_000`

- **Smoke battery (6 tests, 2 real-data):**
  1. **REAL-DATA:** Mocked `delivered` load against real db.carriers entry → submission created in db.factor_submissions
  2. Evidence bundle: rate-con + invoice + POD + BOL all present in submitted package
  3. Idempotency: re-submit same load → no duplicate row
  4. Retry: simulate SFTP failure → retry 3x with exponential backoff → 3rd failure writes `db.failure_events`
  5. **REAL-DATA:** Factor capability matrix lookup for actual carrier in db.carriers → routes to correct factor's submission channel
  6. Trust-gate: first 10 submissions queue for operator-approval; 11th auto-fires (when operator confirms first-10 mode)

### Stage 1c (~5h dev, ~10 min operator)

- **What operator does (~10 min):**
  1. Review reconciliation matching rules (amount tolerance, memo-parse patterns per factor)
  2. Sign-off on operator-queue thresholds (unreconciled-after-7-days, missing-wire-after-1.5x-typical-latency)

- **What dev does (~5h):**
  1. `reconcile_invoice(invoice_id)` function combining 3 input streams (email-ingestion + Mercury + factor-portal poll)
  2. `db.factor_payment_events` collection (latency tracker per iter 145.1 §1 Detection Layer "Gaps")
  3. Wire iter 143.1 Layer 4 hooks: `factor_wire_confirmation` + `mercury_deposit` events trigger reconciliation
  4. Factor-portal status poll cron (6h cadence, throttle-respecting, `@track_sla`)
  5. Operator-queue write: `db.unreconciled_payments` for orphans + `db.missing_wires` for stalled submissions
  6. SLA target `factor_reconcile` registered with `target_ms=2_000`

- **Smoke battery (7 tests, 3 real-data):**
  1. **REAL-DATA:** Mocked Mercury-deposit event for $2,850 with memo "Triumph Factor INV-12345" → matches db.factor_submissions row → invoice flips to `funded`
  2. **REAL-DATA:** Mocked factor-wire-confirmation email → matches submission → invoice flips to `funded`
  3. Factor-portal poll: returns submission status; updates db.factor_submissions accordingly
  4. Orphan deposit (no matching submission within 7 days) → flagged in db.unreconciled_payments
  5. Missing wire (submission older than 1.5x typical latency, no payment) → flagged in db.missing_wires + operator-DM
  6. Latency tracker: db.factor_payment_events row written on funded transition with `latency_hours` calculated
  7. **REAL-DATA:** End-to-end: synthetic delivered load → submit → mocked Mercury deposit → reconcile → invoice funded → latency tracked

### Stage 1d (~6h dev, ~15 min operator)

- **What operator does (~15 min):**
  1. Review Tier 1 dispute rules + canned response templates
  2. Sign-off on auto-respond boundary (default: only `pod_missing` + `duplicate_submission` at v1; expand post-30-day track record per Open Question #3)

- **What dev does (~6h):**
  1. `backend/factor_dispute_classifier.py` — Tier 1 rules + Tier 2 LLM (mirror email-ingestion spec patterns)
  2. `db.factor_disputes` collection: `{dispute_id, load_id, factor_id, dispute_reason, classifier_tier, suggested_resolution, action_status, resolved_at}`
  3. Connection to `carrier_outreach.py` for carrier-side resolution coordination
  4. Operator queue with one-click resolution suggestions
  5. Auto-respond logic for whitelisted Tier 1 cases (LOW-risk only at v1)
  6. SLA target `factor_dispute_classify` registered with `target_ms=5_000`

- **Smoke battery (5 tests, 1 real-data):**
  1. Mocked `pod_missing` dispute → Tier 1 matches → POD verified in db.loads → re-send to factor with POD attached
  2. Mocked `rate_dispute` → Tier 1 matches → operator queue with one-click "rates verified, please re-process" template
  3. Mocked novel dispute reason → Tier 1 fallthrough → Tier 2 LLM classifies → operator queue with LLM-suggested resolution
  4. Auto-respond gate: only `pod_missing` + `duplicate_submission` auto-respond; all others operator-queue
  5. **REAL-DATA:** Carrier-side coordination: dispute fires → mocked SMS to real db.carriers carrier-mc → carrier_outreach.send_dispute_resolution_request fires correctly

---

## 4. Open Questions (operator decision points before iter 144.x opens)

1. **Which 2-3 additional factor providers beyond RTS/OTR?** Top candidates based on freight brokerage market share: TBS Factoring, HaulPay, Compass Funding Solutions, Single Point Capital. Recommendation: pick based on operator's actual carrier base (factor preferences cluster by carrier-segment — small owner-operators favor HaulPay/Compass; mid-size fleets favor TBS/Single Point). Operator should audit the top 20 carriers in db.carriers (post-Phase-1-ingest) for `factor_provider` distribution before picking.

2. **Auto-submission trust-gate posture:** auto-fire on all loads from day-1 vs operator-approve-on-first-N (default: first 10) vs always-operator-approve. Default recommended: first-10-operator-approve, then auto-fire if no operator-rejections in those 10. Pre-revenue-solo posture; promote to all-auto-fire after 30-day clean track record.

3. **Dispute auto-respond boundary:** v1 default is auto-respond ONLY for `pod_missing` + `duplicate_submission` Tier 1 cases; all others operator-queue. Operator should confirm OR expand the auto-respond set (e.g., add `rate_dispute` if rate-con verification is reliable). **Sleeper concern: getting this wrong = losing cash relationships.** Conservative posture is correct for v1.

4. **Multi-factor scenarios:** carrier changes factors mid-haul (e.g., they were with Apex when load was assigned, switched to Triumph before delivery). Detect-and-redirect (auto-submit to new factor) vs queue-as-exception (operator decides). Default recommended: queue-as-exception at v1 (low frequency, high stakes); detect-and-redirect post-30-day-track-record.

5. **Rate negotiation feedback:** factor takes a haircut (e.g., 3% standard, 4% quick-pay). Should factor haircut be visible in dispatch packet pricing (operator-internal) or hidden? Default: hidden at v1 (operator's net-net is what matters; surfacing haircut in dispatch packet creates noise). Could surface in operator-internal margin reports later.

6. **Quick-pay opt-in default:** for factors that support quick-pay (Apex, Triumph, RTS), default opt-in (faster cash, ~1% extra haircut) vs opt-out. Default recommended: opt-in at v1 (cash-flow > marginal cost at pre-revenue scale); operator can override per-load or per-carrier.

7. **Factor-portal poll cadence:** 6h default for factors with API. Tighter (1h) gets faster reconciliation but adds API call cost; looser (24h) reduces cost but delays cash-confirmation. Default 6h is the middle ground; revisit if specific factors charge per-API-call.

---

## 5. Dependencies

**Depends on:**
- **iter 142.1 stage 1c** (CarrierEvaluator) — needed for "which factor each carrier uses" auto-detect logic; CarrierEvaluator's reliability score also informs factor selection (high-reliability carrier = quick-pay default; low-reliability = standard pay)
- **iter 143.1 stages 1d** (email-ingestion factor-wire-success/failure hook + Mercury-deposit hook) — load-bearing for stage 1c reconciliation
- **iter 145.1 Layer 5** (outcome-feedback) — for `FactorEvaluator` v2 (re-tuning factor preference based on actual paid-on-time + dispute rates). v1 ships hard-coded rules; v2 replaces with FractalEvaluator.
- **iter 142.1 stages 1d-1f** (delivered-load + invoice auto-fire pipeline via `delivered_load_autofire.py` + `automations.py`) — Phase 6 hooks at the `delivered → invoice signed → submit-to-factor` boundary

**NOT dependent on (parallel-shippable with):**
- Plivo (factoring is cash-flow event-driven via email-ingestion + bank events; no SMS dependency; Plivo wait does not gate this iter)
- FMCSA broker authority directly (factor relationships are between carrier and factor; CHL's broker authority unblocks broker-side billing only — different concern from factor-side carrier payment closure)
- iter 142.2 Phase 4 GPS Tracking (tracking is in-transit signal; factoring is post-delivery)

**Phase 6 work is fully parallel-shippable with iter 141.3 + iter 142.1 stages 1c-1f development.** This is high-leverage given the FMCSA + Plivo wait — Phase 6 progress doesn't gate on vendor unblocks.

---

## 6. Out of scope (deferred to later iters)

- **Multi-currency factoring** (US-only at v1; international post-revenue)
- **International cargo factoring** (domestic OTR only; international air/ocean post-revenue)
- **Factor-broker direct relationships** (cut out the carrier — different business model; factor pays broker who pays carrier; defer to Phase 9+ when CHL has scale to negotiate this)
- **Real-time factor rate quotes** (manual today; ML-driven Phase 10)
- **Factor portal scraping** for factors without SFTP/API (operator manually exports/uploads at v1; HTML-portal scraping deferred until specific operator-cited need)
- **Multi-tenant factoring** (one CHL = one set of factor relationships at v1; multi-broker SaaS adds per-tenant factor scoping post-Phase 9)
- **Carrier payment auto-fire** (after factor confirms wire, CHL pays carrier via existing `stripe_connect.py`) — this is post-Phase-6 cleanup; could fit in iter 144.x stage 1e or separate iter

---

## 7. Aggregate Stats

| Stage | Operator hands-on | Dev hours | Smoke tests | Real-data tests | North-star (R/O/S) | Parallel-shippable during FMCSA+Plivo wait? |
|---|---|---|---|---|---|---|
| 1a | 15 min | 6h | 5 | 2 | M/M/M | YES (no external blocker; needs operator-supplied factor credentials) |
| 1b | 10 min | 5h | 6 | 2 | H/H/M | YES (depends on 1a + iter 142.1 stage 1c carrier-factor mapping) |
| 1c | 10 min | 5h | 7 | 3 | H/H/H | YES (depends on 1b + iter 143.1 stage 1d email-ingestion hooks) |
| 1d | 15 min | 6h | 5 | 1 | H/M/M | YES (depends on 1b + carrier_outreach module from iter 142.1 stage 1d) |
| **Totals** | **~50 min** | **~22h** | **23** | **8** | — | — |

---

## 8. North-star scoring (per stage)

| Stage | R (removes operator-touch) | O (adds observability) | S (substrate for self-healing) | Notes |
|---|---|---|---|---|
| **1a** | M — operator still picks which factors to integrate; per-factor signup/credential-provision is operator-touch | M — `db.factors` capability matrix surfaces per-factor health | M — substrate for `FactorEvaluator` (iter 145.1+) | Foundational; less operator-touch reduction than later stages |
| **1b** | **H** — auto-fires factor submission on every delivered load; eliminates ~5 min/load operator-touch × 50-200 loads/mo | H — `db.factor_submissions` + retry telemetry + SLA tracking | M — submission-failure modes are patchable (template tunings, retry-backoff tunings) | Highest operator-time-savings stage in iter 144.x |
| **1c** | **H** — auto-reconciles wire/deposit events; eliminates ~5-15 min/load operator-touch reconciliation | **H** — factor-payment-latency tracker is a NEW observation surface for iter 145.1 Detection Layer | **H** — multi-stream reconciliation is rich substrate (orphan-deposit detection, missing-wire detection, latency drift) | Triple-H scoring; highest-leverage stage in iter 144.x |
| **1d** | **H** — auto-classifies + auto-responds-where-safe to disputes; eliminates ~10-30 min/dispute operator-touch | M — `db.factor_disputes` + classifier-confidence telemetry | M — Tier 1 + Tier 2 classifier tunings are patchable; dispute-template tunings are LOW-risk | High operator-touch reduction; observability is contained to dispute lifecycle |

**Aggregate iter 144.x scoring:** **R=HIGH** across the iter (cash-flow loop is the highest operator-touch surface today + Phase 6 collapses it to near-zero on routine flow); **O=HIGH** (factor-payment-latency tracker + reconciliation telemetry + dispute classifier-confidence are net-new observation surfaces); **S=MED→HIGH-over-time** (v1 ships hard-coded rules; iter 145.1+ swaps in FractalEvaluator + LLM-classified disputes).

---

## 9. Cross-references

- `chl-memory/research/iter_142_2_phase_4_tracking_sketch.md` — structural template (mirror for sketch granularity); GPS-tracking sketch's 4-stage pattern matches this 4-stage breakdown
- `chl-memory/research/email_ingestion_architecture_spec.md` (commit `09b744b`) — Layer 4 hooks (`factor_wire_confirmation` + `mercury_deposit` automation hooks) are stage 1c reconciliation inputs
- `chl-memory/research/iter_142_1_stages_1c_1f_refresh.md` (commit `0f9ccca`) — CarrierEvaluator pattern referenced for `FactorEvaluator` v2 design (FractalEvaluator subclass at meso scale; parameters cost/latency/reliability/dispute_rate)
- `chl-memory/research/phase_6_self_healing_roadmap.md` (commit `7d658cc`) — Layer 5 outcome-feedback wires factor-paid-on-time outcomes into `FactorEvaluator` v2 re-tuning. **Naming-collision warning:** that doc is "iter 145.1 Phase 6+" (self-healing); THIS doc is "iter 144.x Phase 6" (factoring). Different scopes.
- `chl-memory/CHL_STRATEGIC_PLAYBOOK.md` Phase 6 boundary — authoritative phase definition ("Cash flow happens autonomously")
- `chl-memory/E2E_LOAD_LIFECYCLE_AUDIT.md` — 3 director-decisions on Mercury webhook + auto-payout + factor latency alarm tied into stage 1c reconciliation design (resolution: webhook is post-revenue optimization → use email-ingestion at v1; auto-payout is post-Phase-6 cleanup → could fit stage 1e or separate iter; factor latency alarm is built into stage 1c via missing-wires surface + per-factor latency tracker)
- CHL backend `factor_matrix.py` — Apex + Triumph SFTP live patterns to mirror for new factor integrations
- CHL backend `automations.py` — invoice + rate-con generation (evidence-bundle source)
- CHL backend `delivered_load_autofire.py` — invoice auto-fire trigger (Phase 6 hooks at the existing stub point)
- CHL backend `shipper_credit_headroom.py` — HaulPay-stub for shipper credit limit; pairs with factor selection (a load with shipper-credit-saturation can't use credit-protection-style factors)
- CHL backend `broker_invoice.py` — broker invoice + NOA module (Notice of Assignment is part of evidence bundle for some factors)
- CHL backend `stripe_connect.py` — Express onboarding + instant ACH (operator-side carrier payment; out-of-scope for iter 144.x stages 1a-1d but referenced for stage-1e/separate-iter "carrier payment auto-fire" follow-up)

---

## 10. Probable smoke battery + sequencing recommendation

**Aggregate (post-iter-144.x ship):** **23 smoke tests** (5+6+7+5) with **8 real-data assertions** against `db.carriers` (post-Phase-1-ingest 480K-row roster) + `db.invoices` + `db.factor_submissions` + `db.factor_payment_events`. Operator hands-on: ~50 min across iter; dev hours: ~22h.

**Sequencing recommendation:**
1. **First:** ship stage 1a (zero blockers; needs operator-supplied factor credentials only)
2. **Next:** ship stage 1b (depends on 1a + iter 142.1 stage 1c carrier-factor mapping)
3. **After iter 143.1 stage 1d ships** (email-ingestion hooks live): ship stage 1c reconciliation
4. **In parallel with 1c (or after 1b):** ship stage 1d dispute handling (depends on 1b + iter 142.1 stage 1d carrier_outreach module)
5. **Iter 144.x close ceremony:** when all 4 stages green; cumulative 23-smoke battery + real-data assertions across the cash-flow loop

**Iter 144.x milestone met when:** real delivered load goes from `delivered` → invoice → factor submission → factor wire confirmed → `db.invoices.{id}.status: "funded"` without operator intervention on at least one real load.

---

**Status:** ✅ SKETCH COMPLETE. Operator/dev review before iter 144.x opens. Full agenda by @pm-lead post-iter-142.1-stages-1c-1f-ship + post-iter-143.1-stage-1d-ship (the two hard dependencies). Iter 144.x is **fully parallel-shippable with FMCSA + Plivo wait** — high-leverage given those are the current critical-path blockers.

**Source:** Drafted by @pm-lead 2026-05-07 EOD-3 per @dev-engineer task brief. North-star pillars: operator's "autonomous + self-healing + learning + AI-embedded" directive. Cash-flow-loop closure is the highest operator-touch reduction in the post-launch revenue path. Forward-looking only; specific factor SFTP/API signatures + per-factor capability nuances will adjust at dev-time per actual factor-portal documentation.
