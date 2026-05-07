# EMERGENT TASK: Threshold Tuning UI (MEDIUM PRIORITY)

> **Source:** PM Claude (`@pm-lead`) via AgentDM messages `f14d056a` + `b9143f9c` on 2026-05-06.
> **Status:** Paste-ready prompt for Claude Emergent.
>
> **⚠️ IMPORTANT NOTE:** The backend endpoints below (`GET /api/settings/thresholds`, `POST /api/settings/thresholds`, `GET /api/lanes/preview`) **DO NOT EXIST YET.** They need to be built before this dashboard works end-to-end. Recommend operator: **do NOT have Emergent build this until** the backend endpoints are scoped (could be iter 141.2 sub-task or a separate iter). Or: build with mocked responses + smoke tests, ship the UI, build the backend later.

---

## Context

CHL scores lanes using 4 thresholds. Currently they're only adjustable via direct MongoDB queries on `business_settings.lane_thresholds`. The operator needs a UI to tune thresholds without code changes.

## Backend Endpoints (need to be built — frontend may ship first with mocks)

```
GET /api/settings/thresholds
Response: {
  "lane_thresholds": {
    "profitability_min": 0.18,
    "reliability_min": 0.85,
    "risk_max": 0.08,
    "capacity_min": 20
  },
  "defaults": {...}
}

POST /api/settings/thresholds
Body: { "lane_thresholds": {...} }
Response: {"status": "updated", "lanes_passing": 15}

GET /api/lanes/preview?profitability_min=0.20&reliability_min=...&risk_max=...&capacity_min=...
Response: {
  "lanes_passing": 15,
  "change_from_current": -3,
  "top_lanes": [...]
}
```

## What to Build

**File:** `frontend/src/ThresholdTuner.jsx`

**Component Structure:**
1. **Current vs New Comparison (top)** — two columns side-by-side, highlight changed values in yellow
2. **Threshold Sliders (left)** — Profitability `10–30%`, Reliability `70–95%`, Risk `5–15%`, Capacity `10–50` loads/day
3. **Impact Preview (right)** — "X lanes pass (±Y change from current)", top-5 lanes that would pass under proposed thresholds. Debounced 500ms after slider change.
4. **Action Buttons** — "Reset to Defaults", "Save Thresholds" (opens confirmation modal)
5. **Save Confirmation Modal** — shows the diff (current → new) + impact summary
6. **Success Toast** — "Thresholds updated! N lanes now passing"

**Acceptance Criteria:**
- 4 sliders with live numeric values
- Impact preview debounced (no thrashing the API on every slider tick)
- Current vs new comparison highlights changes
- Reset-to-defaults button works
- Save modal confirms before commit
- Success toast appears on save
- Smoke tests for: load existing thresholds, modify sliders, preview updates, save commits

## App.js (2-line patch — operator-approval-per-edit required)

```javascript
import ThresholdTuner from './ThresholdTuner';
<Route path="/settings/thresholds" element={<ThresholdTuner />} />
```

## General Constraints (apply to both Emergent tasks)

- **No backend changes** — the spec assumes endpoints exist (they don't yet for either dashboard); Emergent builds the frontend; backend follows in iter 141.2 stage 1f for auto-bid; threshold-tuner backend may be later
- **Match CHL style** — inspect App.js patterns (Tailwind / CSS / component structure)
- **Operator approval required for App.js edits** (per operator-approval-per-edit monolith rule)
- **Mobile-friendly responsive tables** — solo operator may review on phone
- **Use existing auth** — AuthContext + API client patterns from App.js

## Why This Matters

Thresholds drive autonomous bidding decisions. Currently, tuning requires direct MongoDB queries — error-prone, gates operator from rapid experimentation. UI converts threshold tuning from "engineering task" to "operator workflow." Faster iteration on what counts as a "good lane" → faster path from $0 → $1,200+/2wk.

— END OF EMERGENT PROMPT —

---

**Operator instructions:**
1. Paste the section between the `---` separators above into Claude Emergent's chat
2. Emergent produces: `ThresholdTuner.jsx`, App.js diff, smoke tests (likely with mocked backend responses since endpoints don't exist yet)
3. Review App.js diff before merge
4. **DECISION POINT:** scope the threshold-tuner backend endpoints into iter 141.2 stage 1f? Or defer to a later iter? Without backend, tuner is read-only / non-functional.
