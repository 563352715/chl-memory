# EOD-10 Dashboards Spec — Disputes / Patch Proposals / Outcomes

- Date: 2026-05-08
- Author: dev sub-agent Z (EOD-14 fallback dispatch under autonomous-continue)
- Status: DRAFT — design only, no code commits, no App.js touches
- Reason this exists: PM Lead allegedly drafted three dashboard specs in
  commit `be21dba`. The file `chl-memory/research/eod_10_dashboards_spec.md`
  does not exist on disk in either CHL or chl-memory worktrees; this is
  consistent with the failure mode documented in
  `feedback_pm_claude_bridge_unreliable.md` (PM Claude web_fetch fabrications).
  Operator authorized fallback dispatch under autonomous-continue mode;
  this doc reconstructs the spec from backend ground-truth in CHL repo.

## Scope

Three operator-facing React dashboards that surface backend systems
already shipped in EOD-6 / EOD-7 / EOD-9. All three follow the established
App.js inline-component + activeTab pattern (see Cross-cutting Standards).

Backend endpoints for all three are already live and authed via
`require_broker`. The work this spec covers is FRONTEND ONLY:

- Section A — Disputes Dashboard (/disputes) — surfaces EOD-7 Stream G
- Section B — Patch Proposals Dashboard (/patch_proposals) — surfaces EOD-6 Stream C
- Section C — Outcomes Dashboard (/outcomes) — surfaces EOD-7 Stream H + EOD-9 Stream L

## Cross-cutting standards

All three dashboards MUST follow these conventions, lifted from the
existing in-repo patterns:

### Tab integration (App.js)

- Component is defined inline in `frontend/src/App.js` between the
  `TrackingExceptionsPanel` (line 11762) and `InboxPanel` (line 11986)
  blocks. Mirror those component shapes exactly.
- New tab id added to the `tabs` array around App.js line 2724 (where
  `inbox` is currently registered). Suggested placement: immediately
  after `inbox` for Disputes; group Patch Proposals + Outcomes together
  under an `is_owner`-gated cluster near `factor_matrix` (line 2747).
- New activeTab branches added next to the existing
  `{activeTab === "factor_matrix" && ...}` block around App.js line 2886.
  All three new branches MUST be wrapped in `currentUser?.is_owner` —
  these are operator-only surfaces.
- Tab badges (red dot + count) are wired by passing a count from a
  parent-level `useEffect` poll. Patch Proposals badge = pending HIGH
  count; Disputes badge = pending count; Outcomes does not need a badge
  (informational dashboard, not action queue).

### HTTP client

- Use top-level `axios` import already in App.js. All requests
  pass `{ withCredentials: true }`. No new HTTP client.
- API base: `${API}` is already a module-level constant defined near
  App.js line 100; do NOT re-derive.

### Auto-refresh

- Pattern: `useEffect(() => { refresh(); const id = setInterval(refresh, MS); return () => clearInterval(id); }, [refresh])`.
- Disputes: 5min (300_000) — money flow but not real-time.
- Patch Proposals: 2min (120_000) — operator's hard-look surface; HIGH
  proposals can land any time and need quick visibility.
- Outcomes: 5min (300_000) — informational + audit; not time-sensitive.

### Loading / Error / Empty states

Every panel surfaces these THREE states explicitly. No skeleton screens
(not the established CHL pattern). Use the existing TrackingExceptionsPanel
shape:

- Loading: header `refresh` button shows `↻ refreshing…` with disabled
  attribute. Body shows existing rows (don't blank during refresh).
- Error: top-of-panel one-line slate-500 italic banner with the error
  string + a "retry" button. Body keeps last good rows.
- Empty: centered slate-500 12px text inside the table area with
  domain-specific reassuring copy (samples below in each section).

### Severity / state pills

Reuse the existing `SEVERITY_PILL` constant (App.js line 11743) for
status colors. New constants for each dashboard are added directly above
each component in the same const-then-component pattern as
`SENDER_TYPE_PILL` (App.js line 11983).

Existing pill palette (do NOT diverge):

```
critical : bg-red-100   text-red-800   border-red-300
alert    : bg-orange-100 text-orange-800 border-orange-300
warn     : bg-amber-100 text-amber-800 border-amber-300
unknown  : bg-slate-100 text-slate-700 border-slate-300
```

### Operator-authority gate (write actions)

Every write action (resolve / approve / reject) is two-stage:

1. Click → confirmation modal that shows EXACTLY what's about to happen
   (anomaly_id, gate, file count, what flips after the POST).
2. Operator types optional notes, clicks Confirm → POST executes.

Do NOT use `window.confirm`/`window.prompt` for write actions on
patch_proposals or outcomes — the existing TrackingExceptionsPanel pattern
of `window.prompt("note?", "")` is OK for low-stakes ack-only flows but
NOT for HIGH-gate patch approvals.

For Disputes (resolve), `window.prompt` is acceptable v1; upgrade to a
proper modal in a follow-up iter (tracked under Known Gaps).

### Time formatting

Reuse `_formatTrackingTime(iso)` (App.js line 11750). Renders as
"just now" / "Nm ago" / "Nh ago" / locale date.

### Audit / cross-link conventions

- Outcome rows that have a linked patch_proposal: render the anomaly_id
  as a button that calls `setActiveTab("patch_proposals")` and stores the
  target id in a module-level ref (or sessionStorage) the
  PatchProposalsDashboardView reads on mount to auto-scroll + highlight.
  Pattern: `window.dispatchEvent(new CustomEvent("chl:nav", { detail: "patch_proposals" }))`
  — App.js line 2208 already wires this hook.
- Patch proposal rows that have a known anomaly_id: render `anomaly_id`
  as a copy-to-clipboard click target (no destination tab yet — anomaly
  digest router lives elsewhere; cross-link is informational).

---

## Section A — Disputes Dashboard (/disputes)

### Backend (already shipped, EOD-7 Stream G)

- `backend/factors/dispute_handler.py` — detect / resolve / list pending
  disputes. ASCII docstring at top of file documents the schema.
- `backend/factors/dispute_router.py` — three endpoints, all behind
  `require_broker`:

```
GET  /api/factoring/disputes?factor_slug=&status=&limit=50
GET  /api/factoring/disputes/{dispute_id}
POST /api/factoring/disputes/{dispute_id}/resolve?resolution=cured|lost&notes=...
```

- 409 returned on already-terminal disputes (resolved_cured / resolved_lost).
- 404 returned on unknown dispute_id.

### Mongo schema fields used by UI

From `dispute_handler.py` (lines 311-332, 511-514):

```
id                  -- dispute uuid
submission_id       -- linked factor_submissions row
factor_slug         -- rts | otr | apex | triumph
dispute_marker      -- portal-issued ref, fallback to sha256[:16]
dispute_reason      -- human-readable explanation
cure_path           -- factor's cure instructions (nullable)
amount_at_risk_usd  -- float
status              -- pending | resolved_cured | resolved_lost
detected_at         -- iso8601
resolved_at         -- iso8601 (nullable)
resolved_by         -- operator email (nullable)
resolution_notes    -- str (nullable)
```

Plus `db.reconciliation_alerts` for clawback events on funded subs
(separate collection; out of scope for v1 dashboard but surfaced as
header-pill count).

### UI requirements

#### Top-bar header

- "Factor Disputes" title (Outfit font, matching InboxPanel line 12039).
- Inline pills next to title:
  - Total open disputes count (slate)
  - Per-factor breakdown: `RTS:N OTR:N APEX:N TRIUMPH:N` (mono, slate)
  - Clawback alert count: red pill with `clawback:N` (links to a future
    /clawbacks panel; v1 just shows the count)
- Right-side: refresh button (existing pattern).

#### Main table

Rows show:

- `dispute_id.slice(0,12)` (mono, slate-500)
- factor_slug pill (color per factor: RTS=blue, OTR=indigo, APEX=teal, TRIUMPH=violet — define `FACTOR_PILL` constant)
- dispute_marker (mono, slate-700)
- dispute_reason truncated to 80 chars (slate-700, no italic)
- amount_at_risk_usd formatted as `$1,234.56`
- detected_at via `_formatTrackingTime`
- status badge: `pending` (amber) / `resolved_cured` (emerald) / `resolved_lost` (red)

Click row → modal:

- Full dispute_reason (no truncation, monospace block)
- cure_path (if present) or "(no cure path returned by factor)"
- Two action buttons: "Mark Cured" (emerald) + "Mark Lost" (red)
- Notes textarea (resolve_notes, optional, 200-char limit)
- Resolve action POSTs to /resolve with resolution + notes
- 409 → toast "Already resolved by another operator (status: <X>)"; refresh closes modal
- 404 → toast "Dispute not found (deleted?)"

#### Filters

- Factor pill row above table: All / RTS / OTR / Apex / Triumph (single-select)
- Status pill row: Pending (default) / Cured / Lost / All
- Both filter rows POST query params to GET /api/factoring/disputes

#### Empty state

"No open disputes. Factors clean. ✓" (centered, slate-500)

#### Loading state

Refresh button shows `↻ refreshing…`; existing rows visible.

#### Error state

Top banner: "Failed to refresh disputes — <error.message>" with retry button.
Existing rows persisted.

### Component skeleton (paste-ready into App.js)

```jsx
// =============================================================================
// FACTOR DISPUTES DASHBOARD — EOD-10 Section A
// Surfaces EOD-7 Stream G (factor_disputes operator queue).
// Backend: backend/factors/dispute_router.py + dispute_handler.py
// API:
//   GET  /api/factoring/disputes?factor_slug=&status=&limit=50
//   GET  /api/factoring/disputes/{dispute_id}
//   POST /api/factoring/disputes/{dispute_id}/resolve?resolution=&notes=
// =============================================================================

const FACTOR_PILL = {
  rts:     "bg-blue-100   text-blue-800   border-blue-300",
  otr:     "bg-indigo-100 text-indigo-800 border-indigo-300",
  apex:    "bg-teal-100   text-teal-800   border-teal-300",
  triumph: "bg-violet-100 text-violet-800 border-violet-300",
  unknown: "bg-slate-100  text-slate-700  border-slate-300",
};

const DISPUTE_STATUS_PILL = {
  pending:         "bg-amber-100   text-amber-800   border-amber-300",
  resolved_cured:  "bg-emerald-100 text-emerald-800 border-emerald-300",
  resolved_lost:   "bg-red-100     text-red-800     border-red-300",
  unknown:         "bg-slate-100   text-slate-700   border-slate-300",
};

function DisputesDashboardView({ addNotification }) {
  const [disputes, setDisputes] = useState([]);
  const [byFactor, setByFactor] = useState({});
  const [clawbackCount, setClawbackCount] = useState(0);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  const [factorFilter, setFactorFilter] = useState(null);   // null = all
  const [statusFilter, setStatusFilter] = useState("pending");
  const [selected, setSelected] = useState(null);

  const refresh = useCallback(async () => {
    setLoading(true);
    setError(null);
    try {
      const params = { limit: 200 };
      if (factorFilter) params.factor_slug = factorFilter;
      if (statusFilter && statusFilter !== "all") params.status = statusFilter;
      const r = await axios.get(`${API}/factoring/disputes`, { params, withCredentials: true });
      const rows = r.data.disputes || [];
      setDisputes(rows);
      // Aggregate per-factor count from rows themselves (server doesn't pre-aggregate)
      const agg = {};
      rows.forEach((d) => { const k = d.factor_slug || "unknown"; agg[k] = (agg[k] || 0) + 1; });
      setByFactor(agg);
      // Clawback count via separate endpoint (v1 stub: 0; wire to recon_alerts in follow-up)
      // setClawbackCount(...)
    } catch (e) {
      setError(e?.response?.data?.detail || e.message);
    } finally {
      setLoading(false);
    }
  }, [factorFilter, statusFilter]);

  useEffect(() => {
    refresh();
    const id = setInterval(refresh, 300000);  // 5min
    return () => clearInterval(id);
  }, [refresh]);

  const resolve = async (dispute, resolution) => {
    const notes = window.prompt(
      `Mark this dispute as ${resolution}? Notes (optional):`,
      ""
    );
    if (notes === null) return;
    try {
      await axios.post(
        `${API}/factoring/disputes/${dispute.id}/resolve`,
        null,
        { params: { resolution, notes }, withCredentials: true }
      );
      addNotification?.(`Dispute ${dispute.id.slice(0, 8)} marked ${resolution}`, "success");
      setSelected(null);
      await refresh();
    } catch (e) {
      if (e?.response?.status === 409) {
        addNotification?.("Already resolved by another operator", "warning");
        await refresh();
      } else {
        addNotification?.(`Resolve failed: ${e?.response?.data?.detail || e.message}`, "error");
      }
    }
  };

  return (
    <div className="space-y-3" data-testid="disputes-dashboard">
      {/* Header + factor breakdown pills + refresh button */}
      {/* Factor + Status filter rows */}
      {/* Error banner if error */}
      {/* Main rows table (mirrors TrackingExceptionsPanel shape) */}
      {/* Modal when selected */}
    </div>
  );
}
```

### Open questions — Section A

- Q1. Should clawback alert count come from a new endpoint
  `GET /api/factoring/recon_alerts?type=clawback&limit=1` (count only)
  or extend `GET /api/factoring/disputes` response with a top-level
  `clawback_alert_count`? My read: extend the disputes response; one
  fewer round-trip and keeps related state co-located.
- Q2. When a dispute is `resolved_lost`, the invoice flips to
  `operator_attention`. Should the dashboard show a "Linked invoice
  needs attention" indicator on lost rows? (Out of scope for v1; track
  in known gaps.)

---

## Section B — Patch Proposals Dashboard (/patch_proposals)

### Backend (already shipped, EOD-6 Stream C)

- `backend/self_healing/sub_agent_dispatcher.py` — gate constants:
  - `REVIEW_PENDING_LOW = "pending_low_auto"`
  - `REVIEW_PENDING_MED = "pending_med_dev_dm"`
  - `REVIEW_PENDING_HIGH = "pending_high_operator_approval"`
  - `REVIEW_APPROVED`, `REVIEW_REJECTED`
- `backend/self_healing/patch_proposal_router.py` — three endpoints,
  behind `require_broker`:

```
GET  /api/_self_healing/patch_proposals?gate=HIGH&review_status=&limit=25
GET  /api/_self_healing/patch_proposals/{id}
POST /api/_self_healing/patch_proposals/{id}/approve  body: {notes}
POST /api/_self_healing/patch_proposals/{id}/reject   body: {notes}
```

- `llm_raw` is stripped server-side before returning (router line 52-61);
  do NOT expect it in API responses.

### Mongo schema fields used by UI

From `sub_agent_dispatcher.py` (lines 660-700, 825-895):

```
_id                          -- anomaly_id (idempotent on re-dispatch)
anomaly_id                   -- str
gate                         -- LOW | MED | HIGH
review_status                -- pending_low_auto | pending_med_dev_dm |
                                pending_high_operator_approval |
                                approved | rejected
proposed_at                  -- iso8601
first_proposed_at            -- iso8601 (set-on-insert)
proposal_count               -- int (incremented on re-dispatch)
llm_cost_usd_estimate        -- float
llm_skipped                  -- bool / str (reason if skipped)
requires_operator_approval   -- bool (true for HIGH gate)
affected_files               -- [str, ...]
gate_rationale               -- {rule, high_risk_hits, loc_delta, ...}
proposed_patch               -- str (unified diff text)
notes                        -- str (operator notes from approve/reject)
reviewed_at                  -- iso8601 (nullable)
reviewed_by                  -- operator email (nullable)
```

### UI requirements

#### Top-bar header

- "Patch Proposals" title.
- Filter chips (multi-select):
  - Gate: All / LOW / MED / HIGH (default: all 3 pending statuses)
  - Status: Pending (default) / Approved / Rejected / All
- Right side: refresh button + auto-refresh indicator (showing "auto 2min").

#### Main list — card per proposal

NOT a table; cards because each row needs more vertical real estate to
show gate_rationale + affected_files preview.

Card shows:

- Top row: gate badge (LOW=emerald / MED=amber / HIGH=red, large bold),
  anomaly_id (mono, copy-on-click), proposed_at (relative time)
- Affected files: count + first 3 paths (mono, slate-700; "+N more"
  expansion link if > 3)
- gate_rationale.rule (slate-700 italic) — the sentence describing
  WHY this gate level was assigned
- gate_rationale.high_risk_hits (if non-empty): red mini-pills listing
  each hit (e.g., "money_flow", "auth_code")
- Bottom row: llm_cost_usd_estimate (mono `$0.0123`),
  proposal_count (`re-proposed Nx`), requires_operator_approval flag
  (red dot if true)

HIGH-gate cards have `border-red-500 border-2` (operator's hard-look
surface).

#### Card click → expanded view (slide-down panel inline, not modal)

- Full gate_rationale (all keys, JSON-pretty-print)
- llm_skipped reason if any (e.g., "no_api_key", "cost_cap_exceeded")
- Full unified diff: use a simple `<pre>` block initially (no
  react-diff-viewer dependency); colorize +/- lines via CSS
  `:before { content }` matching pattern. Optional follow-up iter:
  pull in `react-diff-viewer-continued` if the operator finds the
  monochrome diff hard to scan.
- Action row at bottom:
  - Notes textarea (200-char, optional)
  - "Approve" button (emerald) — for LOW + MED, single-confirm modal
  - "Approve" button — for HIGH, two-stage confirm: type "APPROVE" to
    enable the actual approve button. This is the operator's hard
    guardrail.
  - "Reject" button (red) — single-confirm modal for all gates

#### Empty state

"No pending patch proposals. The self-healing dispatcher is quiet — either
no anomalies upstream or all proposals are reviewed. ✓"
(reassuring; this is the "clean dashboard" state)

#### Loading / Error states

Standard pattern from cross-cutting standards.

#### Cross-link

- anomaly_id is rendered as a copy-to-clipboard click target.
- (Forward-looking) Clicking it WOULD open the linked anomaly_digest;
  digest is at `/api/_self_healing/anomalies` but not yet surfaced in
  the UI. Track in known gaps.

### Component skeleton

```jsx
// =============================================================================
// PATCH PROPOSALS DASHBOARD — EOD-10 Section B
// Surfaces EOD-6 Stream C (sub_agent_dispatcher patch proposals).
// Backend: backend/self_healing/patch_proposal_router.py
// API:
//   GET  /api/_self_healing/patch_proposals?gate=&review_status=&limit=25
//   GET  /api/_self_healing/patch_proposals/{id}
//   POST /api/_self_healing/patch_proposals/{id}/approve  body: {notes}
//   POST /api/_self_healing/patch_proposals/{id}/reject   body: {notes}
// =============================================================================

const GATE_PILL = {
  LOW:     "bg-emerald-100 text-emerald-800 border-emerald-300",
  MED:     "bg-amber-100   text-amber-800   border-amber-300",
  HIGH:    "bg-red-100     text-red-800     border-red-300",
  unknown: "bg-slate-100   text-slate-700   border-slate-300",
};

function PatchProposalsDashboardView({ addNotification }) {
  const [proposals, setProposals] = useState([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  const [gateFilter, setGateFilter] = useState(null);          // null = all
  const [statusFilter, setStatusFilter] = useState("pending"); // sentinel
  const [expandedId, setExpandedId] = useState(null);
  const [pendingAction, setPendingAction] = useState(null);    // {id, action, notes}

  const refresh = useCallback(async () => {
    setLoading(true);
    setError(null);
    try {
      const params = { limit: 100 };
      if (gateFilter) params.gate = gateFilter;
      // statusFilter "pending" expands client-side to multi-call OR is filtered
      // server-side via a single review_status param; v1 picks the latter and
      // does 3 calls for "pending" merging client-side.
      const r = await axios.get(`${API}/_self_healing/patch_proposals`, { params, withCredentials: true });
      let rows = r.data.proposals || [];
      if (statusFilter === "pending") {
        rows = rows.filter((p) => (p.review_status || "").startsWith("pending_"));
      } else if (statusFilter !== "all") {
        rows = rows.filter((p) => p.review_status === statusFilter);
      }
      setProposals(rows);
    } catch (e) {
      setError(e?.response?.data?.detail || e.message);
    } finally {
      setLoading(false);
    }
  }, [gateFilter, statusFilter]);

  useEffect(() => {
    refresh();
    const id = setInterval(refresh, 120000);  // 2min
    return () => clearInterval(id);
  }, [refresh]);

  const submitAction = async () => {
    if (!pendingAction) return;
    const { id, action, notes } = pendingAction;
    try {
      await axios.post(
        `${API}/_self_healing/patch_proposals/${id}/${action}`,
        { notes: notes || "" },
        { withCredentials: true }
      );
      addNotification?.(`Proposal ${id.slice(0, 8)} ${action}d`, "success");
      setPendingAction(null);
      setExpandedId(null);
      await refresh();
    } catch (e) {
      addNotification?.(`${action} failed: ${e?.response?.data?.detail || e.message}`, "error");
    }
  };

  return (
    <div className="space-y-3" data-testid="patch-proposals-dashboard">
      {/* Header + gate / status chips + refresh */}
      {/* Error banner */}
      {/* Card list (HIGH-gate cards have border-red-500 border-2) */}
      {/* Expanded panel (gate_rationale + diff + action row) */}
      {/* Confirm modal when pendingAction (HIGH = type-APPROVE-to-enable) */}
    </div>
  );
}
```

### Open questions — Section B

- Q3. Should the diff viewer pull in `react-diff-viewer-continued`
  (~30KB gz) on day 1, or ship with monochrome `<pre>` and upgrade
  later? Recommend: monochrome v1; only upgrade if operator complains.
- Q4. The "type APPROVE to enable" pattern for HIGH-gate is slower than
  a single confirm. Is the friction welcome (operator's explicit
  guardrail) or annoying? Recommend keep — HIGH proposals are rare and
  the friction cost is small relative to the cost of an accidental
  approval.
- Q5. Notes-required vs notes-optional? Recommend optional for
  Approve, REQUIRED for Reject (operator should be able to explain
  rejections for the audit trail / future weight-tuning learning loop).

---

## Section C — Outcomes Dashboard (/outcomes)

### Backend (already shipped, EOD-7 Stream H + EOD-9 Stream L)

- `backend/self_healing/outcome_tracker.py` — record / list / metrics.
- `backend/self_healing/outcome_router.py` — four endpoints, behind
  `require_broker`:

```
POST /api/_self_healing/outcomes/{anomaly_id}     body: {outcome, observed_anomalies, notes}
GET  /api/_self_healing/outcomes/{anomaly_id}
GET  /api/_self_healing/outcomes?gate=&outcome=&limit=
GET  /api/_self_healing/outcomes/metrics?since_days=30
```

- 5 outcome values: `fixed_clean | fixed_with_regression | no_change | made_worse | rolled_back`
- Auto-version-increment on re-record (audit trail preserved)

Plus weight-tuning side panel:

- `backend/self_healing/weight_tuning_router.py`:

```
GET  /api/_self_healing/weight_proposals?evaluator_kind=&status=pending&limit=25
GET  /api/_self_healing/weight_proposals/{proposal_id}
POST /api/_self_healing/weight_proposals/{proposal_id}/approve
POST /api/_self_healing/weight_proposals/{proposal_id}/reject
GET  /api/_self_healing/weight_proposals/_health
```

### UI requirements

#### Top-bar — metric tiles

A 4-tile horizontal strip:

- LOW success rate (% with success_rate_overall fallback)
- MED success rate
- HIGH success rate
- Overall success rate

Each tile shows: tile label / large-num percent / small denom (e.g.,
"23 of 28 outcomes recorded"). Color-coded: ≥80% emerald, 60-80% amber,
<60% red.

Window selector pills above tiles: 7d / 30d (default) / 90d / all.
Selecting a window re-calls `/api/_self_healing/outcomes/metrics?since_days=N`
(or omits param for "all").

Below the tiles: per-outcome breakdown row, 5 mini-pills:

```
fixed_clean:N   fixed_with_regression:N   no_change:N   made_worse:N   rolled_back:N
```

Color: fixed_clean=emerald, fixed_with_regression=amber,
no_change=slate, made_worse=red, rolled_back=red.

#### Main outcome list

Paginated (limit=50 per page) table:

- anomaly_id (mono, click → patch_proposals tab w/ highlight)
- outcome badge (5 variants per above palette)
- gate badge (LOW/MED/HIGH)
- observed_anomalies count (mono; "(0)" most rows; ">=1" highlighted amber)
- recorded_at (`_formatTrackingTime`)
- recorded_by (slate-500)

Click row → drill panel showing:

- Full outcome history (all versions; ordered ascending by version)
- Per-version: outcome + recorded_at + recorded_by + notes + observed_anomalies
- Affected files (denormalized from patch_proposal at record time)

#### Sidebar — Pending Weight Adjustments

Right-side column (1/3 width on desktop, full-width stacked on mobile):

- "Pending Weight Adjustments" header
- Per-pending-proposal row:
  - evaluator_kind pill (carrier/load/lane)
  - feature_name (mono)
  - current → proposed delta (e.g., `0.45 → 0.52  +0.07`)
  - residual (small slate; the unexplained-variance metric)
  - Approve / Reject buttons (one-click confirm; same auth gate as
    patch_proposals; v1 modeled on simpler confirm-modal since
    weight-tuning impact is bounded)

Empty: "No pending weight adjustments. Evaluators are stable. ✓"

#### Empty states

- Outcomes empty: "Outcomes appear here as patches are recorded after
  operator approval. Until the first patch is approved + outcome
  captured, this dashboard reads empty — that's the steady state for
  early-iter CHL."
- Weight side panel empty: see above.

### Component skeleton

```jsx
// =============================================================================
// OUTCOMES DASHBOARD — EOD-10 Section C
// Surfaces EOD-7 Stream H (outcome_tracker) + EOD-9 Stream L (weight_tuning).
// Backend:
//   backend/self_healing/outcome_router.py
//   backend/self_healing/weight_tuning_router.py
// API:
//   GET /api/_self_healing/outcomes/metrics?since_days=30
//   GET /api/_self_healing/outcomes?gate=&outcome=&limit=
//   GET /api/_self_healing/outcomes/{anomaly_id}
//   GET /api/_self_healing/weight_proposals?status=pending
//   POST .../weight_proposals/{id}/approve  body: {notes}
//   POST .../weight_proposals/{id}/reject   body: {notes}
// =============================================================================

const OUTCOME_PILL = {
  fixed_clean:           "bg-emerald-100 text-emerald-800 border-emerald-300",
  fixed_with_regression: "bg-amber-100   text-amber-800   border-amber-300",
  no_change:             "bg-slate-100   text-slate-700   border-slate-300",
  made_worse:            "bg-red-100     text-red-800     border-red-300",
  rolled_back:           "bg-red-100     text-red-800     border-red-300",
  unknown:               "bg-slate-100   text-slate-700   border-slate-300",
};

const SUCCESS_RATE_TIER = (rate) => {
  if (rate == null) return "bg-slate-100 text-slate-700";
  if (rate >= 0.80)  return "bg-emerald-100 text-emerald-800";
  if (rate >= 0.60)  return "bg-amber-100   text-amber-800";
  return "bg-red-100 text-red-800";
};

function OutcomesDashboardView({ addNotification }) {
  const [metrics, setMetrics] = useState(null);
  const [outcomes, setOutcomes] = useState([]);
  const [weightProposals, setWeightProposals] = useState([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  const [windowDays, setWindowDays] = useState(30);
  const [drilledAnomalyId, setDrilledAnomalyId] = useState(null);
  const [drillHistory, setDrillHistory] = useState([]);

  const refresh = useCallback(async () => {
    setLoading(true);
    setError(null);
    try {
      const metricsParams = windowDays ? { since_days: windowDays } : {};
      const [m, o, w] = await Promise.all([
        axios.get(`${API}/_self_healing/outcomes/metrics`, { params: metricsParams, withCredentials: true }),
        axios.get(`${API}/_self_healing/outcomes`, { params: { limit: 100 }, withCredentials: true }),
        axios.get(`${API}/_self_healing/weight_proposals`, { params: { status: "pending", limit: 25 }, withCredentials: true }),
      ]);
      setMetrics(m.data);
      setOutcomes(o.data.outcomes || []);
      setWeightProposals(w.data.proposals || []);
    } catch (e) {
      setError(e?.response?.data?.detail || e.message);
    } finally {
      setLoading(false);
    }
  }, [windowDays]);

  useEffect(() => {
    refresh();
    const id = setInterval(refresh, 300000);  // 5min
    return () => clearInterval(id);
  }, [refresh]);

  const drillOutcome = async (anomalyId) => {
    try {
      // History across versions: backend doesn't currently expose a
      // "list versions" endpoint; v1 fetches latest only and renders
      // a single-row history. Track gap.
      const r = await axios.get(`${API}/_self_healing/outcomes/${anomalyId}`, { withCredentials: true });
      setDrillHistory([r.data]);
      setDrilledAnomalyId(anomalyId);
    } catch (e) {
      addNotification?.(`Drill failed: ${e?.response?.data?.detail || e.message}`, "error");
    }
  };

  const reviewWeightProposal = async (id, action) => {
    const notes = window.prompt(`${action} this weight proposal? Notes:`, "");
    if (notes === null) return;
    try {
      await axios.post(
        `${API}/_self_healing/weight_proposals/${id}/${action}`,
        { notes: notes || "" },
        { withCredentials: true }
      );
      addNotification?.(`Weight proposal ${id.slice(0, 8)} ${action}d`, "success");
      await refresh();
    } catch (e) {
      addNotification?.(`${action} failed: ${e?.response?.data?.detail || e.message}`, "error");
    }
  };

  return (
    <div className="space-y-3" data-testid="outcomes-dashboard">
      <div className="grid grid-cols-1 lg:grid-cols-3 gap-3">
        <div className="lg:col-span-2 space-y-3">
          {/* Window selector */}
          {/* 4 metric tiles */}
          {/* 5 outcome breakdown pills */}
          {/* Main outcome list */}
          {/* Drill panel when drilledAnomalyId */}
        </div>
        <div className="lg:col-span-1">
          {/* Pending Weight Adjustments side panel */}
        </div>
      </div>
    </div>
  );
}
```

### Open questions — Section C

- Q6. The metric tiles render percent values. When success_rate is
  null (no outcomes recorded for that gate), show "—" or "0%"? Recommend
  "—" (the absence of data is meaningful and shouldn't read as 0%
  failure).
- Q7. Outcome history: backend `GET /outcomes/{anomaly_id}` returns
  the latest version only. To render the full timeline, we either need
  a new `?include_history=1` flag on the GET endpoint OR a new
  `/outcomes/{anomaly_id}/history` endpoint. Recommend: extend
  existing GET with `include_history` query flag (lower-cost backend
  change). Track in known gaps.
- Q8. Weight-proposal residual is a math-y term. Tooltip explanation
  on hover? Recommend yes — short hover-tip "(unexplained variance
  after the proposed weight)" + link to weight_tuning_proposer.py
  module docstring.

---

## Sequencing recommendation

Build in this order if shipping serially:

1. **Disputes (Section A) FIRST.** It directly affects money flow.
   Disputes are already detected + persisted; without the dashboard the
   operator must hit the API endpoint manually or wait for a digest. This
   has the highest operator-touch removal score.
2. **Patch Proposals (Section B) SECOND.** Self-healing's primary
   surface; without it, the dispatcher is generating proposals that no
   one ever reviews — an open-loop liability. HIGH-gate proposals are
   the operator's hard-look surface and need fast eyeball-time.
3. **Outcomes (Section C) THIRD.** Informational dashboard; no
   operator action required for the loop to function in steady state.
   BUT outcomes is the feedback signal that closes the self-healing
   learning loop (feeds future weight-tuning), so should not be skipped
   indefinitely. Ship within the same week as Section B.

Rough effort estimate (assuming the established App.js inline-component
pattern + no new dependencies):

- Disputes: ~3-4h (modal + 3 API calls + filter rows + factor pills).
- Patch Proposals: ~5-7h (cards layout + diff render + two-stage
  HIGH-gate confirm + filter chips). Larger because the diff render
  needs careful CSS.
- Outcomes: ~5-7h (metric tiles + side panel + drill + 4 API calls).
  Larger because of the side-panel layout + window selector.

Total ~13-18h frontend work, single sub-agent serial; ~6-9h if dispatched
parallel to a single sub-agent (cross-module reuse of the pill +
formatting + refresh patterns).

## Cross-cutting standards (consolidated checklist)

Every dashboard must satisfy these gates before merge:

- [ ] Inline component definition in App.js (NOT a new view file)
- [ ] Tab id added to App.js tabs array (line ~2724)
- [ ] activeTab branch added (line ~2886) wrapped in
      `currentUser?.is_owner` for Patch Proposals + Outcomes
- [ ] Disputes can be open to all brokers (still gated by
      require_broker on backend; UI gate optional for now)
- [ ] Pill constant defined directly above the component
- [ ] Auto-refresh wired with cleanup (no leaked intervals)
- [ ] Loading / Error / Empty states present
- [ ] Write actions show confirmation before POST
- [ ] axios calls pass withCredentials: true
- [ ] data-testid on the root div + all clickable elements
- [ ] No console.log left in shipped code
- [ ] _formatTrackingTime reused (don't reinvent)
- [ ] addNotification?.(...) called for every success + failure path
- [ ] No new external deps introduced (no react-diff-viewer in v1)

## Known gaps + future-iter follow-ups

- G1. Clawback alerts (db.reconciliation_alerts) are referenced in
  Section A header but not rendered as a list. Track as iter
  follow-up: dedicated /clawbacks tab or modal-list within Disputes.
- G2. Anomaly-digest cross-link from patch_proposals (anomaly_id →
  digest detail) is not implemented; needs a new tab or modal that
  hits `/api/_self_healing/anomalies/{id}` (router exists per
  `backend/self_healing/anomaly_digest_router.py`).
- G3. Outcome history full timeline requires backend extension
  (Q7 above).
- G4. react-diff-viewer-continued upgrade for Patch Proposals
  diff render — defer until operator complains about monochrome.
- G5. Weight-tuning approval surfaces only the pending queue;
  approved + rejected history is not surfaced in v1. Track as
  follow-up: "Weight Adjustments History" sub-tab.
- G6. No SSE / websocket push for real-time proposal arrival;
  2min polling is the v1 contract. If HIGH-gate rate increases
  materially, upgrade to SSE pushed via the existing socketRef
  (App.js line 2595).
- G7. Mobile layout: the 4-tile metrics strip + side panel layout
  works on desktop; verify graceful stack on mobile during build.
- G8. CSV export (operator wants to dump pending disputes /
  proposals to spreadsheet for outside review): not in v1; add
  in EOD-11 batch.
- G9. Slack / DM relay: when a HIGH-gate patch lands or a clawback
  fires, push a notification through the existing
  addNotification path AND optionally to AgentDM @pm-lead so the
  bridge sees it without a tab visit. Track as iter follow-up.
- G10. `factor_matrix` view (App.js line 2886) already exists and
  is is_owner-gated; new dashboards should sit beside it visually
  in the tab bar (proximity to the existing operator-only cluster
  reduces operator cognitive load).

## Boot pointer for downstream agents

If you are a sub-agent picking up the implementation work for one
of these dashboards, your starting points are:

- App.js TrackingExceptionsPanel (line 11762) for table+ack pattern
- App.js InboxPanel (line 11986) for filter-tab + override pattern
- App.js tab registration (line 2724) for adding new top-bar entry
- App.js activeTab branch (line 2886) for adding new view-render
- Backend routers / handlers cited per section above (do NOT touch
  backend; design assumes endpoints as currently shipped)
- This spec is the contract; deviations require operator sign-off.

End of spec.
