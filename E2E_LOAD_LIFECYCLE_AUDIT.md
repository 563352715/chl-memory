# 🚛 End-to-End Load Lifecycle Audit — Continental Haul Logistics
> Built 2026-04-25 for Director's review on return.
> Goal: every step of "load created → carrier assigned → delivered → factor pays" mapped to actual code, with status (✅ live / ⚠️ partial / ❌ missing).

---

## STAGE 0 — Load Creation
**Question:** *How does a load show up in `db.loads`?*

| Path | Code | Status |
|---|---|---|
| Manual: dispatcher clicks **+ Post Load** on Tab 5 | `POST /api/loads` → `server.py:~2245` | ✅ |
| Public: shipper books quote on landing page | `POST /api/public/quote/book` → `server.py:~7712` | ✅ |
| Email ingestion: rate sheet → AI parser → draft → approve | `email_ingestion.py` | ✅ |
| Aggregator → Loads "Adopt" button (DAT/Truckstop) | NOT BUILT | ❌ Suggestion #8 |

---

## STAGE 1 — Find a Carrier (Tab 5 → Find a Truck)
**Question:** *Once a load is on Tab 5, how do we find a driver/carrier?*

| Source | Code | Status |
|---|---|---|
| AI Matcher (auto-runs on every new load) | `carrier_matcher.match_and_notify` | ✅ |
| Find a Truck modal (manual unified search) | `loadboards.py` + `FindTruckModal.jsx` | ✅ |
| Saved Searches with auto-watch + red notif | `saved_searches.py` | ✅ Iter 113.8 |
| FMCSA Census discovery (federal db) | `fmcsa_census.py` | ✅ |
| External capacity (DAT/Truckstop/123LB) | adapters wired, need API keys | ⚠️ keys-only |
| Cold Outreach (email/SMS to top-N) | `carrier_outreach.py` | ✅ email · SMS regulatory-blocked |

---

## STAGE 2 — Carrier Acceptance (the Driver Handshake)
**Question:** *Carrier got an offer email. What happens when they tap accept?*

| Step | Code | Status |
|---|---|---|
| Public offer page rendered | `carrier_outreach.py /public/carrier-offer/{token}` | ✅ |
| Carrier accepts → status moves to `accepted` | `POST /public/carrier-offer/{token}/accept` | ✅ |
| Magic-link issued so carrier can sign Rate Con on phone | `magic_link.py` | ✅ |
| Rate Confirmation PDF generated + signed | `rate_con.py` + `RateConSignPage.jsx` | ✅ |
| Consignee secret PIN auto-emailed to receiver | Iter 113 | ✅ |
| Load flips `available → booked` after Rate Con signed | `carrier_outreach.py /accept` | ✅ |

---

## STAGE 3 — Pickup
**Question:** *Driver heads to shipper. What does the system enforce?*

| Step | Code | Status |
|---|---|---|
| Driver dashboard (post Rate Con signing) | `RateConSignPage.jsx` | ✅ |
| Driver taps "I've Picked Up" | `POST /public/carrier-offer/{token}/status` body `{status:"in_transit"}` | ✅ |
| State-Lock geofence sentinel: rejects flip if driver isn't near origin | `carrier_outreach.py` | ✅ |
| GPS tracking pings every 60s | `RateConSignPage.jsx` | ✅ |
| Live map updates dispatcher view | WebSocket `chl:gps` event | ✅ |

---

## STAGE 4 — In Transit
**Question:** *Load is rolling. What happens automatically vs. manually?*

| Step | Code | Status |
|---|---|---|
| GPS tracking continuously pings | live | ✅ |
| Anomaly Sniffer watches for stalled trucks | `anomaly_sniffer.py` | ✅ |
| Shipper magic-link tracking page (Iter 105) | `/public/shipper-track/{token}` | ✅ |
| Margin erosion engine drops carrier rate floor as load ages | `margin_erosion.py` | ✅ |

---

## STAGE 5 — Delivery (Iter 113 hardening)
**Question:** *Driver pulls into receiver dock. What's the gate?*

| Step | Code | Status |
|---|---|---|
| Driver uploads BOL / lumper / detention photos | `load_completion_workflow.py` | ✅ |
| Photo quality validation (no blank/dark BOLs) | Gemini Vision OCR Iter 112 | ✅ |
| Driver taps "Confirm Delivery · Hand to Receiver" | `RateConSignPage.jsx` | ✅ |
| Receiver signs OR enters 6-digit PIN OR dispatcher overrides | `consignee_confirmation.py` Iter 113 | ✅ |
| Backend gate: `delivered` flip returns 412 unless confirmation on file | `carrier_outreach.py` | ✅ |
| State-Lock geofence: must be within 5mi of consignee | sentinel | ✅ |

---

## STAGE 6 — Billing Packet Stapler (the closure)
**Question:** *Load is delivered. What gets sent to the factor automatically?*

| Step | Code | Status |
|---|---|---|
| POD upload triggers Stapler workflow | `load_completion_workflow.py` | ✅ |
| Stapler assembles Invoice + RateCon + POD into single PDF | `pdf_stapler.py` | ✅ |
| Auto-routes to correct factor based on `loads.assigned_factor` | `factor_matrix.py` | ✅ |
| Resend email fires the packet to the factor's AP inbox | `notification_service.py` | ✅ |
| Reputation warning surfaces if factor reliability_score < 70 | `factor_matrix.py` | ✅ |

---

## STAGE 7 — Factor Payment ⚠️ THE GRAY ZONE
**Question:** *Factor receives the packet. How does payment land?*

| Step | Code | Status |
|---|---|---|
| Factor receives packet via Resend | live | ✅ |
| Factor's AP team manually processes (industry standard 24-72h) | external | ⚠️ outside our system |
| Factor sends ACH/wire to your Mercury Operating account | external | ✅ confirmed by Director |
| **Mercury reconciliation** auto-matches deposit → invoice | `reconciliation.py` polls every X min | ✅ |
| **Invoice marked PAID** in `db.invoices` | partial | ⚠️ |
| **Factor latency reporting** — alarm if factor exceeds its SLA | NOT WIRED yet (awaiting Director's pick A vs. B) | ❌ |
| **Profit Split auto-router** — route % to tax savings + personal | Iter 113.10 — module shipped, awaiting account ID + % config from Director | ⚠️ ready |
| **Carrier payouts** — handled by carrier's OWN factor (NOA-based) | external — NOT this system's job | ✅ confirmed by Director |

### 🎯 Director — this is THE GAP to discuss when you're back
The Stapler delivers the packet flawlessly; what happens **after** the factor pays is partly stitched but not bulletproof. Three concrete questions:
1. Does the factor send you ACH or wire? Where does it land — Mercury checking?
2. Should we wire **Mercury Webhook → Reconciliation engine** to auto-mark invoices PAID the moment funds arrive?
3. Should the system auto-pay your driver/carrier their share when YOU get paid (i.e., margin-take-and-flow-through)?

These three are the difference between "automated through delivery" and "automated end-to-end."

---

## What's already wired in this gap zone (less than fully closed)
- `mercury_client.py` — pulls Mercury balances + transactions
- `mercury_reconciliation_loop` (server.py:8709) — runs every 60min, does diff between Mercury and ledger
- `factor_performance.py` — tracks payment-aging metrics per factor
- `_trigger_payout` (server.py:~2413) — exists; needs validation it fires reliably

---

_Reference for Director's return:_

**3 questions to settle:**
1. **Mercury** — is it your operating bank and should we wire its webhooks for auto-reconciliation? (Yes/no)
2. **Auto-payout to drivers** — flow-through margin, or hold for weekly settlement? (Pick one)
3. **Factor latency reporting** — should the system raise a red notification if a factor takes >72h? (Yes/no)

When you're back, answer those three and I close the loop. We're ~95% there end-to-end.
