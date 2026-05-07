# Iter 142.2 — Phase 4 Tracking & Exception Handling Preliminary Scope Sketch

> **Source:** PM Claude (`@pm-lead`) drafted 2026-05-07 during autonomous-continue mode (post-iter-141.1-close, while iter 141.3 stage 1a blocked pending Plivo sales response).
> **Status:** Preliminary sketch (~1 page). Not a full agenda. Operator + dev review; full agenda for iter 142.2 drafted by @pm-lead when operator authorizes iter 142.2 to open (post-iter-142.1).
> **Strategic phase:** `CHL_STRATEGIC_PLAYBOOK.md` Phase 4 (Tracking & Exception Handling). Milestone: "System monitors loads and handles routine issues autonomously."

---

## Goal

Real-time GPS-backed load tracking + exception detection (late / off-route / stopped) + automated carrier communication for routine resolution. Shifts CHL from "carrier reports status when asked" (iter 142.1 stage 1c check-ins) to "system detects exception → auto-responds → only escalates the tail to operator."

---

## Stage Breakdown (4 stages estimated, ~150–200 LOC each, ~600–800 total)

### Stage 1a: GPS Provider Integration + Tracking Schema

- New `db.load_tracking` collection: `{load_id, carrier_mc, gps_source, position_history: [{lat, lng, ts}], speed_history, last_ping_at, opt_in_at}`
- GPS source options (operator decision pre-kickoff — see Open Questions): Samsara API (fleet ELDs, ~$30–60/mo per truck), Geotab API (similar), or carrier-side mobile-app integration (lower cost, requires carrier app install).
- New `backend/tracking_ingest.py`: poll-based pull (or webhook receive) per active load; throttle-respecting; writes positions to `db.load_tracking` at ~5-min cadence default.
- Endpoints: `POST /api/loads/{load_id}/tracking/enroll`, `GET /api/loads/{load_id}/tracking/current`, `GET /api/loads/{load_id}/tracking/history`.
- **Carrier opt-in flow:** GPS-share enrollment link in dispatch packet (iter 142.1 stage 1f rate-con email). Carrier clicks → consents to position sharing for the active load duration only. Default: track active loads only, expire 7 days post-delivery.

### Stage 1b: Exception Detection Rule Engine

- New `backend/exception_detector.py`: scheduled task every 15 min checks all `dispatch_status: "confirmed"` loads against rule set:
  - **Late at pickup:** `now > pickup_due_at + 30min` AND no `picked_up_at`
  - **Off-route:** current position >25 miles from planned route OR opposite direction of travel for >10 min
  - **Stopped (not at pickup/delivery):** speed <5mph for >2h AND not within 5 miles of pickup/delivery address
  - **No GPS ping:** `last_ping_at < now - 30min` AND load is in-transit
- New `db.load_exceptions` collection: `{load_id, exception_type, severity: "info|warn|critical", detected_at, resolved_at, auto_action_taken, escalated_to_operator}`
- Severity tuning operator-configurable via `business_settings.tracking_thresholds`.

### Stage 1c: Automated Carrier Communication (Check-in + ETA Updates)

- Reuses `backend/carrier_outreach.py` from iter 142.1 stage 1d (no new outreach module).
- New behaviors:
  - **Late detection → SMS check-in:** "CHL: Load {load_id} pickup ETA was {time}. Are you delayed? Reply 1=at pickup, 2=delayed, 3=cancelled." Auto-fired by exception detector on `late` severity ≥ warn.
  - **Stopped detection → SMS check-in:** "CHL: Load {load_id} shows stopped {duration} at {location}. Everything OK? Reply 1=routine break, 2=mechanical issue, 3=accident."
  - **ETA recalc:** On position update, recalc ETA via simple distance/avg-speed model. If new ETA differs from prior by >30 min, auto-notify shipper (email-only at v1; SMS to shipper if shipper opted-in).
- Operator-escalation path: any exception severity `critical` OR carrier non-response to 2 consecutive check-ins → operator-alert SMS + dashboard flag.

### Stage 1d: Operator Tracking Dashboard + Manual Exception Override

- New `/api/dashboard/active_loads_tracking` endpoint (broker-JWT-protected, returns active loads with current GPS + exception status).
- Frontend dashboard page: live map of active loads + exception list + 1-click "acknowledge" / "override" / "escalate to phone call" actions.
- Manual exception flag: operator can flag a load for tracking-priority (e.g., high-value shipper, hazmat) → exception detector raises severity threshold sensitivity for flagged loads.

---

## Open Questions (operator decision points before 142.2 opens)

1. **GPS provider primary source** — Samsara (mature, expensive, fleet-side ELD), Geotab (similar), carrier mobile-app (cheaper, requires carrier adoption), or hybrid? Strong recommendation: start with carrier mobile-app (lowest carrier-side adoption friction at v1; defer fleet-ELD integration to v3+ when broker pricing supports the per-truck cost).
2. **Exception severity thresholds** — proposed defaults: late >30min = warn, late >2h = critical; stopped >2h = warn, stopped >4h = critical; off-route >25mi = warn. Operator adjusts based on lane / shipper sensitivity.
3. **Self-healing automation scope** — auto-recalc ETA + auto-notify shipper at v1 OK? Or operator-in-loop for all shipper notifications? (Auto-notify reduces operator load but risks miscommunication if ETA model is wrong; recommended start: auto-notify only for ETA delays >2h, operator-in-loop for shorter delays.)
4. **Multi-broker tracking consent partitioning** — when CHL hosts multiple brokers, each broker's tracking enrollment is per-load-per-carrier-relationship; consent does NOT auto-propagate cross-broker. Same partition shape as iter-142.1-stage-1d `db.carrier_consent` but for tracking specifically.
5. **POD photo capture** — should iter 142.2 stage 1d include photo-of-BOL at pickup + photo-of-POD at delivery, or defer entirely to Phase 5 (POD/invoicing iter 143.x)? Recommendation: defer; Phase 4 = real-time exceptions, Phase 5 = post-delivery proof.

---

## Dependencies on Iter 142.1

- Stage 1c CarrierEvaluator: reliability metric should incorporate tracking-derived signals (on-time arrival %, exception rate per carrier). Iter 142.2 stage 1b fires events that update CarrierEvaluator's reliability score per carrier.
- Stage 1d outreach module: reused for check-in SMS and ETA-update notifications. No new outreach code needed.
- Stage 1f dispatch packet: rate-con email gains a "GPS enrollment link" — operator-fillable in template, prominent CTA.

## Dependencies on Iter 141.3

- Stage 1c SMS communication path requires Plivo migration shipped (iter 141.3 stage 1d cutover). Pre-141.3-cutover, fall back to Twilio Voice-only check-in calls (more friction; recommend NOT opening 142.2 until 141.3 ships).

---

## Out of Scope (deferred to later iters)

- POD photo capture + verification (Phase 5, iter 143.x)
- Invoice generation from delivery confirmation (Phase 5)
- Factoring integration (Phase 6, iter 144.x)
- ML-based ETA prediction (current spec uses simple distance/avg-speed; ML at Phase 10)
- Multi-modal tracking (only over-the-road trucking at v1; rail / ocean / air post-revenue)
- Hazmat-specific exception handling (Phase 9)
- Driver-side mobile app development (carrier-side mobile-app integration only at v1; CHL-specific driver app deferred indefinitely)

---

## Probable smoke battery (~22 tests, expanded at full-agenda time)

- 1a: GPS API client init, position-history persistence, enrollment link issuance, opt-in webhook handling, throttle integration → ~6 tests
- 1b: rule firing for each of 4 exception types, severity escalation, no-false-positive on healthy loads → ~6 tests
- 1c: check-in SMS template rendering, response parsing, ETA recalc math, shipper notification gating → ~5 tests
- 1d: dashboard endpoint auth, override flag persistence, operator-alert SMS firing → ~5 tests

Total estimate: ~22 tests across stages.

---

## Operator hands-on time estimate (pre-full-agenda)

- 1a: ~15 min (GPS provider account signup if Samsara/Geotab; ~0 min if carrier mobile-app default)
- 1b: ~10 min (review + sign-off on exception severity thresholds)
- 1c: ~15 min (review + sign-off on check-in SMS templates + shipper-notification copy)
- 1d: ~10 min (visual review of tracking dashboard + 1-2 test loads through end-to-end)

Total: ~50 min hands-on across iter.

---

**Cross-reference:**
- `chl-memory/CHL_STRATEGIC_PLAYBOOK.md` Phase 4 boundary (lines 279–283)
- `chl-memory/agenda/iter_142_1_full.md` — Phase 3 carrier network agenda (1c CarrierEvaluator + 1d outreach module + 1f dispatch packet — all referenced as 142.2 dependencies)
- `chl-memory/research/iter_141_3_agenda_draft.md` — SMS provider migration (gates 142.2 stage 1c automated SMS check-ins)
- `chl-memory/research/messaging_stack_scaling_sketch.md` §5 — multi-broker consent partition pattern (applies to tracking-consent in iter 142.2 too)
- `chl-memory/research/iter_142_1_phase_3_sketch.md` (SUPERSEDED) — pattern reference for sketch structure

**Status:** ✅ SKETCH COMPLETE. Operator/dev review before iter 142.2 opens. Full agenda drafted by @pm-lead post-iter-142.1 ship + post-FMCSA-authority + post-141.3 cutover.

**Source:** Drafted by @pm-lead 2026-05-07 during autonomous-continue-mode quiet-time. Forward-looking only; no binding commitments.
