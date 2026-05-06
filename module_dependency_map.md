# Module Dependency Map — Code-Derived

**Generated:** 2026-05-06 (post iter 139.46 close)
**Source:** Direct read of `C:\CHL\backend\` only.

How data and control flow through the CHL backend. Each section shows: **input → module → output**.

---

## High-level data flow (load lifecycle)

```
                      ┌────────────────────────────┐
  Resend webhook      │  STAGE 1: RFQ INTAKE        │
  IMAP poller    ───▶ │  rfq_inbound.py             │
  Manual paste        │   - _parse_with_ai (gpt-4o) │
                      │   - _safe_json_parse        │
                      │   - PDF→JPEG (pymupdf)      │
                      └────────────┬────────────────┘
                                   │ writes
                                   ▼
                      ┌────────────────────────────┐
                      │ db.rfq_inbound             │
                      │ db.quotes_inbound (per     │
                      │   lane)                    │
                      │ db.shipper_profiles        │
                      └────────────┬────────────────┘
                                   │ read by
                                   ▼
                      ┌────────────────────────────┐
                      │  STAGE 2: QUOTE             │
                      │  auto_quote_draft.py        │
                      │   - _suggest_rate()         │
                      │  autopilot_phase1.py        │
                      │   - recommend_margin()      │
                      └────────────┬────────────────┘
                                   │ writes
                                   ▼
                      ┌────────────────────────────┐
                      │ db.quotes_inbound          │
                      │   .auto_quote_draft        │
                      └────────────┬────────────────┘
                                   │ accepted
                                   ▼
                      ┌────────────────────────────┐
                      │  STAGE 3: DISPATCH          │
                      │  auto_load_pipeline.py      │
                      │  auto_dispatch.py           │
                      │   - try_auto_accept()       │
                      │  margin_floor_router.py     │
                      │  automations.py             │
                      │   - rate-con generation     │
                      └────────────┬────────────────┘
                                   │ writes
                                   ▼
                      ┌────────────────────────────┐
                      │ db.loads                   │
                      │ db.auto_accept_log         │
                      │ db.rate_confirmations      │
                      │ db.rate_signals (margin    │
                      │   outcomes for AI)         │
                      └────────────┬────────────────┘
                                   │ events from
                                   ▼
                      ┌────────────────────────────┐
                      │  STAGE 4: TRACKING          │
                      │  - GPS pings, geofence      │
                      │  - failure_detectors*.py    │
                      │  - auto_repost_scheduler    │
                      │  - SMS check-in             │
                      └────────────┬────────────────┘
                                   │ writes
                                   ▼
                      ┌────────────────────────────┐
                      │ db.load_events             │
                      │ db.repost_pulses           │
                      │ db.failure_alerts          │
                      └────────────┬────────────────┘
                                   │ status='delivered'
                                   ▼
                      ┌────────────────────────────┐
                      │  STAGE 5: COMPLETION        │
                      │  load_completion_workflow.py│
                      │   - validate_pod_image_     │
                      │     quality()               │
                      │   - run_pod_ocr_via_gemini  │
                      │     (now gpt-4o vision)    │
                      │  automations.py             │
                      │   - BOL generation          │
                      └────────────┬────────────────┘
                                   │ writes
                                   ▼
                      ┌────────────────────────────┐
                      │ db.pod_uploads             │
                      │ db.load_documents          │
                      │ db.required_tasks (Type E) │
                      └────────────┬────────────────┘
                                   │ delivered + POD ✓
                                   ▼
                      ┌────────────────────────────┐
                      │  STAGE 6: BILLING           │
                      │  delivered_load_autofire    │
                      │  billing_automation.py      │
                      │   - _compute_detention()    │
                      │  automations.py             │
                      │   - invoice generation      │
                      │   - Stripe Checkout link    │
                      └────────────┬────────────────┘
                                   │ writes
                                   ▼
                      ┌────────────────────────────┐
                      │ db.invoices                │
                      │ db.carrier_payments        │
                      │ db.billing_dispatches      │
                      └────────────────────────────┘
```

---

## Module → module dependencies

### `server.py` (monolith)

**Imports / depends on:**
- `automations.py` (router registration: rate-con, BOL, invoice, voice agent endpoints)
- `rfq_inbound.py` (router registration)
- `load_completion_workflow.py` (router registration: POD upload)
- `live_wire_diary.py` (router + cron)
- `boot_briefing.py` (Anthropic-backed narrative)
- `auto_dispatch.py`, `auto_load_pipeline.py`, `auto_quote_draft.py`
- `failure_detectors.py`, `failure_detectors_part2.py`
- `circuit_breaker.py`, `margin_floor_router.py`
- `bmc84_watcher.py` (cron)
- `auto_repost_scheduler.py` (cron)
- `silent_shipper_ping.py`, `gap_close_watcher.py` (cron)
- `day1_readiness_email.py` (cron)
- `synthetic_auto_wipe.py` (cron)
- `rfq_imap_poller.py` (cron)
- `factor_matrix.py`, `factor_api_integration.py`
- `stripe_connect.py`, `compliance.py`, `pre_authority.py`
- `manual_coverage.py` (parses `OPERATING_MANUAL.md`)
- `eld_integration.py`, `multimodal.py`, `load_dedup.py` (partial)
- Standard libs: `motor` (async MongoDB), `fastapi`, `httpx`, `pydantic`

**Imported by:** None (entry point — uvicorn loads `server:app`).

---

### `rfq_inbound.py`

**Inputs:**
- HTTP: `POST /api/rfq-inbound/webhook` (Resend), `POST /api/rfq-inbound/parse-text` (manual)
- Cron-driven: called by `rfq_imap_poller.py` after IMAP fetch

**Calls:**
- `AsyncOpenAI` direct (gpt-4o vision, post iter 139.43 stage 3b)
- `pymupdf` (`fitz`) for PDF→JPEG conversion (post iter 139.45 stage 5a)
- `db.rfq_inbound.insert_one`, `db.quotes_inbound.insert_many`, `db.shipper_profiles.upsert`

**Outputs:**
- `_RFQExtract` shape: `{shipper_company, lanes[], notes, parser_model="openai/gpt-4o"}`
- HTTP responses

**Failure modes handled:**
- `HTTPException(503)` if no `OPENAI_API_KEY`
- `HTTPException(422)` if PDF empty / corrupt / >10 pages

---

### `load_completion_workflow.py`

**Inputs:**
- HTTP: `POST /api/load-completion/pod-upload/{load_id}` (driver/public)
- Internal call: `assemble_billing_packet()` (line 122 calls `run_pod_ocr_via_gemini`)

**Calls:**
- `validate_pod_image_quality()` — heuristic image check
- `AsyncOpenAI` direct (gpt-4o vision, post iter 139.46 stage 6b — was gemini-2.0-flash via emergent proxy)
- `db.pod_uploads.insert`, `db.load_documents.insert`
- `pypdf` + `reportlab` for billing packet PDF assembly

**Outputs:**
- POD OCR result dict: `{ran, engine="gpt-4o", signature_detected, is_actual_bol, extracted_text, red_flags[], run_at}`
- Assembled billing packet PDF bytes

---

### `automations.py`

**Top-level constants:** `FMCSA_WEBKEY` (optional), `RESEND_INBOUND_SECRET` (post iter 139.46: no more `EMERGENT_LLM_KEY`).

**Router endpoints registered:**
- `POST /api/automations/rate-con/{load_id}` (rate-con PDF + email)
- `POST /api/automations/bol/{load_id}` (BOL PDF + email)
- `POST /api/automations/invoice/{pending_payment_id}` (carrier invoice + Stripe link)
- `POST /api/automations/checkin/trigger` (SMS check-in scheduler)
- `POST /api/automations/webhook/voice/agent` (Twilio voice agent)
- `POST /api/auto-quote-draft/email-intake` (manual email paste)

**LLM-calling functions (post iter 139.46 stage 6a):**
- `llm_parse_load_from_email(raw)` → AsyncOpenAI gpt-4o, returns parsed dict or `{"_error": ...}`
- `llm_voice_agent(caller_said, context)` → AsyncOpenAI gpt-4o with 8s timeout, returns spoken response or fallback voicemail string

**External integrations:**
- Twilio (voice + SMS)
- Resend (email)
- Stripe (Checkout for carrier payouts)
- FMCSA SAFER (carrier vetting via `fmcsa_lookup`)

---

### `failure_detectors.py` + `failure_detectors_part2.py`

**Triggers:** Background async loops, started by `server.py` startup event.

**Watches:**
- `db.loads.status='in_transit'` rows for GPS silence >4h
- `db.loads.eta_arrival` past + status not `delivered` for no-show
- Carrier SMS response timestamps for ghosting
- External boards for double-broker matches
- FMCSA bankruptcy flag
- POD OCR results for damage/short keywords
- Reweigh weight vs `weight_lbs`
- Factor API decline responses

**Writes:**
- `db.failure_alerts` (alert log)
- SMS via Twilio
- Email via Resend

---

### `auto_dispatch.py`

**Trigger:** `POST /api/carrier/loads/{id}/interest`

**Inputs:**
- `business_settings` (auto_accept_master_enabled, margin_floor_pct, max_auto_rate)
- `carriers` (vetted_trusted, mc_verified, blocklist)
- `loads` (margin, risk_flags)

**Logic:** 8-gate hard ruleset.

**Writes:**
- If accepted: `db.loads.update` (carrier_id, status='assigned'), `db.auto_accept_log`
- If rejected: `db.auto_accept_log` with reason code

---

### `boot_briefing.py`

**Trigger:** `GET /api/admin/boot-briefing`

**Calls:**
- `AsyncAnthropic` direct (Anthropic Claude, post iter 139.42)
- `db.loads.find` (last 24h), `db.invoices.find`, `db.carrier_payments.find`

**Output:** `{source: "llm" | "fallback", markdown: string}`

**Fallback path:** if Anthropic API fails (429/529/timeout), returns hand-built summary from collection counts. Engaged during stage 6d smoke window when Anthropic returned 529.

---

### `live_wire_diary.py`

**Trigger:** Cron (18:00 CT daily) + `POST /api/diary/run-now`

**Calls:**
- `AsyncOpenAI.audio.speech.create` (tts-1, voice "onyx", post iter 139.43 stage 3d — was emergentintegrations TTS)
- `db.live_wire_diary.insert`, sends SMS via Twilio + email via Resend

---

### `factor_matrix.py` + `factor_api_integration.py`

**Inputs:**
- HTTP: invoice generation routes
- Internal: `assemble_billing_packet()` for evidence packet

**Adapters:**
- Apex (live SFTP)
- Triumph (live SFTP)
- RTS, OTR, others (stubs)

**Calls:**
- Outbound SFTP for live adapters
- `db.factor_submissions`

---

### `bmc84_watcher.py`

**Trigger:** Cron every 30 min.

**Calls:**
- FMCSA SAFER lookup
- `db.business_settings.update` (authority/bond status flip)
- Twilio SMS to owner if authority lost

---

### `circuit_breaker.py`

**Trigger:** Called from dispatch / auto-accept paths.

**Logic:**
- Maintains failure rate (failures / loads booked, rolling window)
- Trips at threshold → flips `business_settings.circuit_tripped=True`
- Records in `db.circuit_trip_log`
- Auto-resets after `cooldown_sec` + success streak

**Effect:** While tripped, `auto_dispatch.try_auto_accept()` returns rejected for all loads → human-only mode.

---

## External service dependencies

| Service | Used by | Auth env var |
|---|---|---|
| OpenAI API | `rfq_inbound.py`, `automations.py`, `load_completion_workflow.py`, `live_wire_diary.py`, `rfq_concierge.py` | `OPENAI_API_KEY` |
| Anthropic API | `boot_briefing.py` | `ANTHROPIC_API_KEY` |
| Twilio | `automations.py` (voice agent), check-in, browser_phone | `TWILIO_ACCOUNT_SID`, `TWILIO_AUTH_TOKEN`, `TWILIO_PHONE_NUMBER` |
| Resend | inbound webhook + outbound email | `RESEND_INBOUND_SECRET` (and outbound key) |
| Stripe Connect | `stripe_connect.py`, `automations.py` (Checkout for carrier payouts) | `STRIPE_API_KEY` |
| FMCSA SAFER | `bmc84_watcher.py`, `automations.py:fmcsa_lookup` | `FMCSA_WEBKEY` (optional) |
| Mercury Bank | (read-only owner views) | `MERCURY_API_KEY` |
| Zoho IMAP | `rfq_imap_poller.py` | `ZOHO_IMAP_HOST/USER/PASS` |
| MongoDB | All modules | (connection string in env) |
| R2/S3 | POD storage (inferred) | (S3 creds) |

---

## Cron job → module mapping

```
Every 5 min  ──┬─▶ rfq_imap_poller.py     (Zoho RFQ inbox)
               └─▶ gap_close_watcher.py   (OPERATING_MANUAL.md changes)

Every 30 min ──┬─▶ bmc84_watcher.py       (FMCSA authority/bond)
               └─▶ auto_repost_scheduler.py (rate ratchet)

Hourly       ──┬─▶ server.py:quote_recovery_loop
               ├─▶ synthetic_auto_wipe.py
               └─▶ server.py:pnl_scheduler_loop (scaffold)

Daily 18:00  ──┬─▶ live_wire_diary.py     (TTS + SMS + email)
               └─▶ silent_shipper_ping.py (24h RFQ followup)

Sunday 18:00 ──▶ day1_readiness_email.py  (weekly digest)

Continuous   ──▶ failure_detectors*.py    (12 background loops)
```

---

## Routing graph (FastAPI router registrations)

```
server.py (root: /)
├── /api/auth                    ← built into server.py
├── /api/admin                   ← built into server.py
├── /api/loads                   ← built into server.py
├── /api/billing                 ← built into server.py
├── /api/load-dossier            ← built into server.py
├── /api/system-state            ← built into server.py
├── /api/aged-ar                 ← built into server.py
├── /api/manual-override         ← built into server.py
├── /api/carrier                 ← built into server.py
├── /api/shipper                 ← built into server.py
├── /api/auto-accept             ← built into server.py
├── /api/sop-flow                ← built into server.py
├── /api/public                  ← built into server.py
│
├── /api/rfq-inbound       ────▶ rfq_inbound.py (router)
├── /api/rfq-imap          ────▶ rfq_imap_poller.py (router)
├── /api/load-completion   ────▶ load_completion_workflow.py (router)
├── /api/automations       ────▶ automations.py (router)
├── /api/diary             ────▶ live_wire_diary.py (router)
├── /api/auto-quote-draft  ────▶ auto_quote_draft.py (router)
├── /api/lane-rate-history ────▶ autopilot_phase1.py (router)
├── /api/margin-floor      ────▶ margin_floor_router.py (router)
├── /api/collections-packets──▶ (server.py or sub-router; verify)
└── /api/mercury           ────▶ mercury_bank.py (or in server.py)
```

---

## State changes triggered by each lifecycle stage

```
STAGE 1: RFQ inbound webhook OR IMAP poller OR manual paste
  → INSERT db.rfq_inbound
  → INSERT db.quotes_inbound (per lane)
  → UPSERT db.shipper_profiles
  → INSERT db.required_tasks (Type F)

STAGE 2: Operator triggers draft, reviews, sends
  → UPDATE db.quotes_inbound.auto_quote_draft
  → (no auto-state change; review-and-send only)

STAGE 3a: Auto-dispatch path
  → UPDATE db.quotes_inbound.status = "accepted"
  → INSERT db.loads
  → INSERT db.auto_accept_log
  → INSERT db.rate_confirmations
  → INSERT db.rate_signals (margin outcome for AI training)

STAGE 3b: Manual dispatch path
  → INSERT db.loads
  → operator advances db.loads.status manually via PATCH

STAGE 4: Active load
  → INSERT db.load_events (dock arrival, departure, lumper paid)
  → UPDATE db.loads.status (operator-driven OR derived from events — gap)
  → INSERT db.repost_pulses (auto_repost_scheduler)
  → INSERT db.failure_alerts (background detectors)

STAGE 5: POD upload by driver
  → INSERT db.pod_uploads
  → INSERT db.load_documents (POD + lumper + detention)
  → IF OCR detects damage/short → INSERT db.failure_alerts
  → IF mismatch with rate-con → INSERT db.required_tasks (Type E)

STAGE 6: Status → delivered
  → trigger delivered_load_autofire
  → INSERT db.invoices (broker invoice)
  → INSERT db.carrier_payments (carrier invoice + Stripe link)
  → INSERT db.billing_dispatches (assembly record + email send status)
```

---

## Cross-cutting concerns

### Auth flow

```
server.py (middleware)
  ├── /api/auth/login → JWT cookie
  ├── /api/auth/refresh
  ├── require_broker (dispatcher)
  ├── require_owner (admin)
  ├── magic-link / public-token (driver POD upload, public quote)
  └── webhook signature verification (Resend, Stripe, Twilio)
```

### Logging

- Module-level loggers (e.g., `logger = logging.getLogger("load_completion_workflow")`)
- NSSM service writes to `C:\CHL\logs\nssm\backend.err.log` and `backend.out.log`
- LiteLLM internal retries suppressed via `LITELLM_DROP_PARAMS` and `LITELLM_REQUEST_TIMEOUT` env vars (`automations.py:48-49`)

### Synthetic test data

- Tagged with `is_synthetic=True` flag
- `synthetic_auto_wipe.py` cron deletes after 24h
- `synthetic_audit` route flags any code path missing the synthetic-exclusion filter (so test data never bleeds into prod metrics)

### Vault / credentials

- 46-entry MongoDB vault with Fernet AES-256 encryption (`VAULT_ENCRYPTION_KEY` env)
- Bootstrapped in iter 139.42 stage 3a
- Houses sensitive creds beyond what `.env` holds

---

## Recommended next-iter candidates (from gap analysis)

1. **GPS-event → load.status auto-advance** — close the Stage 4 loop. Code framework supports it; missing the bridge logic.
2. **Stripe Checkout migration** — last `emergentintegrations` import (server.py:16). Closes the proxy dependency entirely.
3. **AI-suggested override** — surface "this load is borderline, consider X" in the dashboard.
4. **Cosmetic doc cleanup batch** — 4 stale comments in server.py + 5 docstring mentions across 3 files + orphaned comment from iter 139.46 stage 6d.
5. **Multi-factor auto-retry** — when one factor declines, try backup factor before blocking invoice.
6. **Load model extension** — multi-stop / multi-leg / LTL support if business needs it.
7. **R2/S3 cron leak fix** — iter 139.41 carry-over (25 leaks vs 5-leak threshold; needs investigation iter first).

---

## Playbook alignment — modules that implement (or should implement) cross-cutting concerns

The strategic playbook calls out **two cross-cutting subsystems** that don't fit cleanly under any one stage. This section maps those subsystems to current code presence.

### Autonomous Throttle System (playbook Phase 7)

| Capability | Current code | Gap |
|---|---|---|
| Health monitoring (per-module 60s heartbeat) | None (only `/api/system-state` aggregate, 30s cache, operator-facing) | ❌ Need `/health` endpoints per module |
| Error rate tracking per pipeline stage | `circuit_breaker.py` tracks load-failure rate (binary) | ❌ Need per-stage rates |
| Latency tracking | None (no SLA monitor) | ❌ Need histogram per endpoint |
| GREEN/YELLOW/ORANGE/RED state machine | None | ❌ Net new module: `throttle_state.py` |
| Adaptive intake throttle | None | ❌ Net new feature |
| Auto-recovery monitoring (15+ min stable → step up) | `circuit_breaker.py` has cooldown_sec but no graduated recovery | ❌ Extend circuit_breaker or new module |
| SMS alerting on RED | `failure_detectors.py` SMS via Twilio | ⚠️ Wiring exists, just needs throttle state input |

### Self-Healing Architecture (playbook Phase 7)

| Layer | Current code | Gap |
|---|---|---|
| **Detection** — health checks, error rate, latency, anomaly | `failure_detectors.py` + `failure_detectors_part2.py` (12 modes), boot_briefing fallback | ⚠️ Detection is pattern-match, not anomaly-detection; no centralized health framework |
| **Diagnosis** — structured logs, stack traces, request/response logging | Module-level loggers; LiteLLM + Anthropic SDK retry warnings in `backend.err.log` | ⚠️ Distributed; no central error taxonomy |
| **Remediation** — retry+backoff, fallback providers, circuit breakers, graceful degradation, data correction | OpenAI/Anthropic SDK built-in retry, `boot_briefing.py` fallback to non-LLM template, `circuit_breaker.py`, no auto-data-correction | ⚠️ Primitives exist but distributed; no central remediation registry |
| **Escalation** — SMS for RED, email for YELLOW/ORANGE, daily digest | Twilio SMS + Resend email + `live_wire_diary.py` daily summary | ✅ Coverage adequate; just needs throttle-state input |

### Performance targets (playbook bottleneck analysis)

| Target | Current measurement | Gap |
|---|---|---|
| Load eval <60s | No SLA monitor; observed gpt-4o vision parse 2-4s for typical RFQs | ❌ Need histogram + SLA alert |
| Carrier assignment <5min | No SLA monitor | ❌ Net new metric |
| Dispatch <10min | No SLA monitor | ❌ Net new metric |
| Invoicing <1h post-delivery | `delivered_load_autofire.py` runs immediately on status flip | ✅ Auto-fire on delivered; meets target if operator reviews quickly |
| Factoring <24h | Apex/Triumph SFTP batch timing varies | ⚠️ Track factor-submission-to-funding-receipt latency |
| End-to-end <48h load → cash | No tracking | ❌ Net new metric (tag load with `discovered_at`, `cash_received_at`) |

### Bottlenecks (playbook bottleneck analysis)

| Bottleneck | Current state | Mitigation in code | Gap |
|---|---|---|---|
| LLM API latency | gpt-4o ~2-10s per call | retry+backoff in SDK | ❌ No request batching or caching of repeat shippers |
| Load board rate limits | n/a (no active polling) | n/a | ❌ Smart polling logic when boards activated |
| Carrier response time | Manual SMS check-ins | `automations.py:checkin/trigger` | ❌ No multi-carrier simultaneous offer pattern |
| Factoring verification | Apex/Triumph SFTP varies | Batch submit | ❌ No fast-track for pre-verified shippers |
| DB write contention | MongoDB single instance | None | ❌ Phase 8 work (sharding, read replicas) |

---

## Strategic note

This module map shows **healthy domain decomposition** at the per-stage level (RFQ, dispatch, completion, billing each have their own modules). The biggest **structural gaps** are cross-cutting:
1. No unified self-healing framework
2. No throttle state machine
3. No central health endpoint protocol
4. No SLA monitoring infrastructure

Phase 7 work will need to either retrofit these as decorators/middleware (cleanest) or as a parallel monitoring service. Recommend retrofitting as middleware so existing module code doesn't change.

---

DONE
