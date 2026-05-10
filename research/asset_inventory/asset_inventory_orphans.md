# CHL Asset Inventory -- Orphans + Broken

_Generated: 2026-05-10T06:49:11.810158+00:00_

> **Triage focus.** These files are present in the codebase but
> are NOT reachable from `backend/server.py` or `frontend/src/App.js`,
> have no smoke test wired into a known harness, and are not
> imported by anything we could find.
>
> **Action:** triage each one as one of:
> - **Wire it up** -- the work is done, just needs registration
> - **Delete it** -- abandoned or superseded by another module
> - **Keep silent** -- legitimate dev-only utility (mark as DEV-TOOL)

## BROKEN (1)

### `backend/tonu_calculator.py` (248 LOC)

- **Subsystem:** Misc
- **Last modified:** 2026-05-04T22:18:38.672528+00:00
- **Issue:** Parse failure: SyntaxError: invalid non-printable character U+FEFF at line 1

## ORPHAN (63)

### `backend/fmcsa_sms_scores.py` (621 LOC)

- **Subsystem:** Communications
- **Type:** python_module
- **Last modified:** 2026-05-07T18:58:27.934759+00:00
- **Docstring:** FMCSA SMS BASIC Safety Scores per-carrier scraper (iter 142.1 nationwide-vetted-carrier-DB Phase 2). Fetches Behavior Analysis & Safety Improvement Categories (BASIC) percentile scores, inspections, a
- **Note:** No imports, no smoke test, not in LIVE chain
- **Exports:** `fetch_sms_overview_html(), fetch_basic_detail_html(), parse_sms_overview(), parse_basic_detail(), fetch_full_sms_for_carrier(), update_carrier_sms_in_db(), refresh_sms_batch()`

### `backend/scripts/purge_test_data.py` (221 LOC)

- **Subsystem:** Synthetic Test
- **Type:** python_module
- **Last modified:** 2026-05-04T22:04:39.900731+00:00
- **Docstring:** Legacy Seed-Data Purge Script ============================== Clears test accounts, $0 loads, stale hunt events, and orphaned artifacts from production MongoDB so the God-View / Live Board / Market Rad
- **Note:** No imports, no smoke test, not in LIVE chain
- **Exports:** `is_test_reference(), is_test_email(), is_test_shipper(), run()`

### `frontend/src/views/BankerDigestPage.jsx` (212 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:40.012722+00:00
- **Note:** Not reachable from frontend/src/App.js
- **Exports:** `BankerDigestPage`

### `frontend/src/lib/manualOverrideFields.js` (211 LOC)

- **Subsystem:** Misc
- **Type:** js_module
- **Last modified:** 2026-05-04T22:04:40.008731+00:00
- **Docstring:** Copyright (c) 2026 Continental Haul Logistics LLC. All Rights Reserved.
- **Note:** Not reachable from frontend/src/App.js
- **Exports:** `BCA_FIELDS, BROKER_INVOICE_FIELDS, CARRIER_VET_FIELDS, QUOTE_EMAIL_FIELDS, RATE_CON_FIELDS`

### `backend/self_heal_audit.py` (207 LOC)

- **Subsystem:** Compliance
- **Type:** python_module
- **Last modified:** 2026-05-04T22:04:39.901729+00:00
- **Docstring:** self_heal_audit.py — Iteration 80.1. Parses /app/logs/telemetry.json (one JSON object per line — append-only from the Sentinel Sentry endpoint) and produces a clustered Fix-Suggestion report at /app/l
- **Note:** No imports, no smoke test, not in LIVE chain
- **Exports:** `parse_log(), cluster(), suggest_fix(), render(), main()`

### `frontend/src/views/SyntheticDocsCarousel.jsx` (204 LOC)

- **Subsystem:** Synthetic Test
- **Type:** jsx_component
- **Last modified:** 2026-05-09T22:28:27.315617+00:00
- **Docstring:** Copyright (c) 2026 Continental Haul Logistics LLC. All Rights Reserved. Proprietary and Confidential.
- **Note:** Not reachable from frontend/src/App.js
- **Exports:** `SyntheticDocsCarousel`

### `frontend/src/components/ui/menubar.jsx` (198 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.998909+00:00
- **Note:** Not reachable from frontend/src/App.js

### `backend/.smoke_141_3_1b.py` (197 LOC)

- **Subsystem:** Communications
- **Type:** python_module
- **Last modified:** 2026-05-07T05:23:55.970328+00:00
- **Docstring:** .smoke_141_3_1b.py — iter 141.3 stage 1b smoke harness for plivo_client.py. Pre-written 2026-05-07 alongside plivo_client.py. Runs without the Plivo SDK installed (uses unittest.mock to stub out the p
- **Note:** No imports, no smoke test, not in LIVE chain
- **Exports:** `test_1_empty_body(), test_2_dry_run(), test_3_no_credentials(), test_4_no_phone_number(), test_5_successful_send(), test_6_plivo_not_installed(), main()`

### `frontend/src/components/ui/carousel.jsx` (193 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.994428+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/views/QuickEmailButton.jsx` (173 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:40.051254+00:00
- **Docstring:** Copyright (c) 2026 Continental Haul Logistics LLC. All Rights Reserved. Proprietary and Confidential.
- **Note:** Not reachable from frontend/src/App.js
- **Exports:** `QuickEmailButton`

### `frontend/src/components/DeliveryReceipt.jsx` (169 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.989777+00:00
- **Docstring:** Copyright (c) 2026 Continental Haul Logistics LLC. All Rights Reserved.
- **Note:** Not reachable from frontend/src/App.js
- **Exports:** `DeliveryReceipt`

### `frontend/src/components/ShadowModePill.jsx` (167 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.990209+00:00
- **Docstring:** Copyright (c) 2026 Continental Haul Logistics LLC. All Rights Reserved.
- **Note:** Not reachable from frontend/src/App.js
- **Exports:** `ShadowModePill`

### `frontend/src/components/ui/context-menu.jsx` (156 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.995425+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/dropdown-menu.jsx` (156 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.996423+00:00
- **Note:** Not reachable from frontend/src/App.js

### `backend/paperwork_autopilot.py` (155 LOC)

- **Subsystem:** Communications
- **Type:** python_module
- **Last modified:** 2026-05-04T22:04:39.889800+00:00
- **Docstring:** Paperwork Autopilot — when a load flips to `delivered`, automatically: 1. Generate an invoice record (if not already created). 2. Email it to the shipper with a pay-now link + attached PDF link. 3. Lo
- **Note:** No imports, no smoke test, not in LIVE chain
- **Exports:** `on_load_delivered()`

### `backend/security/secrets_cli.py` (153 LOC)

- **Subsystem:** Misc
- **Type:** python_module
- **Last modified:** 2026-05-08T21:01:53.322670+00:00
- **Docstring:** Operator CLI for the DPAPI-encrypted secrets store. Usage: python -m backend.security.secrets_cli set <KEY> python -m backend.security.secrets_cli get <KEY> python -m backend.security.secrets_cli dele
- **Note:** No imports, no smoke test, not in LIVE chain
- **Exports:** `build_parser(), main()`

### `frontend/src/views/MarginFloorModal.jsx` (152 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-05T15:44:03.406156+00:00
- **Docstring:** * MarginFloorModal.jsx — Iter 139.37 * * Two-mode modal that renders either: * • HARD BLOCK — no override possible, "Got it, change rate" close button only * • SOFT WARN — explicit "Yes, dispatch anyw
- **Note:** Not reachable from frontend/src/App.js
- **Exports:** `MarginFloorModal`

### `frontend/src/components/ui/form.jsx` (133 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.996423+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/A2PStatusBanner.jsx` (126 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-09T01:59:45.302250+00:00
- **Docstring:** Copyright (c) 2026 Continental Haul Logistics LLC. All Rights Reserved.
- **Note:** Not reachable from frontend/src/App.js
- **Exports:** `A2PStatusBanner`

### `frontend/src/components/RateMargin.jsx` (126 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.990209+00:00
- **Docstring:** * RateMargin.jsx — Inline rate display with CHL margin attached. * (Iter 134.22 — per Jason: "I want to see the calculation of CHL take at a glance.") * * Variants: * <RateMargin load={l} /> — full in
- **Note:** Not reachable from frontend/src/App.js
- **Exports:** `RateMargin`

### `backend/voice_operator.py` (124 LOC)

- **Subsystem:** Communications
- **Type:** python_module
- **Last modified:** 2026-05-07T02:51:51.116978+00:00
- **Docstring:** voice_operator.py — operator-facing Voice (robo-call) alert wrapper (iter 141.1). Sister module to text_operator.py. Same role (alert the platform owner) but uses Twilio Voice instead of SMS. Voice is
- **Note:** No imports, no smoke test, not in LIVE chain
- **Exports:** `voice_operator()`

### `frontend/src/components/ui/select.jsx` (119 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:40.000906+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/command.jsx` (116 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.995425+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/resources.js` (110 LOC)

- **Subsystem:** Misc
- **Type:** js_module
- **Last modified:** 2026-05-04T22:04:40.009731+00:00
- **Docstring:** Copyright (c) 2026 Continental Haul Logistics LLC. All Rights Reserved. Proprietary and Confidential.
- **Note:** Not reachable from frontend/src/App.js
- **Exports:** `FINANCIAL_LINKS, INDUSTRY_LINKS, MISSOURI_LINKS, REGULATORY_LINKS, STATE_DOT_LINKS, WEATHER_LINKS`

### `frontend/src/components/ui/sheet.jsx` (108 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:40.001904+00:00
- **Note:** Not reachable from frontend/src/App.js

### `backend/text_operator.py` (107 LOC)

- **Subsystem:** Communications
- **Type:** python_module
- **Last modified:** 2026-05-07T02:34:13.388360+00:00
- **Docstring:** text_operator.py — operator-facing SMS alert wrapper (iter 141.1). NOT for driver/carrier comms — that path lives in notification_service.py and is gated on A2P 10DLC APPROVED. This module is for *int
- **Note:** No imports, no smoke test, not in LIVE chain
- **Exports:** `text_operator()`

### `frontend/src/components/ui/navigation-menu.jsx` (104 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.998909+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/pagination.jsx` (100 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.998909+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/alert-dialog.jsx` (97 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.991209+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/breadcrumb.jsx` (92 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.993431+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/NotificationStatusDot.jsx` (90 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.990209+00:00
- **Docstring:** Copyright (c) 2026 Continental Haul Logistics LLC. All Rights Reserved.
- **Note:** Not reachable from frontend/src/App.js
- **Exports:** `NotificationStatusDot`

### `frontend/src/components/ui/drawer.jsx` (90 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.996423+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/table.jsx` (86 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:40.002901+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/toast.jsx` (85 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:40.003899+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/calendar.jsx` (71 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.993431+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/input-otp.jsx` (53 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.997422+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/card.jsx` (50 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.993431+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/alert.jsx` (47 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.991209+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/toggle-group.jsx` (43 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:40.004896+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/accordion.jsx` (41 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.991209+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/tabs.jsx` (41 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:40.003899+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/resizable.jsx` (40 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:40.000906+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/scroll-area.jsx` (38 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:40.000906+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/setupProxy.js` (38 LOC)

- **Subsystem:** Misc
- **Type:** js_module
- **Last modified:** 2026-05-09T21:50:50.067768+00:00
- **Docstring:** Local-dev only. CRA picks this up automatically when `yarn start` runs.
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/badge.jsx` (34 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.992432+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/avatar.jsx` (33 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.992432+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/toaster.jsx` (33 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:40.004896+00:00
- **Note:** Not reachable from frontend/src/App.js
- **Exports:** `Toaster`

### `frontend/src/components/ui/radio-group.jsx` (29 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.999909+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/popover.jsx` (27 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.999909+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/tooltip.jsx` (26 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:40.005893+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/hover-card.jsx` (23 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.997422+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/separator.jsx` (23 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:40.001904+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/checkbox.jsx` (22 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.994428+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/switch.jsx` (22 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:40.002901+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/progress.jsx` (21 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.999909+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/slider.jsx` (21 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:40.001904+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/lib/constants.js` (20 LOC)

- **Subsystem:** Misc
- **Type:** js_module
- **Last modified:** 2026-05-04T22:04:40.007887+00:00
- **Docstring:** Copyright (c) 2026 Continental Haul Logistics LLC. All Rights Reserved. Proprietary and Confidential.
- **Note:** Not reachable from frontend/src/App.js
- **Exports:** `EQUIPMENT_LABELS, STATUS_COLORS`

### `frontend/src/components/ui/input.jsx` (19 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.997422+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/lib/api.js` (19 LOC)

- **Subsystem:** Misc
- **Type:** js_module
- **Last modified:** 2026-05-04T22:04:40.006890+00:00
- **Docstring:** Copyright (c) 2026 Continental Haul Logistics LLC. All Rights Reserved. Proprietary and Confidential.
- **Note:** Not reachable from frontend/src/App.js
- **Exports:** `API, BACKEND_URL, SOCKET_PATH, formatApiErrorDetail`

### `frontend/src/components/ui/textarea.jsx` (18 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:40.003899+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/skeleton.jsx` (14 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:40.001904+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/collapsible.jsx` (9 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.994428+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/aspect-ratio.jsx` (5 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.992432+00:00
- **Note:** Not reachable from frontend/src/App.js
