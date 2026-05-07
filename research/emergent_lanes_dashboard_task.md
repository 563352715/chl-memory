# EMERGENT TASK: Lanes Dashboard Frontend Component

> **Source:** PM Claude (`@pm-lead`) via AgentDM message `1b707941-4bf9-4012-8aec-e53e7c0c25c0` on 2026-05-06.
> **Status:** Paste-ready prompt for Claude Emergent. Operator pastes the section below into Emergent's chat to kick off the build.

---

## Context
Continental Haul Logistics (CHL) is an autonomous freight brokerage currently in iter 141.1. We just shipped a backend endpoint `GET /api/lanes/top/{N}` that returns ranked freight lanes with composite scoring (profitability × reliability / risk). The operator can currently only hit this via curl — we need a React frontend UI to visualize this data.

## Your Mission
Build a new React component that fetches and displays the top 20 lanes in a sortable, color-coded table. This will be CHL's first operator-facing dashboard for lane opportunities.

## Technical Specs

### Backend Endpoint (Already Live)
```
GET /api/lanes/top/20
Authorization: Bearer {jwt_token}
```

**Response Format:**
> **NOTE (corrected from PM Claude's draft):** the actual endpoint returns `top_lanes` (not `lanes`) as the top-level array key, alongside `total_scored_lanes`, `n_requested`, `ranking_formula`, and `dat_status` siblings. Emergent should consume `response.top_lanes`, not `response.lanes`. The schema for each entry below is otherwise correct.

```json
{
  "top_lanes": [
    {
      "origin_city": "Chicago",
      "origin_state": "IL",
      "dest_city": "Dallas",
      "dest_state": "TX",
      "composite_score": 0.847,
      "profitability": 0.22,
      "reliability": 0.91,
      "risk": 0.04,
      "capacity": true,
      "opportunities_24h": 12,
      "historical_loads_90d": 45,
      "thresholds": {
        "profitability_min": 0.18,
        "reliability_min": 0.85,
        "risk_max": 0.08,
        "capacity_min": 20
      }
    }
  ],
  "dat_status": "live" | "deferred_to_iter_141_2"
}
```

### What to Build
**New File:** `frontend/src/LanesDashboard.jsx`

**Component Requirements:**
1. **Data Fetching:** Use AuthContext + existing API client
2. **Table columns:** Lane (city→city), Composite Score (3 decimals), Profitability %, Reliability %, Risk %, Capacity badge, Opportunities (24h), Historical (90d), Thresholds (collapsible)
3. **Sortable:** Click headers, default = composite_score desc
4. **Color coding:** Green (4/4 gates pass), Yellow (3/4), Red (<3)
5. **Controls:** Refresh button, "Adjust Thresholds" stub link
6. **DAT Banner:** If `dat_status !== "live"`, show "⚠️ DAT pending — opportunities_24h = 0, full data after May 13 FMCSA"
7. **Empty state:** "No passing lanes. Cron runs every 6h — check /api/health/system"

### App.js (2-line patch, operator approval required)
```javascript
import LanesDashboard from './LanesDashboard';
<Route path="/lanes" element={<LanesDashboard />} />
```

### Tests (3 smoke tests)
1. **Populated:** 5 lanes render with proper columns
2. **Empty:** Empty-state message shows
3. **DAT pending:** Banner shows when `dat_status === "deferred_to_iter_141_2"`

### Input Docs (for Emergent to read while building)
- `chl-memory/progress/stage_completion/iter_141_1_stage_1e.md` — endpoint spec source-of-truth
- `chl-memory/FRACTAL_DECISION_FRAMEWORK.md` — composite scoring rationale
- `chl-memory/CHL_STRATEGIC_PLAYBOOK.md` — broader context (Phase 2 Load Discovery)

### Constraints
- **No backend changes.** Endpoint is already live; just consume it.
- **One new file** (`LanesDashboard.jsx`) plus optional test file.
- **2-line App.js patch only** (operator-approval-per-edit; surface for review, don't merge directly).
- Match existing CHL frontend styling conventions (whatever Tailwind/CSS pattern App.js uses).

### Output Expected
1. `frontend/src/LanesDashboard.jsx` (the component)
2. App.js patch shown as a unified diff for operator review
3. Test file with the 3 smoke scenarios
4. Setup instructions (any new npm deps, build commands, etc.)

## Why This Matters
Pre-revenue broker. Operator needs visual lane ranking **this week** to scale from 5–10 loads/day (manual review) to 20–30 loads/day (dashboard-assisted decisions). **Clean and functional > fancy.** Speed to operator-usable beats polish.

---

**Operator instructions:**
1. Paste the section between the `---` separators above into Claude Emergent's chat
2. Emergent will produce: `LanesDashboard.jsx`, App.js diff, test file, instructions
3. Operator reviews the App.js diff (per the operator-approval-per-edit rule on App.js monolith)
4. If approved, apply the patch + drop the new component file in
5. Test on `http://localhost:3000/lanes` (frontend dev server)
6. Report results back via AgentDM so we can integrate into iter 141.1 close or iter 141.2 opening

— END OF EMERGENT PROMPT —
