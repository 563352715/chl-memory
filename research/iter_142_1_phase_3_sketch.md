# Iter 142.1 — Phase 3 Carrier Network Preliminary Scope Sketch

> **STATUS: SUPERSEDED** by `agenda/iter_142_1_full.md` (commit f7fb39b, 2026-05-07).
> This sketch is preserved for historical reference. All current planning lives in the agenda doc.

> **Source:** PM Claude (`@pm-lead`) via AgentDM messages `45e3d7e7` + `ffb683e4` on 2026-05-06.
> **Status:** Preliminary sketch (~1 page). Not a full agenda. Operator reviews; final agenda + stage docs drafted by PM Claude when operator authorizes iter 142.1 to open (post-iter-141.2).

---

## Goal

Build multi-carrier outreach, assignment, and dispatch automation. Operator books load → system assigns optimal carrier → dispatch packet auto-generated.

## Stage Breakdown (4 stages estimated, ~200 lines each, ~800 total)

### Stage 3a: Carrier Database Schema + Ingestion
- New `db.carriers` collection with shape:
  ```
  {
    "mc_number": str,
    "legal_name": str,
    "lanes_served": [str],
    "reliability_score": float,
    "insurance_expiry": datetime,
    "contact_phone": str,
    "contact_email": str,
    "sms_opt_in": bool,
    "last_haul_date": datetime
  }
  ```
- Ingestion sources: FMCSA SAFER API (authority + insurance), manual CSV import
- New `CarrierEvaluator(FractalEvaluator)` at meso scale:
  - profitability = avg margin on their hauls
  - reliability = on-time delivery %
  - risk = claim rate
  - capacity = hauls/month
- Endpoints: `POST /api/carriers/import`, `GET /api/carriers/{mc_number}`

### Stage 3b: Multi-Carrier Outreach Orchestration
- Parallel outreach: SMS (Twilio) + email (Resend) + voice (Twilio if no response in 30 min)
- New `backend/carrier_outreach.py` module: select top 3 carriers for lane (by `CarrierEvaluator` composite score), send rate offers, track responses
- New `db.carrier_offers` collection: `{load_id, mc_number, offered_rate, sent_at, response_at, status: pending|accepted|rejected}`
- First-accept wins; others auto-cancel
- Endpoints: `POST /api/loads/{load_id}/outreach` (manual trigger for testing)

### Stage 3c: Carrier Assignment + Booking Confirmation
- `auto_assign_carrier(load_id)` triggered when carrier accepts offer
- Update `db.loads`: `{assigned_carrier_mc, carrier_rate, dispatch_status: "assigned"}`
- Send booking confirmation to carrier (SMS + email with load details)
- Failure detector: if carrier doesn't confirm within 2 hr, re-run outreach to next-best carrier
- Endpoints: `POST /api/loads/{load_id}/assign_carrier`

### Stage 3d: Dispatch Packet Auto-Generation
- Templates: rate-con (PDF), BCA (broker-carrier agreement), insurance cert request
- Fill templates: load details, carrier details, rates, pickup/delivery dates
- Deliver via email (Resend) + SMS link (Twilio)
- New `db.dispatch_packets` collection: `{load_id, rate_con_url, bca_url, sent_at, carrier_confirmed_at}`
- Endpoints: `GET /api/loads/{load_id}/dispatch_packet`

## Open Questions (operator decision points before 142.1 opens)

1. **Paid carrier data sources** — Highway? RMIS? CarrierAssure? Or FMCSA SAFER only?
2. **Self-serve carrier registration portal**? Or broker-controlled vetting only?
3. **Rate negotiation logic** — fixed offer? Or counter-offer rounds?
4. **Carrier blacklist/whitelist per shipper requirements**?

## Dependencies on Iter 141.2

- Auto-bid (stage 1f) wins load → needs carrier assignment handoff
- Current state: `auto_dispatch.try_auto_accept` handles booking, but **NO carrier assignment exists yet**
- Handoff point: after load booked, trigger `carrier_outreach.select_and_send()`

## Out of Scope (deferred to later iters)

- Factoring integration (Phase 6, iter 144.x)
- Real-time GPS tracking (Phase 4, iter 142.x post-Phase-3 OR separated to dedicated iter)
- POD capture + invoice (Phase 5, iter 143.x)
- Multi-broker SaaS (Phase 9+)

---

**Source:** AgentDM thread, msgs `45e3d7e7` + `ffb683e4`. Reconstructed via `@dev-engineer` ↔ `@pm-lead` async loop on 2026-05-06.

**Status:** Preliminary sketch — PM Claude proposes; operator reviews; full agenda for iter 142.1 drafted when operator authorizes (post-iter-141.2).
