# Capability Inventory — Code-Derived

**Generated:** 2026-05-06 (post iter 139.46 close)
**Source:** Direct read of `C:\CHL\backend\` only.

Three buckets: **BUILT** (production-quality, exercised in code path), **PARTIAL** (scaffolded but missing live wiring or credentials), **MISSING** (referenced in docs/comments but no implementation found).

---

## BUILT (production-quality)

### Core load lifecycle

- ✅ **RFQ inbound parsing** — `rfq_inbound.py`. Three entry paths (Resend webhook, Zoho IMAP poller, manual paste). gpt-4o vision parses unstructured email + PDF (3-page cap, 150 DPI JPEG conversion via pymupdf). Auto-creates `rfq_inbound`, `quotes_inbound`, `shipper_profiles` rows.
- ✅ **AI-drafted quote generation** — `auto_quote_draft.py`. Per-lane drafts via `_suggest_rate()` (heuristic + AI margin recommendation if sample size ≥5). Review-and-send by operator.
- ✅ **Quote → load conversion** — `auto_load_pipeline.py`. Quote acceptance auto-creates load + posts to external boards.
- ✅ **Auto-dispatch with hard gates** — `auto_dispatch.py:try_auto_accept()`. 8 hard rules (vetting, MC, margin floor, rate cap, etc.) with full audit trail in `auto_accept_log`.
- ✅ **Manual dispatch + state machine** — `PATCH /api/loads/{id}/status` (available → assigned → pickup → in_transit → delivery → completed).
- ✅ **Rate-con + BOL generation** — `automations.py`. Reportlab-based PDF generation, Resend email + Gmail SMTP fallback.
- ✅ **POD upload + OCR** — `load_completion_workflow.py`. Image quality gate, gpt-4o vision OCR (signature detection, red-flag extraction), 3-doc support (POD/lumper/detention).
- ✅ **Detention calculation** — `billing_automation.py:_compute_detention()`. Geofence-event-based dwell time vs rate-con free_time_hours.
- ✅ **Variance detection** — actual events vs rate-con line items, drops `Required Task Type E`.
- ✅ **Carrier invoice + Stripe Checkout payouts** — `automations.py` + `stripe_connect.py`. Express onboarding, instant ACH.
- ✅ **Billing packet assembly** — invoice + rate-con + BOL + POD + GPS trail bundled for factor submission.
- ✅ **Aged AR + collections** — 30/60/90+ buckets, demand-letter auto-generation.

### Monitoring & self-healing

- ✅ **Failure detectors (12 modes)** — `failure_detectors.py` + `failure_detectors_part2.py`. GPS-silent, no-show, factor-decline, BOL-mismatch, detention overage, ghosting, double-broker, bankruptcy, lumper shortage, reweigh, cargo-claim, authority-lost. SMS/email alerts.
- ✅ **Circuit breaker** — `circuit_breaker.py`. Auto-trip on failure rate, auto-reset on success streak.
- ✅ **Auto-repost scheduler** — rate ratchet per margin-erosion schedule.
- ✅ **BMC-84 watcher** — auto-clears FMCSA "PENDING" banner.
- ✅ **Quote recovery loop** — hourly recovery emails for stale quotes.
- ✅ **Silent shipper ping** — 24h follow-up on no-booking RFQs.
- ✅ **Synthetic auto-wipe** — test data 24h TTL.

### Operator dashboards & control

- ✅ **System state snapshot** — `/api/system-state` (30s cached, 9 gates + warnings).
- ✅ **Boot briefing** — `/api/admin/boot-briefing` (Anthropic 30-second narrative).
- ✅ **Live-wire daily diary** — TTS via OpenAI tts-1, voice "onyx", SMS + email.
- ✅ **Load dossier** — unified panel (load + shipper + carrier + driver + docs + notes).
- ✅ **Manual coverage** — parses `OPERATING_MANUAL.md` tables → dashboard ✅/⚠️/🚧.
- ✅ **Day-1 readiness digest** — Sunday 18:00 CT email with all 8 gates.
- ✅ **Manual override** — universal endpoint for force-accept, waive-margin-floor, force-carrier-pay.
- ✅ **Synthetic load spawning** — fake-load-at-stage testing without live setup.
- ✅ **Audit trail** — `auto_accept_log`, `circuit_trip_log`, `repost_pulses`, `billing_dispatches`.

### External integrations (live)

- ✅ **OpenAI direct** — gpt-4o (parsing/OCR/voice agent), tts-1 (diary). Post-iter 139.46: zero `emergentintegrations` proxy usage in active code.
- ✅ **Anthropic direct** — boot_briefing.py uses AsyncAnthropic.
- ✅ **Twilio voice + SMS** — browser softphone, carrier check-in SMS, voice agent webhook.
- ✅ **Resend email** — inbound webhook + outbound (carrier/shipper invoices, status updates).
- ✅ **Stripe Connect** — Express onboarding, ACH payouts (Stripe Checkout).
- ✅ **FMCSA SAFER** — carrier vetting, BMC-84 polling, authority watch.
- ✅ **Mercury Bank** — read-only balance + transaction history.
- ✅ **Zoho IMAP** — RFQ inbox polling.

---

## PARTIAL (scaffolded, awaiting credentials or wiring)

### Factor integrations

- ⚠️ **Apex factor adapter** — live SFTP submission built, may need final tuning.
- ⚠️ **Triumph factor adapter** — live SFTP submission built.
- 🚧 **RTS Financial** — stub awaiting user agreement signing
- 🚧 **OTR Capital** — stub awaiting user agreement signing
- 🚧 **Other factors** — adapter scaffold ready (`factor_api_integration.py:31-36`); each factor needs API credentials post-agreement.
- 🚧 **`factor_matrix.py:978`** — submission engine "placeholder, no live SFTP yet" for non-Apex/Triumph factors.

### Credit checks

- 🚧 **Ansonia/CreditSafe credit check hook** — `factor_matrix.py:1328` returns `"needs_integration"` stub. UI exists, real credit pull awaits API token.
- 🚧 **HaulPay credit limit endpoint** — `shipper_credit_headroom.py:9248` "Stub until HaulPay API token arrives". Returns static defaults.

### Loadboards

- 🚧 **DAT** — adapter pattern ready, real credentials await.
- 🚧 **Truckstop** — same.
- 🚧 **Sylectus** — same.
- 🚧 **123Loadboard** — same.
- 🚧 **DirectFreight** — same.

Reference: `loadboards.py:332-404`. The adapter framework is well-designed; once credentials are obtained, swapping in is mostly config.

### OCR for non-POD docs

- 🚧 **Lumper receipt OCR** — `_ocr_scan_placeholder` legacy function. POD OCR is real (gpt-4o vision); lumper/detention proof still pattern-matches against keywords without full OCR.

### BCA (Broker Carrier Agreement) signing portal

- 🚧 **`carrier_vetting_completion.py:25`** — "future signing portal (placeholder for now)". BCA flow is currently manual email → download → sign → re-upload.

### Pre-Authority Mode

- ⚠️ **`pre_authority.py`** — restricts booking to existing shipper base during FMCSA protest window. Built but requires manual director gate (no automated flip).

### ELD / HOS compliance

- 🚧 **`eld_integration.py`** — exists but functionality unclear from grep. Likely stub for HOS compliance tracking.

### Auto-billing escalation

- 🚧 **Aged shipper AR auto-escalation** — buckets exist (30/60/90), email open-tracking + payment-link click-tracking via pixel exists, but no auto-fire of collections packet. Operator-triggered only.

### Compliance data flow

- 🚧 **`compliance.py`** — placeholder QR-code carrier emails (`qr_{user_id}@placeholder.local`) suggest incomplete data flow. Compliance docs may not all auto-route.

### Multimodal LLM features

- 🚧 **`multimodal.py` / `multimodal_override.py`** — exist but unclear scope. Likely experimental, not in main flow.

### Background load deduplication

- 🚧 **`load_dedup.py`** — exists but no API routes registered. Background dedup logic exists but not surfaced to dispatcher UI.

### Stripe Connect edge cases

- ⚠️ **`stripe_connect.py`** — Express onboarding live, but ACH payout failure handling and re-try logic may have edge cases not yet tested at scale.

---

## MISSING (referenced but no implementation)

### Load auto-advance from GPS

- ❌ **GPS-derived status auto-advance** — geofence events captured (`load_events`), but `loads.status` field is operator-driven via `PATCH .../status`. Closing this loop (geofence → status flip) would automate Stage 4 substantially. Code framework supports it; logic gap.

### AI-suggested overrides

- ❌ **AI suggestion for margin override** — `manual-override/waive-margin-floor` is a passthrough; no AI proposes "this load is borderline, consider waiving floor."
- ❌ **AI suggestion for carrier selection** — `auto_dispatch` is hard-rule-based. No ML ranking of carriers by fit (rate history, reliability score, equipment match).

### Auto-retry on factor decline

- ❌ **Multi-factor fallback** — when one factor declines, system blocks invoice + Required Task. No auto-attempt with backup factor.

### Quote auto-send for trusted shippers

- ❌ **Auto-send draft quotes** — drafts are review-and-send only. A trust-tier flag on `shipper_profiles` could enable auto-send for top-tier shippers, but no implementation.

### Driver-app integration

- ❌ **Native driver app** — POD upload is via magic link / public token URL. No driver app for GPS pings, electronic BOL signature, or in-app messaging.

### Rate-con e-signature

- ❌ **Carrier rate-con e-sign** — Rate-con PDF is generated and emailed; carrier prints + signs + scans + emails back. No DocuSign/HelloSign integration.

### Shipper rate-quote AI auto-counter

- ❌ **Auto-counter shipper pushback** — when shipper rejects a quote, no AI-suggested counter-offer. Operator must manually re-quote.

### Multi-leg / partial-load support

- ❌ **Multi-stop or LTL** — load model assumes single-leg full truckload. Multi-leg (drop-and-hook, multi-stop) and LTL (multiple shippers per truck) not visible in code.

### Insurance proof verification

- ❌ **Auto-verify carrier insurance** — FMCSA SAFER pulls some insurance data, but no programmatic check that COI is current at dispatch time. Manual check by operator.

### Fuel surcharge calculation

- ❌ **Auto fuel surcharge** — quotes are flat-rate. No auto-FSC calculation based on DOE diesel index.

### Refund / claim workflow

- ❌ **Cargo claim auto-workflow** — failure detector flags claims (OCR detects "damage"), but no auto-workflow to issue refund, file insurance claim, or notify shipper of resolution timeline. Operator-driven.

---

## Playbook phase mapping

Each playbook phase maps to a coarse-grained group of capabilities. Below is current state per phase, with capabilities flagged ✅/⚠️/🚧/❌.

### Phase 0 — Foundation [✅ DONE iter 139.45]
- ✅ emergentintegrations.LlmChat removal across `boot_briefing.py`, `rfq_concierge.py`, `rfq_inbound.py`, `live_wire_diary.py`
- ✅ RFQ vision parsing via direct AsyncOpenAI gpt-4o
- ✅ PDF preprocessing (pymupdf, 3-page cap, 150 DPI JPEG)
- ✅ Bridge architecture (chl-memory agenda + progress + raw URL fetches)

### Phase 1 — EMERGENT_EXIT_PLAN Closure [✅ DONE iter 139.46]
- ✅ `automations.py`: 2 LLM functions migrated to AsyncOpenAI
- ✅ `load_completion_workflow.py`: POD OCR migrated to gpt-4o vision (model swap from gemini-2.0-flash)
- ✅ Dead constants removed (autopilot_phase1.py, server.py, rfq_inbound.py:147 fallback)
- ⚠️ Stripe `emergentintegrations.payments.*` import remains at `server.py:16` (out of scope, separate Phase TBD)

### Phase 2 — Load Discovery & Evaluation [🚧 PLANNED iters 140.x]
- ✅ **RFQ inbound parsing** — Resend webhook + IMAP poller + manual paste-in (PARTIAL coverage of "Load Discovery"; this is RFQ-side only, not load-board-side)
- ⚠️ **Margin calculation engine** — `_suggest_rate()` heuristic + `recommend_margin()` AI when sample size ≥5; could expand
- 🚧 **Load board API adapters** — DAT, Truckstop, 123Loadboard, Sylectus, DirectFreight: scaffold ready (`loadboards.py:332-404`), credentials needed
- ❌ **Load board polling/monitoring loop** — no active scanning of boards for shipper-posted loads
- ❌ **AI opportunity ranking** — no ML scoring of load profitability vs lane history
- ❌ **Auto-bid on load boards** — no programmatic bidding to win loads
- ✅ **Margin floor + warning thresholds** (`margin_floor_router.py`)
- ⚠️ **Auto-accept rules engine** (`auto_dispatch.py`) — exists but limited to RFQ-derived loads, not load-board-derived

### Phase 3 — Carrier Network & Dispatch [🚧 PLANNED iters 141.x]
- ⚠️ **Carrier database** — `carriers` collection exists, vetting flags exist (`vetted_trusted`, `mc_verified`); rate-history per carrier scaffolded but reliability score is missing
- ⚠️ **Carrier matching** — rule-based hard gates in `auto_dispatch.try_auto_accept()` (8 gates); no AI ranking by fit
- ✅ **Dispatch packet generation** — rate-con PDF + email automation live
- ⚠️ **Compliance verification** — FMCSA SAFER pulls authority, BMC-84 polling works; insurance COI auto-verification is not in code
- ❌ **Multi-carrier simultaneous outreach** ("offer to 3 carriers, first accept wins") — playbook bottleneck mitigation, not implemented
- ❌ **Backup carrier network with pre-negotiated rates** — not in code
- ✅ **Manual override** for any carrier decision

### Phase 4 — Tracking & Exception Handling [⚠️ PARTIAL iters 142.x]
- ✅ **12 active failure detectors** (GPS-silent, no-show, ghosting, factor-decline, BOL-mismatch, detention, double-broker, bankruptcy, lumper, reweigh, cargo-claim, authority-lost)
- ✅ **Dock geofence event capture** (`load_events`)
- ✅ **SMS check-in scheduler** (Twilio)
- ❌ **Real-time GPS integration** with carrier ELDs (Samsara, Geotab, KeepTruckin/Motive) — not in code
- ❌ **GPS-derived auto-advance** of `loads.status` — geofence events captured but operator-driven status flips
- ⚠️ **Automated carrier communication** — SMS check-ins live; no AI-driven ETA renegotiation
- ✅ **Manual operator intervention path** for all anomalies (`/api/manual-override/*`)

### Phase 5 — Completion & Invoicing [⚠️ PARTIAL iters 143.x]
- ✅ **POD photo capture** via driver magic link
- ✅ **AI POD verification** — `gpt-4o` vision detects signature, BOL-vs-not-BOL, red flags
- ✅ **Image quality gate** (`validate_pod_image_quality`)
- ⚠️ **Lumper/detention proof OCR** — pattern-match only, not real OCR (legacy `_ocr_scan_placeholder`)
- ✅ **Auto-fire on `delivered` status** (`delivered_load_autofire.py`) — drafts broker invoice + NOA
- ✅ **Invoice generation** from load contract
- ❌ **Shipper portal auto-submit** — invoices emailed via Resend; no portal-API integrations (Convoy, Loadsmart, etc.)
- ✅ **Operator review-and-send** before invoice send
- ✅ **Variance detection** (actual events vs rate-con) → `Required Task Type E`

### Phase 6 — Factoring & Settlement [⚠️ PARTIAL iters 144.x]
- ✅ **Apex factor adapter** — live SFTP submission
- ✅ **Triumph factor adapter** — live SFTP submission
- 🚧 **RTS Financial, OTR Capital, others** — stubs awaiting user agreement signing
- 🚧 **Same-day funding tracking** — partial (`factor_submissions` collection exists)
- ❌ **Auto-retry on factor decline with backup factor**
- 🚧 **Direct shipper billing fast-track** for high-credit shippers — `shipper_credit_headroom.py:9248` stub awaiting HaulPay token
- ✅ **Carrier payment automation** — Stripe Checkout for instant ACH payouts
- ❌ **Margin reconciliation report at settlement** — billing summary exists but no per-carrier margin variance roll-up

### Phase 7 — Monitoring & Self-Healing [❌ PLANNED iters 145.x]
- ❌ **Health check framework** — no `/health` endpoints reporting per-module status every 60s
- ⚠️ **Error detection** — distributed across modules (`failure_detectors.py`, log warnings); not categorized into structured taxonomy
- ⚠️ **Auto-remediation primitives exist** — retry-with-backoff in OpenAI/Anthropic SDKs, fallback in `boot_briefing.py`, `circuit_breaker.py` for dispatch
- ❌ **Autonomous Throttle System (GREEN/YELLOW/ORANGE/RED)** — not implemented; only binary circuit-breaker exists
- ❌ **Auto-recovery from throttle states** — not applicable (no throttle states)
- ⚠️ **Anomaly detection** — pattern-match in `failure_detectors.py`; no ML-based anomaly model
- ✅ **SMS/email escalation** to operator on critical events

### Phase 8 — Scale & Optimization [❌ PLANNED iters 146.x+]
- ❌ **Performance tuning** for >100 loads/day — current scale is much lower
- ❌ **Database optimization** — single MongoDB instance, no sharding/read replicas
- ❌ **LLM token cost reduction** — no caching of common shipper extractions or rate lookups
- ⚠️ **Parallel processing** — async/await is in code but no queue-based parallelism for batch RFQ processing

### Phase 9 — Edge Cases & Compliance [⚠️ PARTIAL iters 147.x+]
- ⚠️ **Specialized load handling** — load model is single-leg full-truckload; HAZMAT/oversize/refrigerated handling unclear from code
- ✅ **FMCSA broker authority verification** (`bmc84_watcher.py`)
- ⚠️ **Insurance verification** — FMCSA pulls some data but no programmatic COI currency check at dispatch time
- ✅ **Failure detector for cargo claim** — OCR detects "damage" keyword
- ❌ **Cargo claim auto-workflow** — no auto-issue refund / file insurance claim / shipper notification timeline
- ❌ **Dispute resolution workflow** — collections packet exists; no formal dispute tracking
- ✅ **Pre-Authority Mode** (`pre_authority.py`) — restricts booking during FMCSA protest window

### Phase 10 — Continuous Improvement [❌ PLANNED ongoing]
- ❌ **A/B testing framework** — not in code
- ❌ **ML model training** for load profitability prediction or carrier reliability — not in code
- ❌ **Competitive intelligence** — no market rate tracking or lane analysis dashboards
- ⚠️ **`rate_signals` collection** — captures margin outcomes for future AI training; framework ready

---

## Notable shape observations

1. **Architecture: monolith + modular automation files.** `server.py` is a large monolith (protected — only single-line surgical edits per protocol). Surrounding it are ~30 single-purpose modules (each ~200-1500 LOC) that register routers or run as background loops. This is a healthy split — the monolith handles routing/auth/middleware while modules own domain logic.

2. **Migration completeness.** Iter 139.42-139.46 closed the EMERGENT_EXIT_PLAN: 0 active `EMERGENT_LLM_KEY` references, 0 `emergentagent.com` calls. Only `emergentintegrations.payments.stripe.checkout` import remains at `server.py:16` (Stripe carve-out, separate effort).

3. **Auth model.** Three tiers: public (no auth), broker (dispatcher), owner (full admin). Carriers and drivers have separate auth tiers via magic links.

4. **Synthetic test data.** First-class — `synthetic_load_spawn` + `synthetic_audit` + `synthetic_auto_wipe` ensure test data never bleeds into prod metrics. Good engineering hygiene.

5. **Audit-trail pattern is strong.** `auto_accept_log`, `circuit_trip_log`, `repost_pulses`, `billing_dispatches`, `manual_overrides` all maintain who/what/why per event.

6. **Failure-detector taxonomy is comprehensive.** 12 modes cover most freight-broker pain points. Missing: weather/route disruption, equipment breakdown reroute, customer credit hold mid-load.

7. **Cron-based execution.** Mostly fine for current scale. As load volume grows, may want to migrate to event-driven (queue + workers) for better backpressure handling.

---

DONE
