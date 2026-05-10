# Pydantic Models & Enum-Style Fields — CHL Backend

**Compilation Date:** 2026-05-10  
**Scope:** Backend (C:\CHL\backend\) — production code only  
**Total Python files:** 405  
**Total Pydantic BaseModel classes:** 249  
**Total Enum classes:** 15  
**Total Literal/Enum fields:** 17 unique catalog entries  

---

## Section A: Summary by Category

### Major Model Files
- **server.py:** 87 BaseModel classes (35% of total)
- **load_schema_v2.py:** 8 models (49 CFR compliance + equipment blocks)
- **throttle_models.py:** 11 models (health checks + SLA)
- **Distributed:** 153 models across 196 other files (~1 per module average)

### Model Distribution by Domain
| Domain | File Count | Model Count | Examples |
|--------|-----------|------------|----------|
| Auth & Identity | 3 | 7 | LoginRequest, RegisterRequest, ShipperRegister |
| Load Management | 12 | 45 | LoadCreate, Load, LoadBoardSource, DATPostBlock |
| Carrier & Vetting | 8 | 28 | CarrierRegister, CompleteVettingRequest, VettingGatesBlock |
| Payments & Finance | 9 | 31 | PaymentTransaction, DriverPaymentCreate, FinanceStrategy (enum) |
| Notifications | 4 | 12 | MagicLinkPayload, RoboCallPayload, ManualSmsPayload |
| Tracking | 3 | 8 | TrackingEventIn, TrackEventPayload, GPSPing |
| System & Health | 5 | 18 | ThrottleStatus, HealthCheck, SystemHealth |
| Compliance & Records | 6 | 14 | ComplianceBlock, RecordsRequestSubmit, BrokerInvoiceExecution |
| Pricing & Rate Calc | 8 | 12 | RateSignalIn, InstantQuoteRequest, PricingMode (enum) |
| Automations | 10 | 16 | EmailIntake, AutoRepostPatch, OutreachPayload |
| Other | 28 | 57 | Factor, ELD, Market Intel, Hardware, Driver Preferences, etc. |

---

## Section B: Complete Enum Catalog (15 Enums)

### ThrottleState (throttle_models.py)
- **Values:** GREEN, YELLOW, ORANGE, RED
- **Purpose:** 4-tier intake degradation state (100% → 50% → 10% → 0%)
- **Context:** Platform health monitoring; auto-blocks new quotes at RED
- **Used by:** throttle_system, SLA monitor, health checks

### HealthStatus (throttle_models.py)
- **Values:** healthy, degraded, unhealthy
- **Purpose:** Per-module health classification
- **Used by:** SystemHealth, HealthCheck models

### Channel (notification_service.py)
- **Values:** email, voice, sms
- **Purpose:** Communication channel priority (Email-First, Voice-Backup fallover)
- **Context:** Solves A2P 10DLC driver-contact crisis
- **Used by:** MagicLinkPayload, RoboCallPayload, ManualSmsPayload

### EngagementKind (notification_service.py)
- **Values:** link_opened, link_clicked, voice_answered, confirmed_tracking
- **Purpose:** Engagement event classification (Iter 100)
- **Used by:** notification_engagement collection

### AttemptStatus (notification_service.py)
- **Values:** sent, pending, failed, blocked, skipped, mocked
- **Purpose:** SMS/email/voice attempt outcome tracking
- **Context:** blocked = A2P-filtered (no retry)
- **Used by:** notification_attempts collection

### FinanceStrategy (finance_strategy.py)
- **Values:** FACTOR, SOVEREIGN
- **Purpose:** Liquidity pipe selection
  - FACTOR: 3rd-party factoring (3% fee, CHL risk-off)
  - SOVEREIGN: Self-finance via Mercury (captures 3% margin)
- **Used by:** shipper_profiles, loads, rate_con, invoice generation

### ProviderKind (liquidity_provider.py)
- **Values:** SOVEREIGN, FACTOR
- **Purpose:** Factoring entity classification
- **Used by:** liquidity_providers collection; NOA block on rate confirmation

### FundingState (liquidity_provider.py)
- **Values:** draft, sent_to_factor, factor_funded_97, carrier_paid, reserve_released
- **Purpose:** Load funding lifecycle state machine
- **Context:** Enforced transitions via _FUNDING_TRANSITIONS validator
- **Used by:** load_funding_events (append-only); director visibility on cash location

### PricingMode (pricing_modes.py)
- **Values:** survival, bid_aggressive, defensive, carrier_attract, volume_grab, outcome_protect
- **Purpose:** Operating-phase pricing strategy
  - Phase 1 default: SURVIVAL (floor-protected, no undercut)
  - Phase 2: 5 modes (undercut, premium, sweetener, volume, loss-leader)
- **Context:** Operator-mandated 2026-05-09; ready to wire into /api/rate-calculator
- **Used by:** (not yet wired; model_selector.select_for_quote will use it)

### PricingScheme (pricing_schemes.py)
- **Values:** time_of_day_premium, reverse_auction_evaluate, dutch_auction_step, fixed_price_contract, spread_based, hybrid_pct_min_dollar, cost_plus_transparent, time_decay_surge, weather_route_risk
- **Purpose:** 9 alternative pricing mechanics (HOW to compute price)
- **Status:** AVAILABLE_NOT_WIRED — shelved for future selection
- **Used by:** (none yet; awaiting operator GO)

### ModelType (model_calculators.py & model_selector.py)
- **Values:** model_a, model_b, model_c, model_d
- **Purpose:** Operating models (CHL strategy tiers)
  - MODEL_A: Volume/commodity (5-8% margin tier)
  - MODEL_B: Specialty/niche (15-25%)
  - MODEL_C: Tech-leveraged generalist [**CHL ACTIVE**] (12-15%)
  - MODEL_D: Hybrid generalist + premium (12% default + 18-25% overlay)
- **Context:** Operator-confirmed 2026-05-09: CHL operates Model C everywhere
- **Used by:** model_selector.select_for_quote; model_calculators (library code, not wired to API)

### DriverMode (driver_mode_toggle.py)
- **Values:** HOME_ARRIVAL, REVENUE_MAX, BALANCED (default)
- **Purpose:** Driver preference for load ranking
  - HOME_ARRIVAL: optimize distance-to-home (70% weight) + time-fit (30%)
  - REVENUE_MAX: optimize $/mile; deadhead penalty
  - BALANCED: default; user selectable
- **Context:** NCS-2 foundation (Network Control Strategy); NCS-3 (multi-leg chains) deferred
- **Used by:** rank_loads_for_driver(); driver_preferences collection

### NegotiationOutcome (negotiation_engine.py)
- **Values:** accept, counter_back, reject_final_offer, reject, escalate_to_operator
- **Purpose:** Counter-offer evaluation decision
- **Context:** Pure function library (not wired to API); caller persists state in db.negotiations
- **Used by:** evaluate_counter_offer(); shipper rate negotiation flow (future)

### PnlState (pnl_monitor.py)
- **Values:** GREEN, YELLOW, ORANGE, RED
- **Purpose:** Platform P&L surveillance state
  - GREEN: positive P&L; nothing required
  - YELLOW: trending down; advisory
  - ORANGE: break-even; operator review
  - RED: aggregate loss; ALL new quotes auto-blocked
- **Context:** Layer 2 (aggregate monitoring); Layer 1 is per-load hard guard
- **Status:** Library code; wiring to /api/rate-calculator DEFERRED pending operator confirmation
- **Used by:** would_load_lose_money() hard guard; evaluate_window_pnl()

---

## Section C: Literal/Enum Fields in Models

### Load Schema (Compliance & Equipment)

#### DATPostBlock (load_schema_v2.py:75)
**Purpose:** Cross-post fields for DAT/Truckstop/123LB

| Field | Type | Values | Notes |
|-------|------|--------|-------|
| full_or_partial | Literal | "full", "partial" | Load type selector |
| contact_method_preference | Literal | "phone", "alt_phone", "email" | Preferred contact channel |
| palletized_or_floor | Literal | "palletized", "floor", "mixed" | Cargo orientation |

**Endpoint:** PUT /api/loads/{load_id}/dat-post (Phase B compliance)

#### HazmatDetail (load_schema_v2.py:115)
**Purpose:** Hazmat material declaration (49 CFR compliant)

| Field | Type | Values | Notes |
|-------|------|--------|-------|
| packing_group | Literal | "I", "II", "III" | Hazmat packing classification (uppercase, industry standard) |

**Endpoint:** PUT /api/loads/{load_id}/equipment-block (Phase C)

#### ReeferDetail (load_schema_v2.py:128)
**Purpose:** Reefer/temperature control specification

| Field | Type | Values | Notes |
|-------|------|--------|-------|
| temp_unit | Literal | "F", "C" | Temperature unit (Fahrenheit / Celsius) |
| continuous_or_cycle | Literal | "continuous", "cycle" | Reefer mode (continuous temp vs. cycle mode) |

**Endpoint:** PUT /api/loads/{load_id}/equipment-block (Phase C)

#### FlatbedDetail (load_schema_v2.py:140)
**Purpose:** Flatbed special handling (securement, tarping, escorts)

| Field | Type | Values | Notes |
|-------|------|--------|-------|
| tarp_size | Literal | "4-tarp", "6-tarp", "8-tarp", "steel-tarp" | Tarp configuration options |
| securement_type | List[Literal] | ["straps", "chains", "coil-racks", "v-boards"] | Multi-select securement types (UNIQUE: allows array) |
| escort_vehicles | Literal | "none", "front", "rear", "both" | Escort vehicle requirement (none / front / rear / both) |

**Endpoint:** PUT /api/loads/{load_id}/equipment-block (Phase C)

#### VettingGatesBlock (load_schema_v2.py:168)
**Purpose:** Carrier vetting minimums (insurance, safety rating, payment terms)

| Field | Type | Values | Issue |
|-------|------|--------|-------|
| carrier_min_safety_rating | Literal | "Satisfactory", "Conditional", "None" | ⚠️ "None" should be null (Python None, not string) |
| payment_terms | Literal | "quickpay", "net_15", "net_30", "net_45" | Missing "net_60", "net_90" (industry standard) |

**Endpoint:** PUT /api/loads/{load_id}/vetting-gates (Phase D)

### External Post Pipeline (external_post_pipeline.py)

#### PostResult (external_post_pipeline.py:36)
| Field | Type | Values |
|-------|------|--------|
| status | Literal | "posted", "error", "skipped" |

#### PostExternallyRequest (external_post_pipeline.py:158)
| Field | Type | Values | Notes |
|-------|------|--------|-------|
| boards | List[Literal] | ["dat", "truckstop", "123loadboard", "directfreight"] | Multi-select external load board targets |

**Endpoint:** POST /api/external-post

#### TrackEventRequest (external_post_pipeline.py:165)
| Field | Type | Values |
|-------|------|--------|
| event_type | Literal | "view", "inquiry", "expired", "removed" |

**Endpoint:** POST /api/tracking/events

### Inquiry Pipeline (inquiry_pipeline.py)

#### NegotiationPatch (inquiry_pipeline.py:149)
| Field | Type | Values |
|-------|------|--------|
| status | Optional[Literal] | "open", "in_progress", "booked", "declined", "no_response" |

**Endpoint:** PATCH /api/inquiries/{inquiry_id}

#### CompleteNegotiationRequest (inquiry_pipeline.py:157)
| Field | Type | Values |
|-------|------|--------|
| outcome | Literal | "booked", "declined", "no_response" |

**Endpoint:** POST /api/inquiries/{inquiry_id}/complete

### Module-Level TypeAlias

#### CarrierType (carrier_hub.py:135)
`python
CarrierType = Literal["owner_operator", "company_fleet"]
`
**Used by:** CarrierRegister, CarrierHub endpoints

---

## Section D: Patterns & Red Flags

### D.1: Case Inconsistencies
- **Uppercase (hazmat/CFR standard):** packing_group ("I", "II", "III"), temp_unit ("F", "C")
- **Lowercase (platform standard):** most others ("full", "partial", "phone", "email")
- **Risk:** API clients may fail if case validation is strict
- **Recommendation:** Document case rules; add API-layer validation

### D.2: Type Confusion
- **String vs Null:** VettingGatesBlock.carrier_min_safety_rating uses string "None" instead of Optional[...] with None default
- **Multi-select inconsistency:** FlatbedDetail.securement_type is List[Literal[...]] (multi-select) but escort_vehicles is single Literal
- **Recommendation:** Standardize; use List[Literal] for all multi-select, single Literal for single-choice

### D.3: Missing Domain Constraints
- **No Status validation:** Load, Match, Booking models use status string fields but no Literal constraints
- **No Priority enum:** Many domains lack urgency/priority selectors
- **No Retry policy enum:** AttemptStatus tracks outcome but not retry strategy
- **Recommendation:** Add Literal["low", "medium", "high", "critical"] to models requiring prioritization

### D.4: Incomplete Enumerations
- **payment_terms:** Missing "net_60", "net_90" (industry standard for freight)
- **PricingScheme:** 9 schemes defined but 0 are wired; ensure they're not lost in backlog
- **Recommendation:** Audit live shipper/carrier contracts; add missing payment terms

### D.5: Centralization Gaps
- **Literal fields scattered:** Spread across load_schema_v2.py, external_post_pipeline.py, inquiry_pipeline.py
- **No single source of truth:** Enum values are defined inline; no central constants module
- **Recommendation:** Create ackend/schemas/enums.py or ackend/constants.py with all Literal → Enum migrations

---

## Section E: Wiring Status

### Phase 1 (Operational Today)
- FinanceStrategy (FACTOR | SOVEREIGN) — live on shipper_profiles
- FundingState — live in load_funding_events
- ThrottleState — live in throttle monitoring
- HealthStatus — live in health checks
- Notification Enums (Channel, AttemptStatus, EngagementKind) — live

### Phase 2 (Ready to Wire, Operator Gate Required)
- PricingMode (6 operating modes) — library code; awaiting Phase 2 transition + operator GO
- ModelType (4 operating models) — library code; awaiting operator selection
- NegotiationOutcome — pure function library; awaiting negotiation flow integration

### Shelved (Available, Not Wired)
- PricingScheme (9 schemes) — "build but don't use yet" per operator 2026-05-10
- DriverMode (3 modes) — NCS-2 foundation; NCS-3 (multi-leg) deferred

---

## Appendix: File Summary

| File | Models | Enums | Purpose |
|------|--------|-------|---------|
| server.py | 87 | 0 | Core API: auth, loads, drivers, payments, business |
| load_schema_v2.py | 8 | 0 | Compliance blocks (CFR, equipment, vetting) |
| throttle_models.py | 11 | 2 | Health & SLA monitoring |
| notification_service.py | 3 | 3 | Multi-channel notifications (Email/Voice/SMS) |
| finance_strategy.py | 2 | 1 | Liquidity pipe selection (FACTOR vs SOVEREIGN) |
| liquidity_provider.py | 5 | 2 | Factoring entity vault + funding lifecycle |
| pricing_modes.py | 0 | 1 | Phase 2 pricing strategies (6 modes) |
| pricing_schemes.py | 0 | 1 | Alternative pricing mechanics (9 schemes, shelved) |
| model_selector.py | 0 | 1 | Operating model routing (4 models) |
| model_calculators.py | 0 | 1 | Rate calculators per model (library) |
| driver_mode_toggle.py | 0 | 1 | Driver preference scoring (NCS-2) |
| negotiation_engine.py | 0 | 1 | Counter-offer evaluation (pure library) |
| pnl_monitor.py | 0 | 1 | P&L surveillance (per-load + aggregate) |
| (196 other files) | 153 | 1 | Distributed: carrier, tracking, automations, etc. |
| **TOTAL** | **249** | **15** | — |

---

**End of Report**

Generated 2026-05-10 · Static Analysis (grep + Read) · No Python execution
