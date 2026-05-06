# Operations SOP — Code-Derived

**Generated:** 2026-05-06 (post iter 139.46 close)
**Source:** Direct read of `C:\CHL\backend\` only. NOT cross-referenced against `OPERATING_MANUAL.md`, `PRD.md`, `E2E_LOAD_LIFECYCLE_AUDIT.md`, etc. — intentional, so PM Claude can diff this against those for drift.

This document describes the load lifecycle **as actually implemented in code**. Every route, automation, and decision point listed below has a real code reference. If a step appears here but isn't in `OPERATING_MANUAL.md`, the manual is stale. If a step is in the manual but not here, the code doesn't (yet) implement it.

---

## Lifecycle stages — overview

| Stage | Name | Trigger | Primary module(s) | Key collection writes |
|---|---|---|---|---|
| 1 | RFQ intake | Inbound email (Resend webhook) OR IMAP poller OR manual paste-in | `rfq_inbound.py`, `rfq_imap_poller.py` | `rfq_inbound`, `quotes_inbound`, `shipper_profiles` |
| 2 | Quote / decision | Operator triggers AI draft per parsed RFQ | `auto_quote_draft.py`, `rfq_inbound.py` | `quotes_inbound.auto_quote_draft` |
| 3 | Dispatch / assignment | Quote accepted (auto or manual) → load created → carrier matched | `auto_load_pipeline.py`, `auto_dispatch.py`, `automations.py` (rate-con) | `loads`, `auto_accept_log`, `rate_confirmations` |
| 4 | Tracking | GPS pings, dock events, SMS check-ins | `automations.py` (check-in), `failure_detectors*.py` | `load_events`, `loads.status` updates |
| 5 | Completion (POD/BOL) | Driver uploads POD via magic link | `load_completion_workflow.py` | `pod_uploads`, `load_documents` |
| 6 | Billing | Load status → "delivered" auto-fires invoice draft | `delivered_load_autofire.py`, `billing_automation.py`, `automations.py` (invoice) | `invoices`, `carrier_payments`, `billing_dispatches` |

---

## Stage 1 — RFQ intake

**Three entry paths:**

1. **Resend Inbound webhook** → `POST /api/rfq-inbound/webhook` (public, signature-verified via `RESEND_INBOUND_SECRET`)
2. **Zoho IMAP poller** → cron every 5 min watches `quotes@continentalhaul.com` inbox; runs unread emails through parser; persists to `db.rfq_inbound`. Drops `Required Task Type F` so dispatcher reviews.
3. **Manual paste-in** → `POST /api/rfq-inbound/parse-text` (broker auth)

**Parser:** `_parse_with_ai` in `rfq_inbound.py:127-225` — direct AsyncOpenAI SDK with `gpt-4o` vision. PDFs converted to JPEG (first 3 pages, 150 DPI) via pymupdf as of iter 139.45 stage 5a. Returns `_RFQExtract` with `parser_model="openai/gpt-4o"` (synced in iter 139.44 stage 4a).

**Auto-creates:**
- `rfq_inbound` row (raw email + extracted lanes)
- `quotes_inbound` row per lane (origin/destination/equipment/weight/pickup_date)
- `shipper_profiles` row if shipper unseen

**Operator touchpoint:** `GET /api/rfq-inbound/recent` returns last 50 parsed RFQs for review.

---

## Stage 2 — Quote / decision

**Manual trigger:** `POST /api/rfq-inbound/{rfq_id}/draft-quote` runs `auto_quote_draft.py:_suggest_rate()` per lane.

**Rate logic** (heuristic rule-based):
1. If parser extracted `estimated_rate_usd` → trust it
2. Else look up `$/mi` table per equipment type (dry van, reefer, flatbed)
3. Fallback: assume 800 miles if origin/destination not parseable
4. Apply `MARGIN_FLOOR_PCT` (env, default 15%) as floor; `MARGIN_WARN_PCT` for soft warnings
5. AI-driven margin recommendation via `autopilot_phase1.py:recommend_margin()` if ≥5 historical accepted/rejected bids per lane

**Operator workflow:**
1. `GET /api/rfq-inbound/{rfq_id}/drafts` — review AI-drafted quote emails
2. `POST /api/rfq-inbound/quote/{quote_id}/regenerate-draft` — re-roll if unsatisfied
3. Operator manually sends to shipper (no auto-send; review-and-send pattern)

**Self-serve path:** `POST /api/public/quote/instant` — shipper-facing instant quote (no auth), `GET /api/public/quote/{quote_id}` — quote recovery deep link, `POST /api/public/quote/book` — convert quote → load.

---

## Stage 3 — Dispatch / assignment

**Two paths:**

### A. Auto-dispatch (`auto_dispatch.py:try_auto_accept()`)

Triggered by `POST /api/carrier/loads/{id}/interest` (carrier auth).

**Hard gates** (all must pass):
- Master toggle `auto_accept_master_enabled = True` (owner-flipped)
- Carrier `vetted_trusted = True`
- Carrier MC verified
- Carrier onboarding complete
- No blocklist entries
- Margin ≥ `margin_floor_pct`
- Carrier rate ≤ `max_auto_rate`
- No load risk flags

If all pass → load auto-booked, `auto_accept_log` row written. Else dispatcher must intervene.

### B. Manual dispatch

`POST /api/loads` (create) and `PATCH /api/loads/{id}/status` (advance state machine: available → assigned → pickup → in_transit → delivery → completed).

**Rate confirmation:** `POST /api/automations/rate-con/{load_id}` generates + emails signed Rate Confirmation PDF (Resend / Gmail SMTP fallback).

**Auto-repost:** `auto_repost_scheduler.py` cron every 30 min scans `available` loads >2h old, ratchets shipper rate per margin-erosion schedule (1st 30min +0%, then +5%, +10%, +15%, capped), re-fires `match_and_notify`.

**External posting:** `external_post_pipeline.py` posts to DAT/Truckstop/123LB/DirectFreight when adapters configured (most are stubs awaiting credentials).

---

## Stage 4 — Tracking

**Active monitoring loops** (background tasks running continuously):

| Loop | Triggers on | Action |
|---|---|---|
| `failure_detectors.py:gps_silent_watcher` | Load `in_transit` + no GPS ping >4h | SMS alert to owner |
| `failure_detectors.py:no_show_watcher` | ETA + 60min passed, status still `in_transit` | SMS alert |
| `failure_detectors_part2.py:ghosting_watcher` | Carrier stopped responding to SMS check-ins | Re-fires auto-dispatch |
| `bmc84_watcher.py` | FMCSA authority/bond status flip | Updates `business_settings`, alerts owner |
| `circuit_breaker.py` | Failure rate > threshold (e.g., 10%) | Trips circuit → human-only mode for cooldown_sec |

**Manual:**
- `GET /api/load-dossier/{id}` — unified panel (load + shipper + carrier + driver phone + all docs + notes timeline with author attribution)
- `POST /api/billing/loads/{id}/events` — record dock arrival/departure with GPS geofence
- `POST /api/automations/checkin/trigger` — schedule SMS check-in at pickup/in_transit/delivery

---

## Stage 5 — Completion (POD/BOL)

**Driver upload flow:**
- Magic link or public token URL → `POST /api/load-completion/pod-upload/{load_id}` (driver/public auth)
- Three doc types accepted: primary POD (REQUIRED), lumper receipt (CONDITIONAL), detention proof (OPTIONAL)
- Image quality gate `validate_pod_image_quality()` (brightness/darkness/blank/overexposed) rejects bad images BEFORE OCR call

**OCR:** `run_pod_ocr_via_gemini()` in `load_completion_workflow.py:273` — function name kept for stability but actually runs `gpt-4o` vision (post iter 139.46 stage 6b model swap from `gemini-2.0-flash`). Returns:
- `signature_detected` (bool)
- `is_actual_bol` (bool — rejects non-BOL images like dashboard photos)
- `extracted_text` (≤4000 chars)
- `red_flags` (cross-referenced against `OCR_RED_FLAG_KEYWORDS`: damage/short/refused/etc.)

**Failure detectors triggered post-upload:**
- BOL signature mismatch
- Lumper shortage (OCR detects "short")
- Cargo claim (OCR detects "damage")
- Reweigh variance (`weight_actual != weight_lbs`)

**BOL generation:** `POST /api/automations/bol/{load_id}` generates + emails signed BOL PDF.

---

## Stage 6 — Billing

**Auto-fire on `delivered`:** `delivered_load_autofire.py` triggers when `PATCH /api/loads/{id}/status` flips to `delivered`. Auto-drafts:
1. Broker invoice (shipper-facing)
2. Notice of Assignment (NOA, factor-facing)

Operator clicks Send (no auto-send).

**Detention calculation:** `billing_automation.py:_compute_detention()` — rule-based, compares dock dwell time (geofence events) vs rate-con `free_time_hours`, calculates billable increments.

**Variance detection:** `GET /api/billing/loads/{id}/variance` — diff actual events vs rate-con line items; drops `Required Task Type E` if mismatch detected.

**Carrier invoice + payment link:** `POST /api/automations/invoice/{pending_payment_id}` — Stripe Checkout link for carrier payouts.

**Billing packet assembly:** `POST /api/billing/loads/{id}/build-packets` — assembles factoring + evidence packet (broker invoice + signed rate-con + BOL + POD + GPS trail).

**Aged AR:** `GET /api/aged-ar/buckets` — 30/60/90+ day buckets per shipper.

**Collections:** `POST /api/collections-packets/{load_id}` — auto-generate demand letter + cargo-claim packet on dispute.

---

## Decision points (where the system chooses)

| Where | Type | Logic |
|---|---|---|
| `_parse_with_ai` (Stage 1) | AI | gpt-4o vision parses unstructured RFQ |
| `_suggest_rate` (Stage 2) | Heuristic | Trust parser estimate or apply $/mi lookup |
| `recommend_margin` (Stage 2) | AI | Historical-bid-based margin if sample size ≥5 |
| `try_auto_accept` (Stage 3) | Rule | 8-gate hard ruleset (vetting, MC, margin, rate cap) |
| `auto_repost_scheduler` (Stage 3) | Rule | Rate ratchet per margin-erosion schedule |
| `validate_pod_image_quality` (Stage 5) | Heuristic | Brightness/blank/overexposed rejection |
| `run_pod_ocr_via_gemini` (Stage 5) | AI | gpt-4o vision OCR + red-flag extraction |
| `_compute_detention` (Stage 6) | Rule | Dwell time vs free_time_hours |
| `circuit_breaker` (cross-stage) | Rule | Failure-rate threshold trips fallback mode |
| `factor_refusal_guard` (Stage 6) | Rule | Proof + credit + AR headroom checks before invoice send |

---

## Operator touchpoints — exhaustive list

These are points where the system explicitly requires or accepts human intervention:

**Per-load:**
- Manual RFQ paste (`/api/rfq-inbound/parse-text`)
- AI draft review + regenerate + send (`/api/rfq-inbound/{rfq_id}/drafts`, `/regenerate-draft`)
- Manual load creation/status advance (`/api/loads`, `PATCH .../status`)
- Rate-con generation (`/api/automations/rate-con/{load_id}`)
- BOL generation (`/api/automations/bol/{load_id}`)
- Carrier invoice (`/api/automations/invoice/{pending_payment_id}`)
- Billing packet assembly (`/api/billing/loads/{id}/build-packets`)
- Collections packet (`/api/collections-packets/{load_id}`)
- SMS check-in trigger (`/api/automations/checkin/trigger`)
- Manual override (`/api/manual-override/{operation}`) — universal endpoint for force-accept, waive-margin-floor, force-carrier-pay, force-status

**System-level:**
- Auto-dispatch master toggle (`/api/auto-accept/config`)
- System-state snapshot (`/api/system-state` — 30s cached)
- Boot briefing (`/api/admin/boot-briefing` — Anthropic-backed 30sec narrative of last 24h)
- Load dossier search (`/api/load-dossier/search` and `/{id}`)
- Aged AR view (`/api/aged-ar/buckets`)
- Failure detector status (`/api/admin/failure-detector/status`)
- Manual coverage update (`/api/admin/manual-coverage` — parses `OPERATING_MANUAL.md`)
- Synthetic load spawn for QA (`/api/admin/synthetic-load/spawn`)
- Live-wire diary trigger (`/api/diary/run-now`)

---

## Automation gaps (where the system still requires humans)

1. **Quote send is review-and-send** — AI drafts but doesn't auto-send. By design (per current code), but a future iter could auto-send for trusted shippers.
2. **Status advance is manual** — `PATCH /api/loads/{id}/status` is operator-driven. GPS-derived auto-advance (e.g., dock arrival → `pickup`) is partially wired via `load_events` but not closing the loop.
3. **Carrier invoice + collections require operator click** — `delivered` auto-drafts but operator presses Send.
4. **No auto-escalation on aged shipper AR** — aged buckets surface 30/60/90 but no auto-collections without operator trigger.
5. **Vetting decisions are human** — FMCSA data is auto-pulled but flip from "vetting" → "vetted_trusted" requires human review.
6. **Margin overrides are human** — `manual-override/waive-margin-floor` exists but no AI suggestion to override.
7. **Factor decline → blocked invoice + Required Task** but no auto-retry with different factor.
8. **Pre-Authority Mode gate** is manual director gate (during FMCSA protest window).

---

## Self-healing loops (where the system corrects itself)

1. `circuit_breaker` — auto-trips on failure rate, auto-resets on success streak after cooldown
2. `bmc84_watcher` — auto-clears FMCSA "PENDING" banner when authority/bond flips
3. `auto_repost_scheduler` — auto-ratchets rate on stale loads
4. `gap_close_watcher` — auto-celebrates `OPERATING_MANUAL.md` row going 🚧→✅
5. `synthetic_auto_wipe` — auto-deletes test data after 24h
6. `failure_detectors` (12 loops) — alert on (auth-lost, GPS-silent, no-show, factor-decline, BOL-mismatch, detention overage, ghosting, double-broker, bankruptcy, lumper-short, reweigh, cargo-claim)
7. `silent_shipper_ping` — auto-followup on RFQs without booking after 24h
8. `quote_recovery_loop` — hourly scan, sends recovery email to shipper at T+24h, T+48h
9. Anthropic SDK retry-with-backoff (boot_briefing, etc.) — auto-recovers from transient 5xx
10. AsyncOpenAI built-in retries + timeout

---

## Cron schedule (inferred from code)

| Cron | Frequency | Module |
|---|---|---|
| RFQ IMAP poller | Every 5 min | `rfq_imap_poller.py` |
| FMCSA BMC-84 watcher | Every 30 min | `bmc84_watcher.py` |
| Auto-repost scheduler | Every 30 min | `auto_repost_scheduler.py` |
| Gap-close watcher | Every 5 min | `gap_close_watcher.py` |
| Failure detectors | Continuous loops | `failure_detectors*.py` |
| Quote recovery | Hourly | `server.py` (quote_recovery_loop) |
| Silent shipper ping | Daily | `silent_shipper_ping.py` |
| Day-1 readiness email | Sunday 18:00 CT | `day1_readiness_email.py` |
| Live-wire diary | Daily 18:00 CT | `live_wire_diary.py` |
| Synthetic auto-wipe | Hourly | `synthetic_auto_wipe.py` |

---

## Drift indicators (against existing memory/ docs)

PM Claude should compare this doc against `OPERATING_MANUAL.md`, `E2E_LOAD_LIFECYCLE_AUDIT.md`, `PRD.md`, `CAPABILITIES.md` for drift. Likely drift points (based on quick scan):

- Some docs reference `gemini-2.5-pro` for RFQ parsing — actual is `openai/gpt-4o` (synced in iter 139.44, parser_model field corrected)
- Some docs reference `gemini-2.0-flash` for POD OCR — actual is `gpt-4o` vision (post iter 139.46 stage 6b)
- Some docs reference `EMERGENT_LLM_KEY` — fully removed from active code as of iter 139.46
- `emergentintegrations` proxy URL no longer hit (only Stripe import remains, server.py:16)

---

## Playbook alignment (mapping code-derived stages to CHL_STRATEGIC_PLAYBOOK)

The playbook defines a 10-stage lifecycle that's broader than what's in code today. This table maps code-derived stages (above) to the playbook's stages and flags gaps.

| Code stage (this doc) | Playbook stage(s) | Coverage | Gap to close |
|---|---|---|---|
| 1 — RFQ intake | Stage 1 (Load Discovery, RFQ-side only) + part of Stage 2 (Evaluation) | ✅ for RFQ-via-email/IMAP/paste | ❌ **Load board monitoring** — playbook Stage 1 includes scanning DAT/Truckstop/123LB/etc. for shipper-posted loads. Code has adapter stubs (`loadboards.py:332-404`) but no active polling. This is a Phase 2 deliverable. |
| 2 — Quote / decision | Stage 2 (Evaluation) + Stage 3 (Booking, accept side) | ✅ heuristic + AI margin recommendation; review-and-send | ⚠️ **Auto-bid on load boards** — no programmatic bidding to win loads off boards. Phase 2 work. ⚠️ **No AI-suggested counter-offers** when shipper rejects quote. |
| 3 — Dispatch / assignment | Stage 4 (Carrier Assignment) + Stage 5 (Dispatch) | ✅ rule-based auto-dispatch + manual override; rate-con generation live | ⚠️ **AI carrier matching** — playbook target is ML-ranked carriers by lane history, reliability, equipment fit. Code is hard-rule gates only. Phase 3 work. ⚠️ **Multi-carrier outreach (3 simultaneous, first-accept-wins)** — playbook bottleneck mitigation; not in code. |
| 4 — Tracking | Stage 6 (Tracking) | ⚠️ 12 failure detectors live; SMS check-in scheduler live | ❌ **Real-time GPS integration** (Samsara, Geotab, carrier apps) — `load_events` captures dock geofence but no live carrier-side GPS feed. ❌ **Auto-advance load.status from GPS** — geofence events captured but `loads.status` is operator-driven via PATCH. Closing this loop is Phase 4 work. |
| 5 — Completion (POD/BOL) | Stage 7 (Delivery Confirmation) | ✅ POD upload + gpt-4o vision OCR + signature detection + red-flag extraction | ⚠️ **Lumper/detention proof OCR** — POD path is real OCR; lumper still keyword-pattern only (`_ocr_scan_placeholder` legacy). Phase 5 cleanup. |
| 6 — Billing | Stage 8 (Invoicing) + Stage 9 (Factoring) + Stage 10 (Carrier Settlement) | ✅ broker invoice gen + Stripe Checkout for carrier payouts; Apex+Triumph factor adapters live | ⚠️ **Other factor adapters** (RTS, OTR, etc.) — stubs awaiting credentials. Phase 6 work. ❌ **Auto-retry on factor decline with backup factor** — currently blocks + Required Task. ❌ **Auto-escalation on aged shipper AR** — buckets surface 30/60/90 but no auto-collection without operator trigger. ❌ **Margin reconciliation report** — no carrier-margin variance report at settlement. |

### Cross-cutting gaps (not in any single stage)

- **Autonomous Throttle System (GREEN/YELLOW/ORANGE/RED).** Playbook describes a system-wide intake throttle that adapts to API health, error rates, latency. Code has `circuit_breaker.py` (binary tripped/not-tripped, per-load-failure-rate) but **not** the holistic 4-state throttle on intake speed across all stages. This is a Phase 7 deliverable.
- **Self-healing framework (Detection → Diagnosis → Remediation → Escalation).** Primitives exist (failure detectors, retry-with-backoff in SDKs, circuit breaker, fallback paths in `boot_briefing.py`) but they're distributed across modules, not unified. No central health-check endpoint reporting per-module status every 60s. Phase 7 work.
- **Health check endpoints.** Playbook calls for "every module reports status every 60s." Currently `/api/system-state` is operator-facing aggregate snapshot (30s cache); not a per-module heartbeat protocol. Phase 7 work.
- **Pre-verified shipper fast-track.** Playbook calls for skipping factoring for high-credit known shippers (direct billing). Code has `shipper_credit_headroom.py` stub awaiting HaulPay token; logic gap.
- **Performance targets** (load eval <60s, carrier assignment <5min, dispatch <10min, invoicing <1h, factoring <24h, end-to-end <48h) — no SLA monitoring or alerting on misses. Phase 7+8 work.

### Roadmap phase mapping (where iters belong)

| Phase | Status | Closes by iter |
|---|---|---|
| 0 — Foundation | ✅ DONE | iter 139.45 |
| 1 — EMERGENT_EXIT_PLAN Closure | ✅ DONE | iter 139.46 |
| 2 — Load Discovery & Evaluation | 🚧 PLANNED | iters 140.x |
| 3 — Carrier Network & Dispatch | 🚧 PLANNED | iters 141.x |
| 4 — Tracking & Exception Handling | 🚧 PLANNED | iters 142.x |
| 5 — Completion & Invoicing | ⚠️ PARTIAL (POD live; shipper-portal auto-submit + lumper OCR not done) | iters 143.x |
| 6 — Factoring & Settlement | ⚠️ PARTIAL (Apex+Triumph live; other factors stub; carrier ACH live) | iters 144.x |
| 7 — Monitoring & Self-Healing | 🚧 PLANNED (throttle system, unified self-healing) | iters 145.x |
| 8 — Scale & Optimization | 🚧 PLANNED | iters 146.x+ |
| 9 — Edge Cases & Compliance | 🚧 PLANNED (HAZMAT/oversize/refrigerated, claims workflow) | iters 147.x+ |
| 10 — Continuous Improvement | 🚧 PLANNED (A/B testing, ML training) | ongoing |

### Strategic note on iter 139.x → 140.x transition

This iter sequence (139.x) closed Foundation and EMERGENT_EXIT_PLAN. The natural next iter (140.x) opens Phase 2 (Load Discovery & Evaluation). PM Claude's iter 139.47 candidates list (Stripe Checkout migration, comment cleanup, R2 leak fix) does **not** map to any playbook phase — they're operational hygiene, not roadmap progress. Recommend:
- **Either:** treat 139.47 as final hygiene iter before 140.x (close Stripe + comments + R2 in one batch)
- **Or:** skip 139.47 entirely and jump to 140.1 (load board API integration scaffolding)

Operator decision per playbook's "Autonomy Over Features" principle.

---

DONE
