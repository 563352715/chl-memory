# Full driver experience — API contracts + component specs

**Operator-authorized 2026-05-09. Build target: ~3-4 hours via 4 parallel agents.**

This doc is the contract that every parallel sub-agent works to. If agents produce work that diverges from these signatures, the integration step will reject it.

---

## Backend endpoints (all under `backend/carrier_outreach.py`)

All public, token-authed (token in URL path validates against `db.carrier_offer_tokens`). No JWT required.

### 1. `POST /api/public/carrier-offer/{token}/advance-status`

Driver advances the load through the state machine.

**State machine:**
```
booked → at_pickup → loaded → en_route → at_delivery → unloaded → delivered
```

Each transition can be reversed only by dispatch (broker side, not via this endpoint).

**Body:**
```json
{
  "new_status": "at_pickup",
  "lat": 39.105,
  "lng": -84.512,
  "note": "Arrived at Acme DC, gate 4"
}
```

**Response 200:**
```json
{
  "ok": true,
  "from_status": "booked",
  "to_status": "at_pickup",
  "transitioned_at": "2026-05-09T20:31:34.708318+00:00",
  "load_event_id": "uuid",
  "next_action": {
    "kind": "upload_bol" | "none" | "upload_pod" | "request_consignee_signature",
    "label": "Upload BOL photo to confirm pickup"
  }
}
```

**Errors:** 400 invalid_transition, 404 token_not_found, 409 already_at_status

### 2. `GET /api/public/carrier-offer/{token}/post-delivery`

Aggregated data for the post-delivery panel.

**Response 200:**
```json
{
  "delivery": {
    "delivered_at": "2026-05-09T20:49:48+00:00",
    "miles_actual": 498,
    "on_time": true,
    "delivery_window_end": "..."
  },
  "payment": {
    "rate": 900.0,
    "currency": "USD",
    "net_terms_days": 30,
    "expected_paid_at": "2026-06-08T00:00:00+00:00",
    "factor_name": "TBD Factoring Partner",
    "instant_pay_eligible": false,
    "instant_pay_url": null
  },
  "documents": [
    {"type": "rate_confirmation", "title": "Rate Confirmation", "url": "/api/public/carrier-offer/{token}/synthetic-docs", "signed": true},
    {"type": "bol", "title": "Bill of Lading", "url": "...", "signed": true},
    {"type": "pod", "title": "Proof of Delivery", "url": "...", "signed": true}
  ],
  "next_loads": [
    {"id": "...", "reference_number": "LD-...", "origin": "Springfield, MO", "destination": "Dallas, TX", "rate": 1200, "miles": 540, "equipment": "dry_van", "rate_per_mile": 2.22}
  ],
  "dispatch_phone": "(417) 219-3856",
  "dispatch_email": "dispatch@continentalhaul.com"
}
```

### 3. `GET /api/public/carrier-offer/{token}/suggest-next-loads?lat=&lng=&equipment=&radius_mi=300`

Returns up to 5 available loads near the carrier's current location, sorted by RPM desc.

**Response 200:**
```json
{"loads": [{"id":"...", "reference_number":"...", ...}], "count": 5, "search_radius_mi": 300}
```

### 4. `POST /api/public/carrier-offer/{token}/empty-truck-post`

Carrier advertises empty capacity for a return-home load.

**Body:**
```json
{
  "current_lat": 37.180,
  "current_lng": -93.290,
  "current_city": "Springfield",
  "current_state": "MO",
  "available_at": "2026-05-10T00:00:00+00:00",
  "target_state": "OH",
  "target_city": "Cincinnati",
  "equipment": "reefer",
  "notes": "Deadhead home"
}
```

**Response 201:**
```json
{"ok": true, "post_id": "uuid", "expires_at": "2026-05-12T00:00:00+00:00"}
```

Stored in new collection `db.empty_truck_posts`.

### 5. `POST /api/public/carrier-offer/{token}/report-incident`

Driver reports a problem during the load.

**Body:**
```json
{
  "kind": "delay" | "breakdown" | "detention" | "accident" | "refused" | "damage" | "missing_paperwork",
  "note": "Detained 90 min at consignee dock 3",
  "lat": 39.105,
  "lng": -84.512
}
```

**Response 200:**
```json
{"ok": true, "incident_id": "uuid", "dispatch_notified": true, "dispatch_notification_channel": "in_app|email|sms"}
```

Logged to `db.load_events` with `event_kind=incident`.

---

## Database collections

### `db.load_events` (NEW — owned by Stream A)

Every state transition, GPS ping batch summary, upload, incident, and counter-offer round writes one row here. Used by the dispatcher map, audit log, and Stream D's telemetry watcher.

```json
{
  "id": "uuid",
  "load_id": "str",
  "token": "str",
  "event_kind": "status_change|gps_ping_batch|upload|incident|counter_offer",
  "from_status": "str|null",
  "to_status": "str|null",
  "details": {},
  "lat": "float|null",
  "lng": "float|null",
  "created_at": "iso",
  "uploaded_by": "str"
}
```

### `db.empty_truck_posts` (NEW — owned by Stream A)

```json
{
  "id": "uuid",
  "carrier_id": "str",
  "load_id_just_completed": "str",
  "current_lat": "float",
  "current_lng": "float",
  "current_city": "str",
  "current_state": "str",
  "available_at": "iso",
  "target_state": "str|null",
  "target_city": "str|null",
  "target_lat": "float|null",
  "target_lng": "float|null",
  "equipment": "str",
  "notes": "str",
  "posted_via": "carrier_offer_token",
  "expires_at": "iso",
  "created_at": "iso"
}
```

---

## Frontend components

All in `frontend/src/views/` unless noted. Mobile-first. Tailwind classes. No new package dependencies except Leaflet.

### `<LiveLoadView token={token} offer={offer} onTransition={(newStatus) => void} />`

NEW file `frontend/src/views/LiveLoadView.jsx`.

Renders when `offer.load.status` is in `{booked, at_pickup, loaded, en_route, at_delivery, unloaded}`. Sections:

1. **Top bar** — load reference, current status pill, ETA badge
2. **Map** (`<LiveMapView>`) — full-width, ~40% viewport height
3. **Status pills row** (`<StatusPills>`) — 5-step progress with tap-to-advance
4. **Stop details panel** — current next stop's address, contact, hours, special instructions
5. **Quick actions row** — call shipper / call consignee / call dispatch / message dispatch / report problem
6. **HOS hours** — local-state-only display (synthetic-test scope; real HOS comes later)

Polls `/api/public/carrier-offer/{token}/gps-ping` every 30s when in motion (en_route status only) using browser geolocation API.

### `<LiveMapView origin={...} destination={...} currentGps={...} pings={[...]} />`

NEW file `frontend/src/views/LiveMapView.jsx`.

Leaflet + OpenStreetMap tiles (free, no API key). 320px+ height responsive. Origin/destination pins, polyline route (straight line OK for Phase 1; OSRM routing optional follow-up), current GPS marker. Auto-pan to fit bounds on prop change.

**Adds `leaflet` to `frontend/package.json` deps.** No yarn install needed in CI; yarn add at integration time.

### `<StatusPills currentStatus={str} onAdvance={fn} disabled={bool} pendingTransition={bool} />`

NEW file `frontend/src/views/StatusPills.jsx`.

Renders 5 pills horizontal: At Pickup → Loaded → En Route → At Delivery → Unloaded. Current = filled yellow. Past = filled green. Future = outlined gray. Tap-to-advance on next pill only.

### `<PostDeliveryView token={token} offer={offer} />`

NEW file `frontend/src/views/PostDeliveryView.jsx`.

Renders when `offer.load.status === "delivered"`. Sections:

1. **Delivery confirmation card** — green checkmark, delivered_at timestamp, miles, on-time badge
2. **Payment card** — rate, payment terms, expected_paid_at, factor name; "instant pay" CTA if eligible
3. **Documents archive** — extends existing `<SyntheticDocsCarousel>` pattern, shows real signed docs (rate-con, BOL, POD)
4. **Find your next load card** — list of `<NextLoadCard>` x up to 3
5. **Empty truck post card** — `<EmptyTruckPostForm>` triggered by "Post my empty truck" button
6. **Tap-to-call dispatch** — big button with `tel:` link
7. **Booking ID + reference** at bottom

### `<NextLoadCard load={...} onAccept={fn} />`

NEW file `frontend/src/views/NextLoadCard.jsx`.

Single-load summary card: lane, rate, RPM, miles, equipment, pickup window. "View load" button takes them to `/offer/{newToken}` if available, else "Request rate" sends a dispatch message.

### `<EmptyTruckPostForm token={token} currentCity={str} currentState={str} equipment={str} onSubmit={fn} />`

NEW file `frontend/src/views/EmptyTruckPostForm.jsx`.

Form: target state dropdown, target city input, available_at datetime, notes textarea. Submit calls `POST /empty-truck-post`. Shows success card on completion.

### `<CarrierOfferPage>` integration

EDIT `frontend/src/views/CarrierOfferPage.jsx`. Routing logic:

```jsx
if (offer.load.status === "delivered") return <PostDeliveryView ... />;
if (offer.load.status in {booked, at_pickup, loaded, en_route, at_delivery, unloaded}) return <LiveLoadView ... />;
// Existing offer-page open/closed branches stay
```

**REMOVE** the broken button at line 877-882 (`Open CHL Driver App → window.location.href = "/"`). The new `<PostDeliveryView>` replaces its purpose.

---

## Stream ownership

| Stream | Owner | Files | Don't touch |
|---|---|---|---|
| A | Backend agent 1 | `carrier_outreach.py` (new endpoints), `load_state_machine.py` (new) | Frontend |
| B | Frontend agent 1 | `LiveLoadView.jsx`, `LiveMapView.jsx`, `StatusPills.jsx`, `package.json` (leaflet only) | `CarrierOfferPage.jsx` |
| C | Frontend agent 2 | `PostDeliveryView.jsx`, `NextLoadCard.jsx`, `EmptyTruckPostForm.jsx` | `CarrierOfferPage.jsx` |
| D | Backend agent 2 | `scripts/synthetic_state_watcher.py` (new) | All else |
| E | Integration (manual / orchestrator) | `CarrierOfferPage.jsx` (route logic + button removal), smoke test | Components built in B/C |

Streams A/B/C/D can run in parallel. Stream E integrates after all return.

---

## Acceptance criteria (per stream)

**Stream A:** All 5 endpoints respond with documented JSON shapes when called via httpx. State machine rejects invalid transitions with 400. `db.load_events` row written on every transition.

**Stream B:** `<LiveLoadView>` renders cleanly on mobile viewport. Status pill tap fires `onTransition`. Map renders Origin + Destination + current GPS pin. No console errors.

**Stream C:** `<PostDeliveryView>` renders all 7 sections cleanly on mobile viewport. Empty truck form submits successfully. Next-load cards click-through.

**Stream D:** `synthetic_state_watcher.py` polls db.loads every 30s, detects `synthetic_run_id` rows with status changes, writes telemetry rows to `db.synthetic_test_runs`. Idempotent (won't double-write on restart).

**Stream E:** Operator can complete a full synthetic load cycle (accept → at_pickup → loaded → en_route → at_delivery → unloaded → delivered → post-delivery panel) on phone via tunnel URL. Broken `/` button is gone.

---

## Audit log

| Date | Change | Commit |
|---|---|---|
| 2026-05-09 | Contracts locked, agents dispatched | (pending) |
