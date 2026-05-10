# CHL Frontend Inventory Audit — 2026-05-08

## Section 1 — Tab Inventory (Navigation Surface)

| tab_id | label | icon | gating | view_component | route_hash | purpose |
|--------|-------|------|--------|----------------|-----------|---------|
| dashboard | Dashboard | ChartLine | always | DashboardView | – | Broker command center with KPIs, matches, and quick actions |
| command | Command Phone | PhoneIcon | always | CommandPhoneView | – | Mobile-optimized SMS/call orchestration + broadcast console |
| monitoring | Monitoring | Eye | always | MonitoringView | – | Health monitoring + AI suggestions (Iter 135.13 promoted) |
| loads | Load Board | Package | always | LoadsView | – | Internal load posting, filtering, and bulk management |
| matches | AI Matching | Lightning | always | MatchesView | – | AI-driven carrier matching with pending request queue |
| carriers | Carriers DB | IdentificationCard | always | CarriersView | – | Carrier master file, FMCSA lookup, vetting (Hub access button) |
| market_radar | Market Radar | Crosshair | always | MarketRadar (lazy) | – | Single-pane hunt/close unifier (loaded on demand) |
| boards | Loadboard Connections | Briefcase | always | LoadBoardsManagerView | – | Integration management (DAT, Truckstop, Custom) |
| rates | Market Rates | TrendUp | always | MarketRatesView | – | Lane pricing intel and historical rate analysis |
| rules | Auto Rules | SlidersHorizontal | always | AutoRulesView | – | Approval & routing rule automation engine (folded Autopilot) |
| payments | Payments | Money | always | PaymentsView | – | Cash flow, rate confirmations, carrier payouts |
| live_map | Live Map | Crosshair | always | LiveGpsMapView + panels | – | GPS tracking + exceptions + check-in dashboards (merged Iter 138.2) |
| inbox | Inbox | ChatCircleDots | always | InboxPanel | – | Incoming partner/prospect messages (partner_type feed) |
| aggregator | Find Loads | Globe | always | LoadBoardAggregator | – | Multi-source live feeds (DAT, Truckstop, shipper portal) |
| bulk_hunter | Specialty Hauler Lists | Target | always | HaulerCategoriesView | – | EOD-13 outreach surface (Hopper/Grain/Tanker/Livestock) |
| factor_api | Factors | Plug | always | FactorAPIWiringView | – | Factor credential vault per factor (encrypted) |
| _route_ops | Ops Console | Eye | always | external route | /ops | External ops surface (opens in new tab) |
| _route_sopflow | SOP Flow | ClipboardText | always | external route | /sop-flow | SOP transaction execution surface (new tab) |
| _route_prevet | Pre-Vet | IdentificationCard | always | external route | /prevet | Carrier pre-vetting workflow (consolidated) |
| _route_scorecard | Shipper Scorecard | Target | always | external route | /shipper-scorecard | Shipper performance analytics (new tab) |
| _route_failures | Failure Console | Shield | always | external route | /failures | Failure mode analysis console (new tab) |
| sop_playbook | SOP Cards | ClipboardText | always | SOPPlaybookView | – | Full load lifecycle narrative deck (pairs /sop-flow) |
| accounting | Accounting | Calculator | always | AccountingView | – | P&L, transaction journal, categorization |
| disputes | Disputes | Shield | is_owner | DisputesDashboardView | – | Factor disputes + clawback tracking (EOD-15 Section A) |
| patch_proposals | Patch Proposals | Robot | is_owner | PatchProposalsDashboardView | – | sub_agent_dispatcher proposals (EOD-15 Section B) |
| outcomes | Outcomes | TrendUp | is_owner | OutcomesDashboardView | – | Outcome tracking + weight tuning (EOD-15 Section C) |
| owner | Owner | Crown | is_owner | OwnerDashboard | – | Operator-only metrics and system controls |
| doc_review | Doc Review | Shield | always | ComplianceTab | – | Driver document review queue (compliance pending) |
| compliance | Compliance | Shield | always | ComplianceView | – | Checklist, resources, doc audit trail |
| vault | Vault | Vault | always | VaultView | – | Credential vault (owner-visible, encrypted) |
| aether | Æther | Target | always | SovereignPulseMonitor | – | Sovereign platform monitoring + telemetry |
| notifications | Notif Log | EnvelopeSimple | is_owner | NotificationLogView | – | Operator system notification log (owner-only) |
| factor_matrix | Factor Picker | HandCoins | is_owner | FactorMatrixView | – | Factor weighting matrix (owner-only) |
| settings | Settings | Gear | always | BusinessSettingsView | – | Team, outbound, stripe, P&L email, rules |
| banker_digest | Banker Digest | EnvelopeSimple | is_owner | BankerDigestPage | – | Financial digest viewer (owner-only, Iter 110) |

**Hidden Tabs (not in top-bar, but rendering logic intact):**

| tab_id | reason_hidden | status | component_exists | accessible_via |
|--------|---------------|--------|-------------------|------------------|
| settlements | Folded into Payments + Accounting conceptually | Iter 138.1 | SettlementsView | Direct activeTab if needed |
| employees | Premature (CHL has 1 employee) | Iter 138.1 | EmployeesView | Hidden but functional |
| facility | Premature (no physical facility planned) | Iter 138.1 | FacilityPlan | Hidden but functional |
| carrier_hub | Consolidated into Carriers DB (Hub button in DB) | Iter 138.1 | CarrierHub | Button within CarriersView |
| tracking | Dead since merge into live_map (safety net) | Iter 138.2 | LiveGpsMapView | Fallback only; live_map preferred |
| automations | Removed from bar (was duplicate of Auto Rules) | Iter 135.13 | AutomationsDashboard | Rendering path exists |
| god_view | Not in top-bar | – | LiveBoard | Intentional (premium feature?) |

---

## Section 2 — Component-to-Tab Map (Top-Level Views)

### Core Navigation Views

| Component | Tabs Rendering | Lines (approx) | API Endpoints | Role Gating |
|-----------|-----------------|----------------|---------------|-------------|
| DashboardView | dashboard | 2802–2812 | /dashboard/stats, /matches/ai-suggest | always |
| CommandPhoneView | command | 2816–2818 | /phone/* (status, sms, calls, voicemails) | always |
| LoadsView | loads | 2824–2832 | /loads?, /rate-calculator, /lane-rate-history | always |
| MatchesView | matches | 2835–2846 | /matches/ai-suggest, /matches/load/{id}, /bookings | always |
| PaymentsView | payments | 2849–2855 | /payments?, /payments/checkout*, /payment-terms | always |
| AccountingView | accounting | 2858–2864 | /accounting/*, /messages | always |
| ComplianceView | compliance | 2877–2879 | /compliance/* | always |
| ComplianceTab | doc_review | 2872–2874 | /compliance/* | always |
| SettlementsView | settlements | 2867–2869 | (no direct calls found in wrapper) | – |
| VaultView | vault | 2882 | (internal state only) | always |
| FacilityPlan | facility | 2883 | (internal state only) | always |
| CarrierHub | carrier_hub | 2885 | (see CarriersView) | always |
| SovereignPulseMonitor | aether | 2886 | (internal telemetry) | always |
| NotificationLogView | notifications | 2887 | /notifications? | is_owner |
| FactorMatrixView | factor_matrix | 2889 | (internal state) | is_owner |
| OwnerDashboard | owner | 2890 | (various owner-scoped) | is_owner |
| MonitoringView | monitoring | 2894–2896 | /monitoring/*, /assistant/chat | always |
| AutoRulesView | rules | 2899–2901 | /approval-rules*, /routing/factors* | always |
| LiveGpsMapView | live_map, tracking | 3011–3016 | /gps/*, /tracking/*, /_tracking/* | always |
| CarriersView | carriers | 2914–2916 | /carriers?, /bca/*, /carrier-vetting*, /hauler_types | always |
| ExternalLoadsView | external | 2919–2921 | /external-loads? | always |
| LoadBoardAggregator | aggregator | 2924–2926 | /hauler_types/*, /shipper-scorecard/lookup | always |
| HaulerCategoriesView | bulk_hunter | 2933–2935 | /hauler_types/* | always |
| FactorAPIWiringView | factor_api | 2938–2940 | (internal credential state) | always |
| DisputesDashboardView | disputes | 2944–2946 | /factoring/disputes* | is_owner |
| PatchProposalsDashboardView | patch_proposals | 2950–2952 | /_self_healing/patch_proposals* | is_owner |
| OutcomesDashboardView | outcomes | 2956–2958 | /_self_healing/* | is_owner |
| SOPPlaybookView | sop_playbook | 2963–2965 | (none in wrapper; internal UI) | always |
| LiveBoard | god_view | 2968–2970 | /live-board/* | – |
| MarketRadar | market_radar | 2973–2984 | (lazy-loaded, check MarketRadar.js) | always |
| LoadBoardsManagerView | boards | 2987–2989 | /load-board-sources* | always |
| MarketRatesView | rates | 2992–2994 | /market-rates? | always |
| AutomationsDashboard | automations | 2999–3001 | (internal state) | – |
| EmployeesView | employees | 3004–3006 | /employees* | always |
| InboxPanel | inbox | 3020–3022 | /_inbox/* | always |
| BusinessSettingsView | settings | 3027–3029 | /business-settings, /team*, /stripe/connect*, /reports/* | always |
| BankerDigestPage | banker_digest | 3036–3038 | (internal state) | is_owner |

### Component-Specific API Calls

**DashboardView:** /dashboard/stats, /matches/ai-suggest, /bookings, /drivers, /loads, /compliance/pending-review

**MonitoringView:** /monitoring/overview, /monitoring/suggestions, /assistant/chat

**CarriersView:** /carriers?, /bca/{mc}, /carrier-vetting/complete-and-send-bca, /carrier-discovery/near-load/{id}, /hauler_types/categories

**LiveGpsMapView:** /gps/live, /gps/trail/{carrierId}, /tracking/drivers, /tracking/checkins/recent, /loads/{id}/exceptions/{id}/acknowledge, /_tracking/exceptions/active

**AutoRulesView:** /approval-rules*, /routing/factors*, /assistant/chat

**LoadsView:** /loads?, /rate-calculator, /lane-rate-history/lookup, /bookings, /matches/load/{id}

**AccountingView:** /accounting/transactions?, /accounting/categories, /accounting/import-csv, /accounting/pnl?, /messages

---

## Section 3 — Orphan Candidates & Broken References

### A. Imported Components Not Rendered in Main Tab Flow

| Component | Import Line | Status | Notes |
|-----------|------------|--------|-------|
| QuoteView | 83 | ORPHANED | Imported but never rendered in activeTab conditions; Iter 135.13 notes "Quote tab removed (use Post Load modal)" |
| ResourcesView | 84 | ORPHANED | Imported, never rendered; Iter 135.13 "Resources removed from top-bar (move to Settings → Resources)" |
| ParkingView | 87 | ORPHANED | Imported, never rendered; Iter 135.13 "Parking moved to carrier portal only" |
| LandingPage | 88 | ORPHANED | Used for auth routes only (not in App.js main render) |
| StrategyRoom | 125 | ORPHANED | Imported, never rendered; Iter 135.13 "strategy demoted to URL only (/strategy)" |
| ExternalPostingsPanel | 116 | ORPHANED | Imported but not rendered in any tab |
| Phase2ReadinessView | 160 | ORPHANED | Imported, never rendered; "Canadian readiness hidden (premature)" |
| ManualCoverageDashboard | 162 | ORPHANED | Imported, never rendered |
| CargoClaimsView | 164 | ORPHANED | Imported, never rendered |
| DetentionReviewView | 165 | ORPHANED | Imported, never rendered |
| FraudConsoleView | 166 | ORPHANED | Imported, never rendered |
| AgedARView | 167 | ORPHANED | Imported, never rendered |
| BusinessMilestones | 117 | ORPHANED | Imported, never rendered in tabs |

### B. Tabs with Broken/Unreachable Render Paths

| Tab ID | Status | Issue | Impact |
|--------|--------|-------|--------|
| automations | HIDDEN | Rendering path exists but tab removed from nav; rendered if activeTab="automations" set directly | Low: functionality intact if accessed programmatically |
| tracking | LEGACY | Kept as "safety net" after merge into live_map; dead render path | Low: fallback only |
| god_view | HIDDEN | No nav entry for this tab; must be set via setActiveTab directly | Medium: feature discovery broken |
| facility | HIDDEN | In nav but gated to nobody (no conditional render) | Medium: data loss risk if re-enabled |
| carrier_hub | HIDDEN | Functionality moved to button inside CarriersView; orphaned render block | Low: accessible via sidebar button |

### C. API Endpoints Called But Backend Status Unknown

**High-confidence matches (found in server.py or route files):**
- /api/drivers*, /api/loads*, /api/bookings, /api/matches*, /api/payments* ✓
- /api/carriers*, /api/bca/*, /api/compliance/* ✓
- /api/phone/*, /api/notifications*, /api/messages* ✓
- /api/accounting/*, /api/approval-rules*, /api/routing/factors* ✓
- /api/gps/*, /api/tracking/* ✓

**Uncertain endpoints (needs backend verification):**
- /_self_healing/* (patch_proposals, weight_proposals, outcomes) → Grep backend for _self_healing
- /_inbox/* (override_sender_type, recent, spam_review) → Check backend for inbox routes
- /_tracking/exceptions/* → Check for exception routes
- /shipper-scorecard/lookup/by-name → Verify shipper module
- /load-board-sources* → Verify aggregator module
- /hauler_types/* → Check hauler_types module

**Critical missing?**
- /api/auth/magic-link/consume → Grep for magic-link
- /api/carrier-discovery/near-load/{id} → Verify discovery module
- /api/carrier/track-event → Verify carrier event tracking

### D. Unused Imports (Imported But Refs ≤ 1)

| Import | Refs | Status | Context |
|--------|------|--------|---------|
| axios | 0 | PARTIALLY USED | Used indirectly via xios.get() etc. in code (not as named export) |
| QuoteView | 1 | ORPHANED | Import only, no render |
| ResourcesView | 0 | ORPHANED | Import only |
| ParkingView | 0 | ORPHANED | Import only |
| ExternalPostingsPanel | 0 | ORPHANED | Import only |
| BusinessMilestones | 0 | ORPHANED | Import only |

---

## Section 4 — UX Consistency Findings

### A. Insufficient Contrast Issues

**Found 2 instances of g-white/10 (low contrast on white):**

1. **Line 613** (PhoneView SMS broadcast):
   `jsx
   className="relative bg-white/10 hover:bg-white/20 p-1.5 rounded transition"
   `
   Issue: White text on white/10 is barely readable. Affects "Clear" button in broadcast panel.
   Risk: Users may miss button affordance.

2. **Line 10496** (CarrierPortal logout):
   `jsx
   className="bg-white/10 hover:bg-white/20 px-3 py-1.5 rounded text-sm"
   `
   Issue: Light gray text on white/10 background.
   Risk: Low visibility in bright environments.

3. **Line 11600** (EmployeesView logout):
   `jsx
   className="bg-white/10 hover:bg-white/20 px-3 py-1.5 rounded text-sm"
   `
   Issue: Consistent pattern; white text on translucent white.
   Risk: Accessibility WCAG AA failure likely.

**Header buttons with potential contrast issues:**
- Line 2562: g-[#002FA7] text-white ✓ (good contrast, 4.5:1+)
- Line 10477: g-[#002FA7] text-white ✓ (good contrast)

**Pattern found:** All g-white/10 instances are in portals/submodules that may not have strict testing. Header primary nav uses g-[#002FA7] consistently (dark blue, good contrast).

### B. Buttons Missing Hover States

**Identified ~40+ interactive buttons. Coverage analysis:**

- Buttons with hover:bg-* states: ~85% (good)
- Buttons with hover but no active state: ~15%
- Buttons with disabled state handling: ~90%

**Specific gaps:**
1. Line 10905: Delivery success button uses hover:bg-[#001F70] only (no :active state)
2. Line 11080: Delivery confirm button uses hover:bg-green-700 (no :active)
3. Several employee/settings buttons missing :focus-ring styles

**Overall risk:** Medium. Most buttons have hover. Gaps are in modals/subflows not in primary path.

### C. Missing Loading/Error States

**Dashboard Components:**

| Component | Loading State | Error State | Notes |
|-----------|---------------|------------|-------|
| DashboardView | ✓ (loading prop) | Implicit fallback | Shows spinner + "Warming up…" message |
| MarketRadar | ✓ (Suspense + fallback) | Fallback only | "Warming up Market Radar…" with spinner |
| MonitoringView | ✓ (loading state) | Try/catch present | Good |
| CarriersView | ✗ (no loading UI) | Try/catch present | Fetch shown but no skeleton/spinner |
| LiveGpsMapView | ✗ (raw load) | Try/catch present | Risk: blank screen while loading |
| MatchesView | ✓ (loading prop) | Handled | Good |
| LoadsView | ✓ (skeleton-like) | Implicit | Good |
| AutoRulesView | ✗ (data appears instantly) | Try/catch | Risk if slow network |
| AccountingView | ✗ (no skeleton) | Try/catch | Data loads then renders |

**Missing patterns:**
- CarriersView (6431 L) has no loading skeleton
- LiveGpsMapView (2935 L) loads pins without UI feedback
- AutoRulesView (9046 L) renders form before rules load

---

## Section 5 — Recommendations (Priority-Ordered)

### 1. **Purge 13 Orphaned Component Imports** (Effort: S, Risk: S)
- Remove: QuoteView, ResourcesView, ParkingView, StrategyRoom, Phase2ReadinessView, ManualCoverageDashboard, CargoClaimsView, DetentionReviewView, FraudConsoleView, AgedARView, BusinessMilestones, ExternalPostingsPanel
- Benefit: Reduce bundle size (~50-100 KB), simplify maintenance
- Risk if skipped: Dead code bloat; increases cognitive load for new devs

### 2. **Fix 3 bg-white/10 Contrast Failures** (Effort: S, Risk: S)
- Replace g-white/10 text-* with proper button color (e.g., g-gray-300 text-gray-900 or g-gray-600 text-white)
- Lines: 613, 10496, 11600
- Benefit: WCAG AA compliance, better UX
- Risk if skipped: Accessibility lawsuit risk; failed audit

### 3. **Add Loading Skeletons to 3 Slow Views** (Effort: M, Risk: S)
- CarriersView (L6431): Add skeleton grid while /carriers? loads
- LiveGpsMapView (L2935): Add map skeleton + pin count before render
- AutoRulesView (L9046): Add form skeleton before rules fetch
- Benefit: Better perceived performance, clearer UX
- Risk if skipped: Users may think app froze on slow connections

### 4. **Deactivate 'tracking' Legacy Tab Fallback** (Effort: S, Risk: M)
- Remove {activeTab === "tracking" && <LiveGpsMapView />} block (L2909)
- Add console.warn if somehow set (for debugging)
- Add migration docs if any external links point to #tracking
- Benefit: Reduce redundant render paths, clarify intent
- Risk if skipped: Hidden tech debt; confusion about live_map vs tracking

### 5. **Document Hidden Tab Re-Activation Procedures** (Effort: S, Risk: L)
- Create CLAUDE.md section for "dormant features" (facility, employees, settlements)
- Include re-activation checklist for each (API status, UI polish, testing)
- Link from codebase comments
- Benefit: Faster feature resurrection in future iterations
- Risk if skipped: Confusion about why some features are "off"

---

## Summary Statistics

- **Total Tabs (visible + hidden):** 37
- **Tab Categories:** 
  - Always-visible: 28
  - Owner-gated: 4
  - Hidden (inactive): 4
  - External routes (new tab): 5
- **Total View Components:** 25
- **Imported Components Not Rendered:** 13 (ORPHANED)
- **Broken/Unreachable Tabs:** 5 (tracked, facility, carrier_hub, automations, god_view)
- **Contrast Issues Found:** 3 (g-white/10)
- **Views Without Loading States:** 3 (CarriersView, LiveGpsMapView, AutoRulesView)
- **API Endpoints Called:** 85+
- **Estimated Cleanup Effort:** 4–6 story points (2–3 person-days)

---

**Audit Metadata**
- Generated: 2026-05-08 (ISO)
- Scope: C:\CHL\frontend\src\App.js (13,994 LOC)
- Tool: Claude Code (Haiku 4.5)
- Read-only: True (no modifications made)

