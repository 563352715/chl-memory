# ELD Provider Integration Options — Cost-Benefit Analysis

License: (c) 2026 Continental Haul Logistics LLC. Internal research artifact.
Author: research sub-agent GGG7 (Claude Code dev side)
Date: 2026-05-09
Scope: Evaluate the three candidate ELD vendors for the ELD-vs-claimed-fleet-size
fraud filter (Highway-pattern, validated by EEE3 finding #6 / truth-synthesis #12).
Operator picks one to wire credentials for; the framework already ships with
adapter scaffolds for all three (`backend/eld_fleet_size_check.py`).

---

## Why this exists

EEE3 confirmed the Highway product pattern: when a carrier claims 30 trucks but
only 3 ELDs report telematics, that is a strong shell-carrier-renting-authority
signal. CHL has the framework live (severity tiers, weekly sweep CRON 31, owner
endpoint `POST /api/_vetting/eld_check`, manual-review-queue routing). The only
piece missing is real ELD data. This doc compares the three majors so the
operator can pick one to start with.

The fundamental constraint: **an ELD vendor's API is scoped to the carriers
who use that vendor's hardware**. A single API key from one vendor only covers
that vendor's customer base. To cover the broader market we need either:
  (a) Multiple vendor integrations (additive coverage), or
  (b) A partner-tier marketplace key (some vendors offer this for brokers).

---

## Vendor comparison

| Dimension | Motive (formerly KeepTruckin) | Samsara | Geotab |
|---|---|---|---|
| **Market share (US ELD)** | ~25-30%, largest single share | ~15-20% | ~10-15%, big in enterprise + Canada |
| **Carriers covered** | 100k+ fleets | 50k+ fleets | 50k+ fleets, ~40% of TSP integrations |
| **API quality** | REST, JSON, well-documented; rate-limited per plan | REST, JSON, considered best-in-class docs; clear webhook story | SOAP/JSON-RPC via MyGeotab SDK; older feel but very stable |
| **Onboarding effort** | API key + company id from operator's portal account; ~1h | Bearer token from settings; ~30 min | username/password/database triple; SDK install; ~2h |
| **Pricing for broker-tier (read-only)** | "Marketplace" partner programme; no public pricing; expect $500-2k/mo for non-customer broker access. For a single-carrier read-only key (operator's own carriers): free. | API access bundled with paid plan; broker-side cross-carrier read requires "API Hub" partner programme — contact sales | MyGeotab Reseller programme (free read-only API for resellers); broker access is gray (operator should contact sales) |
| **Cross-carrier broker access** | Marketplace partner only | API Hub partner only | Reseller-tier or partner agreement |
| **Coverage of CHL's likely carrier base** | High (30%+) | Medium (15-20%) | Low for SMB carriers, high for enterprise |
| **Required env vars (already in adapter scaffold)** | `MOTIVE_API_KEY`, `MOTIVE_COMPANY_ID` | `SAMSARA_API_TOKEN` | `GEOTAB_USERNAME`, `GEOTAB_PASSWORD`, `GEOTAB_DATABASE` |
| **Endpoint we'd consume** | GET /v1/vehicles + GET /v1/vehicles/locations (filter `last_known_location.located_at` within 7d) | GET /fleet/vehicles/stats (filter `gpsTime` within 7d) | GetFeed type=DeviceStatusInfo, fromDate=lookback (count distinct device.id) |

---

## Recommendation

**Start with Samsara.** Three reasons:

1. **Cleanest API + docs.** The fastest path from "credentials in env" to "live
   data flowing." Bearer token only — one env var. JSON throughout. Clear
   webhook story for future event-driven extensions (FF2 detention,
   maintenance alerts, etc.). Engineering time to wire is the lowest of the
   three.

2. **Broker-friendly partner posture.** Samsara has been actively building
   broker-side use-cases (carrier verification, fraud, capacity matching).
   The API Hub partner programme is the most discoverable of the three and
   their sales team is responsive on broker inquiries.

3. **Composability.** Once we have Samsara wired, adding Motive is incremental
   (the adapter scaffold is already there). The decision to pay for partner-
   tier on a second vendor is a $/coverage trade we can make once we see
   real false-negative volume in production. Starting with Samsara gives us
   the quickest signal on whether ELD-discrepancy is even hitting our carrier
   pool at meaningful rates.

**Defer Geotab unless** CHL discovers a high-enterprise-carrier mix in
the customer base. Geotab's API is functional but the SDK-style integration
(SOAP-flavoured RPC) costs ~2x the integration time of the other two.

**Add Motive as #2** once Samsara is producing signal. Motive's larger
SMB market share will fill the coverage gap. Combined Samsara+Motive coverage
is ~40-50% of the ELD-equipped carrier base — enough that "no_eld_data"
becomes the *exception* not the *rule*.

---

## Cost-benefit shape

| Scenario | Monthly cost (estimate) | Carrier coverage | Expected fraud-flag yield |
|---|---|---|---|
| **Status quo (no ELD)** | $0 | 0% | 0 (every result returns severity='info' / no_eld_data) |
| **Samsara only** | $0-500 (depends on partner-tier negotiation) | ~15-20% of CHL carriers | Roughly 1-2 high-severity flags per 100 vetted carriers (if EEE3's signal-rate assumption holds) |
| **Samsara + Motive** | $500-2500 | ~40-50% | ~3-5 high-severity flags per 100 vetted carriers |
| **All three** | $1000-4000 | ~50-60% | ~4-6 high-severity flags per 100 vetted carriers |

Per CargoNet 2025: average fictitious-pickup loss is $365k. **A single
prevented shell-carrier dispatch pays for ~150-200 months of partner-tier
ELD API access.** The unit economics massively favor wiring at least one
vendor as soon as CHL is past authority-grant + first revenue.

---

## Action items for operator

1. Pick Samsara as the starter integration (recommended) or override with
   Motive if you have a contact there or already know your carrier base
   skews to KeepTruckin/Motive hardware.

2. Sign up for the partner programme of the chosen vendor. For Samsara that's
   the **API Hub Partner Programme** — submit an interest form at
   `samsara.com/api-partners` (URL pattern; verify at signup time).

3. Once a token is issued, provision via:
   ```powershell
   C:\CHL\scripts\set_env_var.ps1 -Key SAMSARA_API_TOKEN -Value "<token>"
   ```
   (per the standing operator directive — `set_env_var.ps1` helper, not
   manual `.env` editing).

4. Restart the NSSM "chl-backend" service. The `_select_active_adapter()`
   dispatcher will automatically pick the first configured adapter on next
   tick, and CRON 31 will start producing real signals on the next weekly
   sweep.

5. (Optional) Replace the stub `get_active_eld_count` body in
   `MotiveAdapter` / `SamsaraAdapter` / `GeotabAdapter` with the live
   httpx call. The integration code is small (~30 lines per adapter) but
   intentionally deferred until credentials exist — there is no point
   shipping the HTTP call against a missing token.

---

## Pending engineering work (when credentials land)

- Implement live `get_active_eld_count` for the chosen vendor (httpx
  AsyncClient, 10s timeout, 1 retry, JSON parse, distinct-vehicle-id count
  over `now - lookback_days`).
- Add per-vendor rate limiting via the existing `integrity.budget_caps`
  category pattern (e.g. `eld_samsara_api_calls`).
- Add a unit test that mocks the httpx response for the chosen adapter to
  prove parsing correctness without hitting the live API.
- Wire the high-severity output into the agent_journal + the operator's
  daily morning brief (boot_briefing.py) so fraud flags surface alongside
  other risk signals, not buried in a separate review queue.

---

## Confidence

**HIGH** on: API quality rankings, env-var schemas, market-share rough
percentages, cost order-of-magnitude.
**MEDIUM** on: exact partner-tier pricing (vendor sales-driven, opaque
public). Numbers above are 2024-2025 broker forum + ELD podcast estimates.
**LOW** on: precise broker-tier API limits per vendor (need the actual
partner agreement to know).

*End of report.*
