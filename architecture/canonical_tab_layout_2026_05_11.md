# Canonical CHL Frontend Tab Layout — Locked 2026-05-11

**Purpose:** Single source of truth for the operator-facing tab layout. **Future agents MUST consult this file before adding ANY new tab.** Prevents the duplicate-tab drift that bit us 2026-05-10 (Ops Console v2 vs Ops Console, BOL Search vs global search, etc.).

**Last audit:** `tab_audit_2026_05_10.md`
**Last cleanup:** 2026-05-11 — removed 5 duplicate tabs (`_route_ops`, `_route_sopflow`, `_route_prevet`, `_route_scorecard`, `bol_search`)

---

## Rule: before adding a new tab

1. **Read THIS file** to see existing tabs by category
2. **Check the header buttons** (Calendar, Drafts, Scorecards, Console, SOP Flow, Pre-Vet, Email) — they ARE access points; new tabs may duplicate them
3. **Check the global search bar** — it accepts `BOL / ref# · driver · shipper · city`; don't build dedicated tabs for those concerns
4. If the new tab idea is a button-driven action set → consider adding a CARD to **Ops Console v2** instead of a new tab
5. If you DO add a new tab, **update this file in the same commit**

---

## Header buttons (top-right of nav — DO NOT duplicate as tabs)

| Button | Routes to | Notes |
|---|---|---|
| Calendar | inline modal | Day-grid view of renewals + scheduled events |
| Drafts | inline modal | Outbound message drafts |
| Scorecards | inline modal | Shipper/carrier scorecards summary |
| Console | `/ops` (new tab) | Legacy operator console — read-only live state |
| SOP Flow | `/sop-flow` (new tab) | Load-lifecycle kanban |
| Pre-Vet | `/prevet` (new tab) | Pre-vetting pipeline |
| **Email (yellow dropdown)** | dropdown with 2 options | Operator-mandated PRIMARY email surface |
| Logout | logout action | — |

---

## Tab nav (locked 2026-05-11)

Grouped by category. Each row: `id` (App.js) | `label` (UI) | view file | one-line purpose.

### OPERATIONS (8 tabs)

| id | label | view file | purpose |
|---|---|---|---|
| `dashboard` | Dashboard | `views/DashboardView.jsx` | KPI dashboard (loads / P&L / alerts) |
| `day1_dashboard` | Day-1 Monitor | `views/Day1Dashboard.jsx` | Owner-only 4-quadrant launch-day monitor |
| `monitoring` | Monitoring | inline `MonitoringView` | System health + telemetry |
| `loads` | Load Board | inline `LoadsView` | **CHL-internal** loads CRUD (post / filter / open) |
| `matches` | AI Matching | inline `MatchesView` + `PendingCarrierRequests` | AI carrier-to-load matching |
| `rules` | Auto Rules | inline `AutoRulesView` | Autopilot rule editor |
| `live_map` | Live Map | `views/LiveGpsMapView.jsx` + panels | GPS map + tracking exceptions + check-ins |
| `pipeline_walkthrough` | Pipeline | inline `PipelineWalkthroughView` | RFQ→Pay full-lifecycle walkthrough (owner-only) |
| `operator_queues` | Queues | `views/OperatorQueuesView.jsx` | Wave-1 review dashboard for 5 collections (owner-only) |
| `sop_playbook` | SOP Cards | `views/SOPPlaybookView.jsx` | Printable narrative cards of SOP stages |

### FINDING WORK / EXTERNAL BOARDS (3 tabs)

| id | label | view file | purpose |
|---|---|---|---|
| `aggregator` | Find Loads | `views/LoadBoardAggregator.jsx` | Multi-source aggregator (CHL Direct + SAM.gov + paid) |
| `market_radar` | Market Radar | `views/MarketRadar.jsx` | Single-pane unified hunt-and-close |
| `boards` | Loadboard Connections | `LoadBoardsManagerView` | Manage credentials/sources for external load boards |
| `rates` | Market Rates | `MarketRatesView` | Lane-rate analytics |

### CARRIER MANAGEMENT (2 tabs)

| id | label | view file | purpose |
|---|---|---|---|
| `carriers` | Carriers DB | inline `CarriersView` | Carrier registry: filter / search / open card |
| `bulk_hunter` | Specialty Hauler Lists | `HaulerCategoriesView` | 4-category specialty-hauler outreach (hopper/livestock/tanker/protein) |

### COMMUNICATIONS (3 tabs)

| id | label | view file | purpose |
|---|---|---|---|
| `command` | Command Phone | `views/CommandPhoneView.jsx` | Voice + SMS unified console (Twilio/Plivo) |
| `inbox` | Inbox | inline `InboxView` | dispatch@continentalhaul.com inbox |
| `notifications` | Notif Log | `views/NotificationLogView.jsx` | Outbound notification log (owner-only) |

### FINANCIAL (4 tabs)

| id | label | view file | purpose |
|---|---|---|---|
| `accounting` | Accounting | inline `AccountingView` | Books / ledger / transactions |
| `factor_api` | Factors | `views/FactorAPIWiringView.jsx` | Per-factor encrypted credential wiring + push-to-factor |
| `factor_matrix` | Factor Picker | `views/FactorMatrixView.jsx` | Ranked factoring-provider registry + OCR NOA test + push |
| `disputes` | Disputes | `DisputesDashboardView` | Factor disputes + clawback events (owner-only) |

### DOCUMENT / COMPLIANCE (2 tabs)

| id | label | view file | purpose |
|---|---|---|---|
| `doc_review` | Doc Review | `views/ComplianceTab.jsx` | Pending-document review queue |
| `compliance` | Compliance | inline `ComplianceView` | FMCSA/authority checklist |

### GOVERNANCE / SETUP (3 tabs)

| id | label | view file | purpose |
|---|---|---|---|
| `owner` | Owner | `views/OwnerDashboard.jsx` | Owner-only governance dashboard |
| `renewals` | Renewals | inline `RenewalsView` | Authority/insurance/bond expiration tracker (owner-only) |
| `vault` | Vault | inline `VaultView` | Encrypted credential + document vault (owner-only) |
| `settings` | Settings | inline `SettingsView` | Operator-tunable platform settings |

### SELF-HEAL / DIAGNOSTICS (5 tabs + 1 route)

| id | label | view file | purpose |
|---|---|---|---|
| `self_heal` | Self-Heal | inline `SelfHealView` | Observability log of every self-heal action (owner-only) |
| `patch_proposals` | Patch Proposals | inline `PatchProposalsDashboardView` | Claude patch proposals (owner-only) |
| `dev_reviews` | Reviews | inline `DevReviewQueueView` | YELLOW-tier patches awaiting operator approve/reject (owner-only) |
| `outcomes` | Outcomes | inline `OutcomesDashboardView` | Self-heal outcome tracking + weight tuning (owner-only) |
| `urgent_alerts` | Alerts | inline `UrgentAlertsView` | RED-tier alerts from llm_reasoning_loop + inbox_triage (owner-only) |
| `aether` | Æther | inline `SovereignPulseMonitor` | Sovereign pulse monitor / self-heal heartbeat |
| `_route_failures` | Failure Console | href `/failures` → `FailureModeConsole.jsx` | Failure-mode flags (authority lost, GPS silent, no-show, factor decline) — NO header-pill duplicate, KEEP |

### MANUAL OPS (1 tab)

| id | label | view file | purpose |
|---|---|---|---|
| `ops_console_new` | Ops Console v2 | `views/OpsConsoleView.jsx` | **Button-driven** manual workflow deck — 7 action cards + BOL Quick Search. Owner-only. Distinct from header "Console" pill which is read-only state viewer. |

### TOOLS (1 tab)

| id | label | view file | purpose |
|---|---|---|---|
| `rate_calc` | Rate Calculator | inline `RateCalculatorView` | Interactive rate calculator |

---

## Net counts after 2026-05-11 cleanup

| Audience | Tabs visible | Drop from before |
|---|---|---|
| Broker (non-owner) | ~25 tabs | -5 |
| Owner | ~32 tabs | -5 |

---

## Tabs REMOVED 2026-05-11 — DO NOT RE-ADD

| Removed id | Reason | Equivalent access path |
|---|---|---|
| `_route_ops` | Duplicate of header "Console" pill | Click header "Console" button |
| `_route_sopflow` | Duplicate of header "SOP Flow" pill | Click header "SOP Flow" button |
| `_route_prevet` | Duplicate of header "Pre-Vet" pill | Click header "Pre-Vet" button |
| `_route_scorecard` | Duplicate of header "Scorecards" pill | Click header "Scorecards" button |
| `bol_search` | Subsumed by Ops Console v2's BOL Quick Search card + global search bar | Click "Ops Console v2" → BOL Quick Search panel at top OR type BOL into the global search bar at the top of the page |

---

## Decision-rule for the future

**When you (future Claude) think you should add a new tab, ask:**

1. **Does a header pill already do this?** If yes — STOP. Don't add a tab.
2. **Does an existing tab cover this?** If yes — extend the existing tab; don't add a new one.
3. **Is it a button-driven manual action?** If yes — add a CARD to Ops Console v2; don't add a tab.
4. **Is it search-based?** If yes — extend the global search bar; don't add a search-specific tab.
5. **None of the above?** → Add the tab AND update this file in the same commit.

**If you add a tab without updating this file, the next audit deletes your tab.**

---

## Cross-references

- `chl-memory/architecture/SYSTEM_MATRIX.md` L4 (Execution) — tab nav is operator interface to execution layer
- `chl-memory/architecture/tab_audit_2026_05_10.md` — the audit that drove this cleanup
- `~/.claude/projects/c--CHL/memory/feedback_app_js_authorization.md` — App.js edits pre-authorized
- `~/.claude/projects/c--CHL/memory/MEMORY.md` — reference this file in the index so it's read on boot
