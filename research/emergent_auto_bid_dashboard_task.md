# EMERGENT TASK: Auto-Bid Review Dashboard (HIGH PRIORITY)

> **Source:** PM Claude (`@pm-lead`) via AgentDM messages `71e20015` + `17654ec1` on 2026-05-06.
> **Status:** Paste-ready prompt for Claude Emergent. Operator pastes the section below into Emergent's chat to kick off the build.
>
> **⚠️ IMPORTANT NOTE:** The backend endpoints below (`/api/bids/pending`, `/api/bids/{id}/approve`, `/api/bids/{id}/reject`, `/api/bids/history`) **DO NOT EXIST YET.** They will be built in **iter 141.2 stage 1f** (auto_bid module). Emergent will produce a working frontend that consumes endpoints which the backend won't serve until stage 1f ships. Frontend-built-first is intentional — iter 141.2 stage 1f spec assumes the dashboard already exists and operator can review pending bids during the first 48h gate. After stage 1f ships, the dashboard works end-to-end.

---

## Context

Continental Haul Logistics is launching autonomous bidding (iter 141.2 stage 1f). This React dashboard is a critical operator tool that needs to ship alongside the backend automation.

**Tech Stack:** React + existing CHL frontend patterns (check App.js for AuthContext, API client, Tailwind conventions).

## Your Mission

Stage 1f will autonomously bid on 5–10 freight lanes per day based on composite scores. For the first 48 hours, the operator needs a manual approval gate plus visibility into what the system is bidding on.

Build a React dashboard where the operator can review pending bids, approve/reject them, and track bid outcomes.

## Backend Endpoints (will be built in iter 141.2 stage 1f — frontend ships first)

```
GET /api/bids/pending
Authorization: Bearer {jwt}
Response: {
  "pending_bids": [
    {
      "bid_id": "bid_abc123",
      "lane": "Chicago, IL → Dallas, TX",
      "composite_score": 0.847,
      "bid_amount": 2250,
      "margin": 0.22,
      "profitability": 0.22,
      "reliability": 0.91,
      "risk": 0.04,
      "created_at": "2026-05-13T10:30:00Z",
      "expires_in_minutes": 45,
      "load_details": {
        "origin": "Chicago, IL",
        "destination": "Dallas, TX",
        "distance": 925,
        "equipment": "Van"
      }
    }
  ]
}

POST /api/bids/{bid_id}/approve
Response: {"status": "approved", "sent_at": "2026-05-13T10:31:05Z"}

POST /api/bids/{bid_id}/reject
Body: {"reason": "string"}
Response: {"status": "rejected"}

GET /api/bids/history?days=7
Response: {
  "history": [...],
  "stats": {
    "total_bids": 42,
    "win_rate": 0.38,
    "avg_margin": 0.21
  }
}
```

## What to Build

**File:** `frontend/src/BidReviewDashboard.jsx`

**Component Structure:**
1. **Stats Summary Bar (top)** — Today's Bids, Win Rate %, Avg Margin %
2. **Pending Bids Section** — Table: Lane, Score, Bid $, Margin %, Expires In (countdown timer with color), Actions (Approve/Reject)
3. **Bid History Section** — Table: Lane, Bid $, Outcome (badge), Sent At, Margin %
4. **Modals** — Approve confirmation, Reject reason input
5. **Safety Features** — Empty state, bulk reject, `AUTO_BID_ENABLED` check
6. **Auto-Refresh** — 30s interval, visual timestamp

**Acceptance Criteria:**
- Pending table renders correctly
- Approve + Reject modals work
- Countdown timer changes color as expiration approaches
- Auto-refresh fires every 30s
- History filter works
- Stats summary computes correctly
- Empty state displays when no pending bids
- Smoke tests for populated/empty/auto_bid_disabled states

## App.js (2-line patch — operator-approval-per-edit required)

```javascript
import BidReviewDashboard from './BidReviewDashboard';
<Route path="/bids" element={<BidReviewDashboard />} />
```

## Why This Matters

CHL is pre-revenue, targeting $1,200+ / 2-week operator pay. Stage 1f launches autonomous bidding this month. Without this UI, the operator can't review/approve bids (curl only) — auto-bid ships blind, which is unacceptable risk.

This dashboard is an **operator safety gate** for autonomous operations.

— END OF EMERGENT PROMPT —

---

**Operator instructions:**
1. Paste the section between the `---` separators above into Claude Emergent's chat
2. Emergent produces: `BidReviewDashboard.jsx`, App.js diff, smoke tests
3. Review App.js diff (per operator-approval-per-edit rule on App.js monolith)
4. If Emergent says backend endpoints aren't responding (which they won't until iter 141.2 stage 1f), have it use mocked responses for the smoke tests
5. Merge once iter 141.2 stage 1f backend lands; dashboard then works end-to-end
