# CHL Asset Inventory -- Orphans + Broken

_Generated: 2026-05-11T06:59:52.849183+00:00_

> **Triage focus.** These files are present in the codebase but
> are NOT reachable from `backend/server.py` or `frontend/src/App.js`,
> have no smoke test wired into a known harness, and are not
> imported by anything we could find.
>
> **Action:** triage each one as one of:
> - **Wire it up** -- the work is done, just needs registration
> - **Delete it** -- abandoned or superseded by another module
> - **Keep silent** -- legitimate dev-only utility (mark as DEV-TOOL)

## BROKEN (0)

_None — no parse errors or unresolved internal imports detected._

## ORPHAN (61)

### `frontend/src/lib/manualOverrideFields.js` (211 LOC)

- **Subsystem:** Misc
- **Type:** js_module
- **Last modified:** 2026-05-04T22:04:40.008731+00:00
- **Docstring:** Copyright (c) 2026 Continental Haul Logistics LLC. All Rights Reserved.
- **Note:** Not reachable from frontend/src/App.js
- **Exports:** `BCA_FIELDS, BROKER_INVOICE_FIELDS, CARRIER_VET_FIELDS, QUOTE_EMAIL_FIELDS, RATE_CON_FIELDS`

### `frontend/src/components/ui/menubar.jsx` (198 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.998909+00:00
- **Note:** Not reachable from frontend/src/App.js

### `frontend/src/components/ui/carousel.jsx` (193 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.994428+00:00
- **Note:** Not reachable from frontend/src/App.js

### `backend/.send_truckstop_api_inquiry.py` (180 LOC)

- **Subsystem:** Communications
- **Type:** python_module
- **Last modified:** 2026-05-11T05:45:45.203714+00:00
- **Docstring:** Send comprehensive API access inquiry to Truckstop.com. Operator-mandated 2026-05-11. Per feedback_outbound_email_policy.md: signed as 'Dispatch / Continental Haul Logistics LLC' (no AI/tech-company r
- **Note:** No imports, no smoke test, not in LIVE chain
- **Exports:** `send_via_zoho(), main()`

### `backend/.send_dat_api_inquiry.py` (176 LOC)

- **Subsystem:** Misc
- **Type:** python_module
- **Last modified:** 2026-05-11T05:37:50.054307+00:00
- **Docstring:** Send comprehensive API access inquiry to DAT developer support. Operator-mandated 2026-05-11. Comprehensive list of questions to answer BEFORE committing to a DAT subscription tier. Per feedback_outbo
- **Note:** No imports, no smoke test, not in LIVE chain
- **Exports:** `send_via_zoho(), main()`

### `scripts/cloud_replica/init_replica_set.js` (174 LOC)

- **Subsystem:** Scripts / Tooling
- **Type:** js_module
- **Last modified:** 2026-05-10T23:19:49.855063+00:00
- **Docstring:** init_replica_set.js -- Initialize CHL mongo replica set from the primary.
- **Note:** Not reachable from frontend/src/App.js

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

### `frontend/src/views/MarginFloorModal.jsx` (152 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-05T15:44:03.406156+00:00
- **Docstring:** * MarginFloorModal.jsx — Iter 139.37 * * Two-mode modal that renders either: * • HARD BLOCK — no override possible, "Got it, change rate" close button only * • SOFT WARN — explicit "Yes, dispatch anyw
- **Note:** Not reachable from frontend/src/App.js
- **Exports:** `MarginFloorModal`

### `backend/.smoke_3_layer_parsing.py` (149 LOC)

- **Subsystem:** Synthetic Test
- **Type:** python_module
- **Last modified:** 2026-05-10T21:17:41.816360+00:00
- **Docstring:** End-to-end smoke test of the 3-layer parsing stack against REAL captured public HTML. Tests Layer B (BS4 deterministic) and Layer C (Claude LLM fallback) against 3 captured fixtures from Agent 1's dis
- **Note:** No imports, no smoke test, not in LIVE chain
- **Exports:** `test_layer_b(), test_layer_c(), main()`

### `backend/.send_123loadboard_api_request.py` (141 LOC)

- **Subsystem:** Communications
- **Type:** python_module
- **Last modified:** 2026-05-11T05:29:33.928497+00:00
- **Docstring:** Send API integration request to 123Loadboard partner-integrations team. Operator-mandated 2026-05-11. Per feedback_outbound_email_policy.md: signed as 'Dispatch / Continental Haul Logistics LLC' (no A
- **Note:** No imports, no smoke test, not in LIVE chain
- **Exports:** `send_via_zoho(), main()`

### `backend/.smoke_vault_files.py` (135 LOC)

- **Subsystem:** Synthetic Test
- **Type:** python_module
- **Last modified:** 2026-05-10T20:11:37.485114+00:00
- **Docstring:** Smoke test for vault file-storage feature. Validates: encrypt/decrypt round-trip + Mongo folder/file CRUD against the live database. Bypasses the HTTP layer (we already proved the routes are wired by 
- **Note:** No imports, no smoke test, not in LIVE chain
- **Exports:** `main()`

### `frontend/src/components/ui/form.jsx` (133 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.996423+00:00
- **Note:** Not reachable from frontend/src/App.js

### `backend/.send_comfreight_followup.py` (132 LOC)

- **Subsystem:** Communications
- **Type:** python_module
- **Last modified:** 2026-05-11T05:19:22.014748+00:00
- **Docstring:** Send factoring application follow-up email to ComFreight Haul Pay. Operator-mandated 2026-05-11. Constraints: - Signed as 'Dispatch' (no AI-reveal, no platform-reveal, matches feedback_outbound_email_
- **Note:** No imports, no smoke test, not in LIVE chain
- **Exports:** `send_via_zoho(), log_outbound(), main()`

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

### `backend/conftest.py` (94 LOC)

- **Subsystem:** Synthetic Test
- **Type:** python_module
- **Last modified:** 2026-05-10T23:19:48.589821+00:00
- **Docstring:** Pytest conftest — Iter 145.x test environment isolation ======================================================== Operator-mandated 2026-05-10: synthetic load tests + smoke tests must NEVER write to th
- **Note:** No imports, no smoke test, not in LIVE chain
- **Exports:** `chl_env_info()`

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

### `backend/.run_playwright_with_vault_creds.py` (72 LOC)

- **Subsystem:** Misc
- **Type:** python_module
- **Last modified:** 2026-05-11T00:31:48.757053+00:00
- **Docstring:** Decrypt operator owner creds from vault + run playwright authed E2E. The password never crosses the conversation log: - Decrypted via Fernet into a local variable - Passed to subprocess via env dict (
- **Note:** No imports, no smoke test, not in LIVE chain
- **Exports:** `get_creds(), run_pytest(), main()`

### `frontend/src/components/ui/calendar.jsx` (71 LOC)

- **Subsystem:** Frontend UI
- **Type:** jsx_component
- **Last modified:** 2026-05-04T22:04:39.993431+00:00
- **Note:** Not reachable from frontend/src/App.js

### `backend/.run_playwright_ui_sweep_with_vault_creds.py` (68 LOC)

- **Subsystem:** Misc
- **Type:** python_module
- **Last modified:** 2026-05-11T00:51:04.692168+00:00
- **Docstring:** Decrypt operator owner creds from vault + run the UI-sweep test. Mirrors `.run_playwright_with_vault_creds.py` — same security model: - Decrypted password lives only in this process's memory + a subpr
- **Note:** No imports, no smoke test, not in LIVE chain
- **Exports:** `get_creds(), run_pytest(), main()`

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

### `frontend/src/constants/equipmentTypes.js` (36 LOC)

- **Subsystem:** Misc
- **Type:** js_module
- **Last modified:** 2026-05-10T22:18:27.278737+00:00
- **Docstring:** Copyright (c) 2026 Continental Haul Logistics LLC. All Rights Reserved. Proprietary and Confidential.
- **Note:** Not reachable from frontend/src/App.js
- **Exports:** `CANONICAL_EQUIPMENT_TYPES, EQUIPMENT_LABELS, RATE_PER_MILE_MAX, RATE_PER_MILE_MIN`

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
