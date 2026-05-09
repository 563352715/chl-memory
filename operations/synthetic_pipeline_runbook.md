# CHL Synthetic Pipeline Test Runbook

**Last updated:** 2026-05-09
**Operator-authorized:** 2026-05-09 (Phase 1 ship)
**Owner:** dev Claude (working dir `C:\CHL\`)

This runbook documents the synthetic load pipeline test apparatus so future Claude sessions don't re-discover the wiring. Operator note 2026-05-09: "We have already done this test a long time ago, but results were never documented."

---

## What's working today (2026-05-09)

### Outbound email — Zoho SMTP from dispatch@continentalhaul.com
- `backend/server.py` `send_email()` at line ~8009
- Provider order: `RESEND_API_KEY` if set → `ZOHO_IMAP_PASSWORD` via SMTP_SSL on `smtp.zoho.com:465` → mock
- Sender identity: `Continental Haul Logistics <dispatch@continentalhaul.com>` (real broker FROM)
- Verified send to `jasonandpugee@gmail.com` 2026-05-09 (commit 92fc918, LLL5)
- Smoke test script: `C:\CHL\scripts\test_zoho_smtp.py`
- Password lives in `.env` as `ZOHO_IMAP_PASSWORD` (used for both IMAP polling AND SMTP outbound — same Zoho creds work for both)

### Synthetic load runner — `C:\CHL\scripts\synthetic_pipeline_runner.py`
- Phase 1: load creation + email send + telemetry
- CLI flags:
  - `--single` (default) — emit one synthetic load
  - `--burst N` — emit N loads as fast as possible
  - `--cadence-min M` — continuous, one load every M minutes (SIGINT-clean)
  - `--purge --yes` — delete all `synthetic: True` rows from db.loads, db.bookings, db.synthetic_test_runs
  - `--status` — per-stage counts across runs
  - `--report` — write markdown report to `chl-memory/test_runs/synthetic_<timestamp>.md`
- Variable inputs: 33-city freight-metro origin pool, weighted equipment (van .55 / reefer .25 / flatbed .12 / stepdeck .05 / RGN .03), $1.80-$3.50/mi rate, 100-2500 mi distance
- Destination hardcoded: **618 W Greenwood St, Springfield, MO 65807** (CHL principal address)
- All loads tagged `synthetic: True` + `synthetic_run_id` + `dry_run: True` for safe purge

### Carrier offer page — `/offer/{token}`
- Frontend route: `frontend/src/views/CarrierOfferPage.jsx` (mounted via App.js path matching at line ~17875)
- Backend public APIs at `backend/carrier_outreach.py`:
  - `GET /api/public/carrier-offer/{token}` — read offer
  - `POST /api/public/carrier-offer/{token}/accept` — carrier accepts
  - `POST /api/public/carrier-offer/{token}/status` — driver state updates
  - `POST /api/public/carrier-offer/{token}/pod-upload` — POD file upload
  - `POST /api/public/carrier-offer/{token}/gps-ping` — driver GPS pings
  - `POST /api/public/carrier-offer/{token}/relinquish` — carrier drops the load
- **No counter-offer endpoint yet** (Phase 2 work)

### Outreach mint — `/api/broker/loads/{id}/cold-outreach`
- Mints `carrier_offer_token` + writes to `db.carrier_offer_tokens`
- Calls `send_sms` + `send_email` helpers
- Returns `offer_url = {FRONTEND_URL}/offer/{token}`
- Set `FRONTEND_URL=http://127.0.0.1:3000` for local-only or a public tunnel URL (cloudflared) for phone-clickable links

---

## How to run a Phase 1 test

```powershell
# 1. Verify backend running
Get-Service CHL-Backend | Format-Table Name, Status

# 2. Verify FRONTEND_URL set
Select-String -Path C:\CHL\backend\.env -Pattern "^FRONTEND_URL"
# Expected: FRONTEND_URL=http://127.0.0.1:3000  (or tunnel URL)

# 3. Verify Zoho creds set
Select-String -Path C:\CHL\backend\.env -Pattern "^ZOHO_IMAP_PASSWORD"

# 4. Single-shot smoke
$env:PYTHONIOENCODING = "utf-8"
& "C:\CHL\.venv-local\Scripts\python.exe" C:\CHL\scripts\synthetic_pipeline_runner.py --single

# 5. Operator opens email in jasonandpugee@gmail.com,
#    clicks offer URL on this Windows PC's browser (or phone if tunnel set),
#    accept/reject in PWA.

# 6. Check telemetry
& "C:\CHL\.venv-local\Scripts\python.exe" C:\CHL\scripts\synthetic_pipeline_runner.py --status

# 7. Cleanup
& "C:\CHL\.venv-local\Scripts\python.exe" C:\CHL\scripts\synthetic_pipeline_runner.py --purge --yes
```

---

## Known gaps (deferred to later phases)

### Phase 2 — counter-offer flow (NOT BUILT)
- Frontend: counter-offer button + rate input on `CarrierOfferPage.jsx`
- Backend: `POST /api/public/carrier-offer/{token}/counter-offer` endpoint
- Mongo: `db.load_offer_negotiations` collection per round
- Broker side: notification (in-app + email) when counter arrives, accept/decline/counter-counter UI
- Cap: 3 rounds, then auto-decline
- Estimate: ~3-4 hours focused build

### Phase 3 — synthetic document flow (NOT BUILT)
- BOL/POD stub PDFs (watermarked "SYNTHETIC TEST")
- Operator uploads at pickup/delivery via PWA `pod-upload` endpoint
- Doc-gate enforcement test: try mark "delivered" without POD → must be blocked
- Estimate: ~3 hours

### Phase 4 — edge cases (NOT BUILT)
- Cancellation injection, driver no-show, factor chargeback (stub), customer dispute
- Per-stage latency metrics
- Sovereign Pulse Monitor real-entropy validation under synthetic load
- Estimate: ~2 hours

### Phone-clickable links — cloudflared tunnel (NOT INSTALLED)
- Need a public `https://*.trycloudflare.com` URL → operator's phone can click
- Cloudflared free quick tunnel, no signup, ~15MB binary
- Install path: `winget install --id Cloudflare.cloudflared` OR direct download
- One-shot tunnel: `cloudflared tunnel --url http://localhost:3000`
- Update `FRONTEND_URL` to the tunnel URL, restart backend
- Tunnel URLs are ephemeral (cycle on tunnel restart) — fine for testing, not for production

---

## Why this matters

Operator solo-builds on a 5-hour cap cycle with multiple Claude contexts. Without this runbook, every new session re-discovers the email-provider wiring (Resend vs Zoho vs Gmail), the synthetic-load schema, and the carrier-offer route surface — burning 30-60 minutes per session that should have been new work.

Prior sessions verified Zoho SMTP works (operator quote 2026-05-09) but didn't write it down. This runbook is the corrective. Update on every change.

---

## Audit log

| Date | Change | Commit |
|---|---|---|
| 2026-05-09 | Phase 1 runner + Zoho SMTP fallback shipped | 92fc918 (LLL5) |
| 2026-05-09 | FRONTEND_URL=http://127.0.0.1:3000 provisioned | env-only (not committed) |
| 2026-05-09 | Joplin→Springfield outreach footer fix | 7064c4b (LLL4) |
