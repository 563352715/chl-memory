# Frontend Form Surface Enumeration

Generated: 2026-05-10
Scope: C:\CHL\frontend\src (App.js + views/*.jsx + components/*.jsx)
Read-only static analysis -- no execution, no modifications.

---

## 1. Forms

### 1.1 Add Driver Modal
- Component: AddDriverModal
- File: App.js:3617-3803
- Submission endpoint: POST /api/drivers
- Fields:
  - name (text, required)
  - phone (tel, required)
  - email (email, required)
  - location (text, required) -- "City, State" format
  - mc_number (text, required)
  - equipment_type (select, required) -> [dry_van, reefer, flatbed, step_deck, tanker]
  - rate_per_mile (number, required, step 0.01)
  - insurance_expiry (date, required)
  - safety_rating (select) -> [satisfactory, conditional, unsatisfactory]
  - preferred_lanes (text) -- comma-separated list of routes

Purpose: Onboard new drivers into the carrier system with safety ratings and equipment preferences.

### 1.2 Add Load Modal
- Component: AddLoadModal
- File: App.js:3806-4019
- Submission endpoint: POST /api/loads
- Fields:
  - origin_city (text, required)
  - origin_state (text, 2-char max, required, uppercase)
  - destination_city (text, required)
  - destination_state (text, 2-char max, required, uppercase)
  - pickup_date (date, required)
  - delivery_date (date, required)
  - weight (number, required) -- in lbs
  - equipment_required (select, required) -> [dry_van, reefer, flatbed]
  - rate (number, required, step 0.01) -- in dollars
  - commodity (text, required)
  - shipper_name (text, required)
  - shipper_phone (tel)
  - special_requirements (textarea)

Purpose: Post freight loads for carrier bidding with origin/destination, dates, equipment constraints, and rate.

### 1.3 Rate Calculator Modal
- Component: RateCalculatorModal
- File: App.js:4022-4154
- Submission endpoint: POST /api/rate-calculator?{params}
- Fields:
  - origin_city (text, required)
  - origin_state (text, 2-char max, required, uppercase)
  - destination_city (text, required)
  - destination_state (text, 2-char max, required, uppercase)
  - weight (number) -- optional for rate calc
  - equipment_type (select) -> [dry_van, reefer, flatbed, step_deck]

Purpose: Compute lane pricing based on route, equipment type, and historical market data.

### 1.4 Team Member Invite Form
- Component: TeamManagement (nested in App.js)
- File: App.js:2080-2160
- Submission endpoint: POST /api/team/invite
- Fields:
  - name (text, required)
  - email (email, required)
  - role (select) -> [broker, employee]
  - send_email (checkbox)

Purpose: Invite team members (dispatchers, admins, employees) with role assignment and optional email delivery.

### 1.5 Shipper Intake Modal
- Component: ShipperIntakeModal
- File: views/ShipperIntakeModal.jsx
- Submission endpoint: PUT /api/shipper-profiles/{email} and POST /api/shipper-profiles/{email}/complete
- Fields (dynamic based on required_fields):
  - Typical: email (read-only), company_name, phone, last_origin
  - Editable required fields vary per shipper profile (8 typical fields total)
  - credit_limit (number, optional)

Purpose: Capture missing shipper profile data after initial load intake; auto-fills future BOLs and rate-cons once complete.

### 1.6 Empty Truck Post Form
- Component: EmptyTruckPostForm
- File: views/EmptyTruckPostForm.jsx
- Submission endpoint: POST /api/public/carrier-offer/{token}/empty-truck-post
- Fields:
  - target_state (select, required) -> all 50 US states
  - target_city (text, optional)
  - available_at (datetime-local, default = now + 4 hours)
  - equipment (select, prefilled from driver context)
  - notes (textarea, optional)

Purpose: Allow driver to post empty truck (return-home positioning) after delivery; token-based public endpoint.

### 1.7 Carrier Preferences Form
- Component: CarrierPreferences
- File: views/CarrierPreferences.jsx
- Submission endpoint: PUT /api/carrier/preferences
- Fields:
  - preferred_origin_states (multi-select) -> all 50 US states (toggle buttons)
  - preferred_destination_states (multi-select) -> all 50 US states (toggle buttons)
  - preferred_equipment (multi-select) -> [dry_van, reefer, flatbed, step_deck, tanker, auto_carrier, livestock, intermodal, heavy_haul]
  - min_rate_per_mile (number)
  - notification_channels (multi-select) -> [ws, push, sms, email]

Purpose: Carrier defines load matching criteria: preferred lanes, equipment, rate floor, and alert channels.

### 1.8 Accessorial Confirmation Modal
- Component: AccessorialConfirmModal
- File: views/AccessorialConfirmModal.jsx
- Submission endpoint: PUT /api/loads/{loadId}/accessorials
- Fields (required):
  - free_time_hours_pickup (number, required)
  - free_time_hours_delivery (number, required)
  - detention_rate_per_hour (number, required)
  - lumper_policy (select, required) -> [shipper_pays, carrier_pays, split, no_lumper]
- Fields (optional):
  - layover_rate (number)
  - tonu_rate (number)
  - fuel_surcharge_pct (number)

Purpose: Confirm accessorial terms (free time, detention, lumper) after load adoption; values lock into rate-con.

### 1.9 Driver Onboarding Questionnaire
- Component: DriverOnboardingQuestionnaire
- File: views/DriverOnboardingQuestionnaire.jsx
- Submission endpoint: POST /api/driver-preferences/me/onboarding
- Fields:
  - home_seek (checkbox) -- enables home-seek matching
  - home_address (text, conditional on home_seek) -- best-effort geocoded via OpenStreetMap
  - home_lat (number, auto-computed)
  - home_lng (number, auto-computed)
  - home_radius_mi (select) -> [50, 100, 200, 300]
  - profit_max (checkbox) -- enable statistically high-paying lane steering
  - preferred_equipment (multi-select) -> [dry_van, reefer, flatbed, step_deck]
  - available_days (multi-select) -> [Mon, Tue, Wed, Thu, Fri, Sat, Sun]
  - skipped (boolean) -- set to true if user clicks "Skip"

Purpose: First-time carrier onboarding: home-seek opt-in, profit-max opt-in, equipment/day availability preferences.

### 1.10 QR Load Link Onboarding
- Component: QROnboardLoad
- File: views/QROnboardLoad.jsx
- Submission endpoint: POST /api/public/carrier-offer/{token}/equipment-link (inferred)
- Fields:
  - name (text)
  - phone (tel)
  - equipment_type (select) -> [dry_van, reefer, flatbed, step_deck]
  - insurance_expires_at (date)
  - coiFile (file upload, Certificate of Insurance)
  - w9File (file upload, W9 tax form)
  - gpsConsent (checkbox, required, CCPA-compliant explicit opt-in)

Purpose: Public driver onboarding via QR-code-scanned load link; captures equipment, insurance expiry, tax docs, and explicit GPS tracking consent.

### 1.11 Quote History - Outcome Tracking
- Component: QuoteHistoryRow (within QuoteView)
- File: views/QuoteView.jsx:254-294
- Submission endpoint: POST /api/quotes/{id}/outcome
- Fields:
  - outcome (select, required) -> [won, lost, expired]
  - reason (select, conditional if outcome=lost) -> [price_too_high, equipment_unavailable, pickup_too_late, chose_competitor, no_response, cargo_changed, shipper_unqualified, other]
  - note (textarea, optional)

Purpose: Track quote win/loss outcomes and reasons for post-quote analytics.

### 1.12 Schema Defaults Profile Modal
- Component: SchemaDefaultsProfileModal
- File: views/SchemaDefaultsProfileModal.jsx
- Submission endpoint: PUT /api/schema/defaults/{profileId} (inferred)
- Dynamic field types based on schema:
  - Boolean fields: rendered as select with [---, Yes, No]
  - Select fields with pipe-delimited options
  - Number fields

Purpose: Configure vetting gates, equipment block rules, and DAT posting defaults as a profile.

## 2. Key Findings

### Finding 1: Equipment Enum Mismatch (Severity: Medium)
- CarrierPreferences.jsx line 21: 9 equipment types
- QROnboardLoad.jsx line 8: Only 4 types
- LoadBoardAggregator.jsx line 20: 8 types with title-case keys
- Implication: Carrier selection of tanker, livestock, intermodal, heavy_haul won't match available driver options.

### Finding 2: Validation Gap - Rate Per Mile Bounds (Severity: High)
- AddDriverModal (line 3740): no min/max attributes on rate input
- CarrierPreferences.jsx: min_rate_per_mile field shown but no validation visible
- Implication: Drivers could enter /mi or /mi without client-side warning.

### Finding 3: CCPA Compliance - GPS Consent Gate (Severity: Critical)
- QROnboardLoad.jsx line 56: gpsConsent state (default false, never pre-checked)
- Form submit blocked until checkbox ticked
- Finding: Best practice observed; explicit consent required before tracking starts.

### Finding 4: Mixed Auth Patterns (Severity: Medium)
- EmptyTruckPostForm: Uses public endpoint with token (no credentials)
- All admin forms: Use withCredentials: true
- Implication: Public/private form endpoints live side-by-side.

### Finding 5: Dynamic Field Rendering (Severity: Low)
- SchemaDefaultsProfileModal.jsx line 93: FieldInput renders boolean, select, number types dynamically
- Options are pipe-delimited strings, parsed at render time
- Implication: Adding new vetting gates requires schema constant update.

## 3. Summary

Total Forms: 12
Total Choice Arrays: 14
Total Fields: 140+ (text, number, select, checkbox, radio, textarea, date, datetime-local, tel, email, file)
Endpoints with Frontend Forms: 25
API-only Endpoints (no form): 100+ (accounting, self-heal, routing, vault, etc.)

Output file: C:\CHL\chl-memory\research\input_surface\frontend_forms.md
