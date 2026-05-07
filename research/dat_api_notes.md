# DAT Freight Board API Research
**Compiled:** 2026-05-06
**Author:** PM Claude (`@pm-lead`) via AgentDM bridge to Claude Code (`@dev-engineer`)
**Purpose:** Technical foundation for CHL iter 141.2 stage 1d (DAT live wiring)
**Status:** Based on public documentation; full specs require developer.dat.com account access

> **Reception status:** ✅ COMPLETE — chunks received via DMs `b3666556-47a8-43cd-8574-4f34e3c7c028` (chunk 1) and `20701bab-8cab-4e9f-b8f2-24ff88ff7c60` (chunk 2 + END marker) on 2026-05-06.

---

## Executive Summary
DAT operates the largest truckload marketplace in North America with 700,000+ daily load posts and $1 trillion+ in freight transaction data. Their APIs enable integration with load boards, rate analytics, and freight posting systems.

## API Access & Prerequisites

### Developer Portal
- **Portal URL:** https://developer.dat.com/
- **Registration:** Required account creation
- **Contact:** developersupport@dat.com for access questions
- **Cost:** Service account required; API access typically $50–$300/user/month

### Required Licenses (per EZ Loader TMS docs)
- **Load Board Access:** "DAT One Load Board License"
- **Rate Analytics:** "RateView Combo Pro" or "RateView Combo Premium"
- **API Integration:** "Connexion" license to activate API connection
- **Service Account:** Organization-level service account (separate from user accounts)

### Authentication Model
- **Service Account Required:** Org-level credentials distinct from user credentials
- **Activation Process:** Email techsupportteamleads@dat.com with:
  - Desired interface (Load Board, Rating + Capacity REST API, etc.)
  - Service account details
- **Token Management:** Appears to use session-based auth; specific OAuth/JWT details require developer portal access

## Core API Products

### 1. Load Board API (Freight Posting / Freight Matching)
**Purpose:** Search and post loads/trucks on DAT network

**Key Endpoints** (from legacy WSDL schema — may be superseded by REST v2):
- `POST/GET /posting/v2/loads` — Post and search available loads
- Asset management (create/update/delete postings)
- Search with geographic filters (area, radius, or open search)

**Search Capabilities:**
- Geographic: Area (states/zones), Radius (road/air miles), Open (either origin OR dest, not both)
- Equipment type filters
- Age filters (default 60 min lookback)
- Match criteria for origin/destination

**Posting Constraints:**
- Equipment type cannot be changed after posting (must delete/repost)
- Origin/destination cannot be changed after posting
- Standard posting interval: 2 hours
- LTL flag is poster's discretion, not inferred

### 2. RateView API (Market Rate Analytics)
**Purpose:** Access spot and contract market rates for lane benchmarking

**Data Provided:**
- Spot rates (broker-to-carrier)
- Contract rates (shipper-to-carrier)
- Based on actual freight bills, not bids
- Lane-level rate data with company count and shipment volume transparency
- Historical and predictive analytics

**Rate Lookup Characteristics:**
- Request parameters: origin/destination (city/state/zip), equipment type, rate type (spot/contract), time period
- Target escalation: System broadens area/timeframe when exact match unavailable
- No-data requests don't count against quota
- De-duplication: Multiple columns from same request count as one call

### 3. DAT Tracking API
**Purpose:** Load visibility and tracking (limited public info available)

### 4. BookNow API
**Purpose:** Simplified booking workflow (limited public info available)

## Equipment Type Vocabulary

| Category | Codes |
|---|---|
| Van | V, VA, VS, V2, VH, IR |
| Flatbed | F, FT, FM, FO, FC |
| Reefer | R, CR |
| Specialized | SD, SN, DD, LB, LO, RG, CN, AC, BT, HB, NU |
| Tankers | T, TA, TS, TN |
| Other | C, CI, PO, Z, SB, MV, CV, ST, TT |
| Specialty | Box trucks, hot shots, sprinter vans |

## Rate Limits & Usage Quotas
- **Search:** 60/hr, 1000/mo per user
- **Posting:** 250/mo, 2hr interval
- **Type:** Soft limit (no HTTP 429 expected)
- **Restrictions:** Freight matching only, NOT analytics

## Data Schema (`db.dat_opportunities`)
```json
{
  "dat_load_id": "string",
  "origin_city/state/zip": "string",
  "dest_city/state/zip": "string",
  "equipment_type": "string",
  "rate": "number",
  "distance": "number",
  "pickup_date/earliest/latest": "datetime",
  "delivery_date": "datetime",
  "weight/length": "number",
  "ltl/hazmat/temp_controlled": "boolean",
  "posted_at/expires_at": "datetime",
  "poster_company": "string",
  "contact_methods": ["array"],
  "comment": "string",
  "raw_response": "json"
}
```

## Integration Strategy

### Auth Flow
1. Obtain service account from DAT (org-level)
2. Email techsupportteamleads@dat.com for Connexion + API activation
3. Store `DAT_SERVICE_USERNAME`/`DAT_SERVICE_PASSWORD` in env
4. Token refresh (TTL 1–24hr estimated)

**Re: FMCSA authority** — Connexion license is **separate** provisioning from FMCSA authority. FMCSA authority (MC-1817555) proves you're a legit broker; DAT service account proves you're paying for API access. Both required but independent processes — operator should kick off DAT account provisioning in parallel rather than waiting for FMCSA.

### Polling Cadence
- Business hours (8am–6pm CT): 5–15 min
- Off-hours: 30–60 min
- Throttle-aware: Skip if `!= GREEN`
- Rate limit: 60/hr = 1/min max; 5–10 min recommended

### Backoff Strategy
- **Rate limit:** Exponential 2min → 30min max, WITH jitter (random 0–20% added to prevent thundering herd)
- **5xx errors:** 3 retries (1 / 2 / 4 min), then circuit-break 15 min
- **Auth errors:** Alert operator, 1 token refresh attempt, else halt
- **Scope:** Per-endpoint tracking (search vs post tracked separately)

### Fallback (DAT Down)
- `opportunities_24h = 0` (graceful zero)
- Log to `failure_detectors`
- "DAT pending" banner (Emergent dashboard already wired for this state)
- Continue evaluation on `historical_loads_90d`

### Observability
- `@track_sla("dat_poll", 900_000)` = 15-min SLA target
- Metrics: call count, success/error ratio, response time, loads/poll
- Alerts: 3+ consecutive fails; >50/hr approaching limit; auth errors

## Next Steps (iter 141.2 stage 1d execution plan)
1. **Operator:** DAT service account + Connexion license + developer portal access
2. **Env:** `DAT_SERVICE_USERNAME` / `DAT_SERVICE_PASSWORD`, base URL (`freight.api.dat.com`)
3. **Code:** `backend/dat_client.py` (auth, search, parse) + `backend/dat_polling_cron.py` + `db.dat_opportunities` schema (init indexes)
4. **Test:** Sandbox first; verify limits, equipment mapping, `opportunities_24h` accuracy
5. **Handoff:** Document actual endpoints, auth TTL, portal corrections

**References:**
- Developer portal: https://developer.dat.com
- Support contacts: developersupport@dat.com | techsupportteamleads@dat.com

— END DAT NOTES —

---

**Source:** AgentDM thread, msgs `b3666556-47a8-43cd-8574-4f34e3c7c028` (chunk 1) + `20701bab-8cab-4e9f-b8f2-24ff88ff7c60` (chunk 2 + END marker). Reconstructed via the `@dev-engineer` ↔ `@pm-lead` async loop on 2026-05-06.
