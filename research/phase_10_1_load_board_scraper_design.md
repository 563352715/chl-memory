# Phase 10.1 — Load-Board Scraper Bootstrap (Design Doc)

**Status:** DESIGN + SCAFFOLD ONLY. Captured 2026-05-08 EOD-15. Operator-authorized via autonomous-continue mode (Phase 10 captured in `phase_10_statistical_analytics_and_hardware_plan.md`).

**Scope:** Stand up the load-board ingest substrate so historical data starts accumulating EARLY (per Phase 10 build-sequencing recommendation). Live scraping is gated behind a feature flag and remains OFF until the operator green-lights a specific source. NO live network I/O is performed by this stage.

**Deliverables (this stage):**
- This design doc (committed to chl-memory).
- `backend/scrapers/__init__.py` (package marker).
- `backend/scrapers/loadboard_scraper.py` (~300 LOC, scaffolded — feature-flag gated, parser stubs).
- `backend/tests/test_loadboard_scraper.py` (5 unit tests, all pass with feature flag OFF).
- `backend/cron_scheduler.py` updated with a TODO comment for the future hourly cron registration (no actual cron registered).

---

## 1. Problem statement

### 1.1 Why we need external load-board data

The CHL platform's downstream Phase 10 analytics layer (seasonality decomposer, lane recommender, MarketEvaluator, carrier home-time model, rate forecaster) requires a LARGE, BROAD, TIME-SERIES dataset of freight market activity. Our internal `loads` collection captures only what we BOOKED — that is statistical noise (~10s to 100s of loads/week early on, plus selection bias toward what we chose to engage). For real signal we need the population: every visible load on the major boards, persisted continuously, so we can build models on:

- **Lane discovery** — origin/destination pairs that persistently have demand even when we don't book them.
- **Rate trend forecasting** — average $/mile for a lane over rolling windows; year-over-year seasonality.
- **Deadhead-minimization opportunities** — pairing booked loads with same-region next-load options so a carrier we just delivered isn't running empty.
- **National workload control** — operator-facing dashboard showing where freight is moving in near-real-time across the country.
- **Carrier home-time modeling** — combining accept/decline history (internal) with available-load geography (external) to predict carrier preferences.

### 1.2 What models depend on this

| Downstream consumer | Phase | What it reads |
|---|---|---|
| `seasonality_decomposer` | 10.3 | `loadboard_listings` aggregated to `lane_metrics_daily` (12+ months) |
| `rate_forecaster` | 10.3 | `loadboard_listings` aggregated by lane (rolling window) |
| `lane_recommender` | 10.5 | `loadboard_listings` clustered by origin/dest pair (rolling 30-90d) |
| `MarketEvaluator` (FractalEvaluator MACRO scale) | 10.6 | Aggregated outputs of the above (post-decompose) |
| `carrier_home_time_model` | 10.4 | INTERNAL data primarily; external geography as feature |

### 1.3 Freshness requirements

- **Hourly is sufficient for v1.** Load-board listings churn fast (a load posted at 09:00 may be booked by 11:00) but the SIGNAL we want — average rates, lane activity, geographic distribution — is stable over hour-scale windows.
- **Future:** if the Market Intelligence dashboard wants to show "what's hot RIGHT NOW," we tighten to 15 min. Defer until operator surfaces that need.
- **Backoff on rate-limit:** if a source returns 429 / blocks our IP, back off to every 4-6 hours and DM the operator.

### 1.4 What this stage explicitly does NOT do

- Does NOT call any external endpoint (no network I/O at all).
- Does NOT register a cron loop yet.
- Does NOT enable the feature flag.
- Does NOT pick a SPECIFIC source's parser (per-source parsers ship in 10.1.1+ only after operator-authorized source selection).
- Does NOT attempt to write data to `loadboard_listings` collection in production (only in tests, against a stub DB).

---

## 2. Candidate sources (4-6 evaluated)

| # | Source | Data availability | ToS red flags | Freshness | Coverage | Cost |
|---|---|---|---|---|---|---|
| 1 | **DAT One Load Board** | Paid REST API (`developer.dat.com`); requires service account + multiple licenses | LOW (we'd be a paying customer with formal API access) | Real-time | LARGEST in NA (700K+ daily posts) | $50-300/user/month + license fees |
| 2 | **Truckstop.com** | Paid REST API (Carrier and Shipper) | LOW (formal API) | Real-time | Large (#2 board) | Paid (similar tier to DAT) |
| 3 | **123Loadboard** | Paid REST API + lower-cost subscription tier | LOW (formal API) | Real-time | Medium | $35-99/month (broker tier) |
| 4 | **Loadlink (Canada)** | Paid; Canadian-focused | LOW | Real-time | Canadian-heavy | $99+/month |
| 5 | **Brokerage public RSS feeds** (eg some mid-size brokerages publish feeds) | PUBLIC (no auth) | LOW (intended for public consumption) | Per-feed (varies; usually 15-60 min refresh) | NARROW (one brokerage's loads at a time; aggregating 20+ feeds adds up) | FREE |
| 6 | **Public-web scraping** (any board's public anonymous-search results) | TECHNICALLY accessible without auth on some boards | HIGH (most ToS prohibit automated scraping; legal risk + IP-ban risk) | Real-time | Varies | Proxy costs ~$10-30/month |

### 2.1 Discussion

**DAT** is the gold standard for breadth and freshness, but the activation path (`developer.dat.com` portal, service-account paperwork, license fees, `techsupportteamleads@dat.com` email loop) is multi-week and requires ~$200-500 startup commitment before any data flows. Per `dat_api_notes.md` operator has not yet signed the service-account agreement.

**Truckstop.com / 123Loadboard** have similar activation friction.

**Public RSS / brokerage feeds** are FREE, ToS-clean, and INSTANTLY accessible. Coverage is narrower than DAT, but for v1 model-bootstrap it provides representative signal across many regions if we aggregate ~10-20 feeds. Critically, this gives us a VALID, ETHICAL data flow we can run TODAY without operator-side vendor signups.

**Public-web scraping** of paid boards is a no-go. The ToS exposure + IP-ban risk + legal risk all materialize before any analytics value does. We only scrape what's ETHICALLY available.

---

## 3. Recommended starting source

### **Aggregated public RSS feeds from a curated brokerage list (10-20 feeds).**

**Justification (1 line):** zero activation cost / zero ToS risk / immediately operational / sufficient signal-density for v1 model bootstrap; we upgrade to DAT (or similar paid API) in Phase 10.1.2 once operator-authorized.

**Concrete starter feed list (to be vetted by operator before live ingest enabled):**

- Convoy public load board (deprecated but historical-data-mining still valuable — confirm at activation time)
- Coyote Logistics public board feed
- Echo Global Logistics public board
- Arrive Logistics public board
- Loadboard public RSS aggregators (eg `loads.com`, `freightwaves.com` if they publish feeds)
- 5-10 mid-size broker feeds from operator's existing-relationships shortlist

### Rationale for choosing public RSS first

1. **Reversibility** — if an aggregated feed turns out to be useless data, we drop it and move on. No contract, no licensing.
2. **ToS-clean** — RSS is INTENDED for automated consumption; we're using it as the publisher intends.
3. **Pace-matched to model-readiness** — by the time we have 6+ months of accumulated data needed for seasonality decomposition, we can have layered DAT on top for breadth without delaying the bootstrap.
4. **Operator-time-saving** — no service-account paperwork blocking us.

### When to upgrade to DAT (or similar paid)

- Trigger 1: 10+ booked loads/week steady state (revenue justifies the $50-300/month spend).
- Trigger 2: aggregated RSS coverage demonstrably misses lanes we care about.
- Trigger 3: operator-authorized any time post-revenue.

---

## 4. Schema — `loadboard_listings` collection

### 4.1 Field design

```python
{
    # ---- identity ----
    "id": "<uuid4 hex>",                       # CHL-internal id (primary key)
    "dedup_hash": "<sha256 hex>",              # sha256(source + source_listing_id + posted_at_iso)

    # ---- source attribution ----
    "source": "coyote_rss",                    # short-name; matches a registered source in scraper config
    "source_listing_id": "<feed-provided id>", # whatever the source uses (RSS GUID, DAT load_id, etc.)
    "source_url": "<url-of-listing>",          # for re-fetch or operator-click-through

    # ---- temporal ----
    "posted_at": "2026-05-08T14:00:00Z",       # when the source says it was posted
    "scraped_at": "2026-05-08T14:05:31Z",      # when we ingested
    "normalized_at": "2026-05-08T14:05:31Z",   # when we ran our normalizer
    "last_seen_at": "2026-05-08T15:05:12Z",    # bumped each scrape tick the listing is still visible
    "first_seen_at": "2026-05-08T14:05:31Z",   # never bumped after creation

    # ---- origin / destination ----
    "origin_city": "Chicago",
    "origin_state": "IL",
    "origin_zip": "60601",                     # nullable
    "dest_city": "Atlanta",
    "dest_state": "GA",
    "dest_zip": "30301",                       # nullable

    # ---- equipment / dimensions ----
    "equipment_type": "DRY_VAN",               # normalized: DRY_VAN / REEFER / FLATBED / STEPDECK / RGN / POWER_ONLY / OTHER
    "weight_lbs": 42000,                       # nullable
    "length_ft": 53,                           # nullable

    # ---- rate ----
    "rate_quoted": 2200.00,                    # nullable; total $ if posted
    "rate_per_mile": 1.85,                     # nullable; computed if rate + miles available
    "miles_estimated": 1190,                   # nullable; computed from origin/dest geocode

    # ---- contact ----
    "contact": {                               # all sub-fields nullable
        "company_name": "Acme Brokerage",
        "phone": "+1-555-867-5309",
        "email": "dispatch@acme.example",
    },

    # ---- raw archive (gzipped) ----
    "raw_html_blob_gz_b64": "<base64 of gzip(html)>",  # nullable; OPTIONAL retention
    "raw_blob_size_bytes": 28341,                       # uncompressed size for storage tracking
    "raw_blob_gz_size_bytes": 7104,                     # compressed size

    # ---- quality / parser meta ----
    "parser_version": "0.1.0",
    "parser_warnings": [],                     # list[str]; eg ["missing_rate","ambiguous_equipment"]
    "is_active": True,                         # set False when listing disappears for >24h

    # ---- soft-delete / retention ----
    "expired_at": null,                        # set when listing has been gone > retention cutoff
}
```

### 4.2 Indexes (Mongo)

```python
db.loadboard_listings.create_index([("dedup_hash", 1)], unique=True)
db.loadboard_listings.create_index([("source", 1), ("posted_at", -1)])
db.loadboard_listings.create_index([("origin_state", 1), ("dest_state", 1), ("posted_at", -1)])
db.loadboard_listings.create_index([("equipment_type", 1), ("posted_at", -1)])
db.loadboard_listings.create_index([("last_seen_at", -1)])
db.loadboard_listings.create_index([("is_active", 1), ("last_seen_at", -1)])
```

### 4.3 Retention policy (defer to Phase 10.2)

- Hot data (<30 days): full record incl raw_blob.
- Warm data (30-365 days): record minus raw_blob (raw blob archived to D-drive or S3 cold).
- Cold data (>365 days): aggregated only into `lane_metrics_daily`; per-listing rows pruned.

**For 10.1 bootstrap:** keep everything, warn operator at 200GB C-drive, surface at 100GB, hard-stop at 10GB (per standing directive).

---

## 5. Ingestion flow

```
[scraper cron tick]
    |
    v
[is_scraper_enabled()] -- False --> [return {"status":"skipped_disabled"}]  (no network I/O)
    |
    True
    v
[for each registered source]
    |
    v
[_fetch(source_url, http_client_factory)]  # async httpx GET, timeout, retry-once
    |
    v
[_parse_html(html, source_name)]           # per-source parser dispatch -> list[dict]
    |
    v
[_normalize_listing(listing)]              # equipment-type canonicalization, state-code uppercasing,
    |                                        rate-per-mile computation, phone normalization
    v
[_dedup_hash(listing)]                     # sha256(source + source_listing_id + posted_at_iso)
    |
    v
[_upsert_listings(db, listings)]           # for each listing:
    |                                        find_one({"dedup_hash": h})
    |                                        if exists: update_one $set last_seen_at = now
    |                                        else:      insert_one with first_seen_at=last_seen_at=now
    v
[update health counters; emit journal]
    |
    v
[return {"status":"ok", "fetched": N, "upserted": M, "duplicates": D, ...}]
```

### 5.1 Dedup contract

`dedup_hash = sha256(source + "|" + source_listing_id + "|" + posted_at_iso)`

Properties:
- **STABLE** for the same listing seen twice → upsert idempotent.
- **CHANGES** if any of (source, listing_id, posted_at) change → treated as a new listing.
- **No PII / contact-info in the hash** → contact changes don't fragment the record.

### 5.2 Soft-disappear handling

A listing booked or pulled by the broker disappears from the feed. We DON'T delete the row — we let `last_seen_at` go stale. A daily aggregator (10.2) marks `is_active: False` for rows whose `last_seen_at < now - 24h`. This preserves historical visibility for the seasonality models.

---

## 6. Cron cadence

### 6.1 Initial schedule (Phase 10.1.1, post-source-selection)

- **Frequency:** every 60 minutes.
- **Per-source pacing:** if we have N sources, stagger them across the hour (one source every `60/N` minutes) to avoid coincident outbound bursts.
- **Backoff trigger:** if a single source returns 429 OR 5xx for 3 consecutive ticks, back off to every 6h for that source AND DM `@pm-lead` for operator awareness.
- **Hard kill switch:** `CHL_LOADBOARD_SCRAPER_ENABLED=false` flips ALL sources off in one env-var change. Re-enable returns to per-source backoff state.

### 6.2 NOT in this stage

This stage adds a TODO comment to `cron_scheduler.py` listing the proposed registration. The actual `asyncio.create_task(...)` line is added in Phase 10.1.1 ONLY after operator green-lights a specific source.

---

## 7. Feature flag

### 7.1 Definition

```python
# In loadboard_scraper.py:
ENV_VAR_NAME = "CHL_LOADBOARD_SCRAPER_ENABLED"

def is_scraper_enabled() -> bool:
    raw = (os.environ.get(ENV_VAR_NAME, "") or "").strip().lower()
    return raw in ("1", "true", "yes", "on")
```

### 7.2 Default: OFF

- `.env` has NO entry for `CHL_LOADBOARD_SCRAPER_ENABLED`.
- `is_scraper_enabled()` returns `False` by default.
- `scrape_once(...)` early-returns `{"status": "skipped_disabled", "reason": "feature_flag_off"}` and does ZERO network I/O.

### 7.3 Activation procedure (when operator approves)

1. Operator authorizes a specific source.
2. Run `python scripts/set_env_var.ps1 CHL_LOADBOARD_SCRAPER_ENABLED true` (per standing-directive helper).
3. Restart backend.
4. First cron tick will fetch from approved sources.
5. Verify via `/api/health/scrapers` (route added in Phase 10.1.1).

---

## 8. Failure modes + mitigations

| Failure mode | Symptom | Mitigation |
|---|---|---|
| Source HTML structure changes | Parser silently returns 0 listings or raises KeyError | (a) per-tick log of fetched-vs-parsed delta; (b) alert if parsed=0 and fetched>0 for 3 consecutive ticks; (c) parser_warnings field on the row; (d) raw_blob retained for re-parse with the fixed parser |
| Rate-limit / 429 | Returns 429 status code | Back off per-source to 6h cycle; DM `@pm-lead` |
| IP ban / persistent 403 | Returns 403 across all sources OR persistent block | Hard kill — flip feature flag off; surface to operator with full diagnostic |
| ToS challenge / cease-and-desist | Operator receives outside-channel notification | Hard kill switch (env-var off); pause scraping for that source; operator decides next |
| DB write contention | Mongo upsert latency spike | Bulk-upsert path: build list, single `bulk_write` per tick; if still slow, drop frequency to 4h |
| Disk filling from raw_blob retention | C-drive % free dropping faster than budget | Disable raw_blob retention via `CHL_LOADBOARD_KEEP_RAW=false`; archive existing blobs to D-drive |
| Parser exception kills the cron loop | Whole cron loop dies | Per-source try/except wrapper inside `_parse_html`; one source's failure does NOT crash the others |
| Network outage / DNS failure | All sources timeout | Retry-once with 30s delay; skip the tick; emit "notable" journal entry; resume next hour |
| Clock-skew between source-server and us | `posted_at` drift makes dedup_hash flap | Round `posted_at` to nearest minute when computing hash; preserve full precision in stored field |

---

## 9. Integration points (downstream)

### 9.1 `MarketEvaluator` (Phase 10.6)

Reads `db.lane_metrics_daily` (10.2 product) NOT raw `loadboard_listings`. So 10.1 doesn't directly couple — the integration is via the daily-rollup intermediary.

### 9.2 `lane_recommender` (Phase 10.5)

Reads `db.loadboard_listings` filtered by `posted_at >= now - 90d` and groups by `(origin_state, dest_state, equipment_type)`. The 10.1 schema deliberately makes those fields top-level and indexed.

### 9.3 `seasonality_decomposer` (Phase 10.3)

Reads `db.lane_metrics_daily` (12+ months). The decomposer doesn't touch `loadboard_listings` directly — it depends on the 10.2 daily-aggregate cron.

### 9.4 `rate_forecaster` (Phase 10.3)

Reads `db.lane_metrics_daily` (12+ months); falls back to `loadboard_listings` rolled up on-demand for lanes with sparse `lane_metrics_daily` history.

### 9.5 `carrier_home_time_model` (Phase 10.4)

Reads PRIMARILY internal `loads` + `carrier_interests`; uses `loadboard_listings` only as a feature for "what other loads were available near home when this carrier accepted/declined." Optional, lightweight join.

### 9.6 `/api/health/scrapers` route (Phase 10.1.1)

Exposes `loadboard_scraper.health()` output for the standing health-monitor surface. Operator-readable. Not in 10.1 scaffold.

---

## 10. Test plan

### 10.1 Tests in this stage (5 unit tests, all pass with feature flag OFF)

1. `test_scrape_disabled_returns_skip_no_network`
   - Mock `http_client_factory`; assert it is NEVER called when `CHL_LOADBOARD_SCRAPER_ENABLED` is unset/false.
   - Assert return value: `{"status": "skipped_disabled", "reason": "feature_flag_off"}`.

2. `test_dedup_hash_stable_for_same_listing`
   - Two dicts with same (source, source_listing_id, posted_at) → identical hash.
   - Even when other fields (rate, contact, raw_blob) differ.

3. `test_dedup_hash_differs_for_different_listings`
   - Different `source_listing_id` → different hash.
   - Different `source` → different hash.
   - Different `posted_at` → different hash.

4. `test_upsert_creates_new_listing`
   - Empty stub DB; call `_upsert_listings` with one record.
   - Assert one row exists; `first_seen_at == last_seen_at`; `dedup_hash` set.

5. `test_upsert_refreshes_last_seen_at_on_duplicate`
   - Pre-seed one row; call `_upsert_listings` with same dedup hash but later timestamp.
   - Assert: row count still 1; `last_seen_at` advanced; `first_seen_at` UNCHANGED.

### 10.2 Tests deferred to Phase 10.1.1 (per-source parser ship) -- IMPLEMENTED 2026-05-08 EOD-16 (Stream EE3)

- Fixture-based parser tests: SHIPPED. `backend/tests/test_rss_parser.py` (38 unit tests, parametrized across 8 mandatory cases) + `backend/tests/test_loadboard_scraper.py` extended with 2 integration tests. Fixtures at `backend/tests/fixtures/loadboard/` (sample_rss_clean.xml, sample_rss_malformed.xml, sample_atom.xml).
- Never hit the live source in tests. All tests use the synthetic XML fixtures and a stubbed `_fetch_html` for the integration path.

### 10.3 Tests deferred to Phase 10.2 (daily aggregate)

- Daily-rollup cron correctness: given N listings, daily aggregator produces correct lane-level metrics.
- Soft-disappear marking: listings stale >24h are flagged `is_active: False`.

### 10.4 Tests deferred to Phase 10.4-10.5 (model consumers)

- Lane recommender / seasonality decomposer integration tests against synthetic listing fixtures.

---

## 11. Dependencies

### 11.1 Already in `backend/requirements.txt`

- `httpx==0.28.1` — async HTTP client (verified present 2026-05-08 EOD-15).

### 11.2 Required ADDITIONAL libs (NOT installed in this stage)

| Lib | Purpose | When needed | Notes |
|---|---|---|---|
| `feedparser` | RSS/Atom parsing | Phase 10.1.1 (per-source parser ship) | Pure Python; no system deps. ~`pip install feedparser`. |
| `beautifulsoup4` + `lxml` | HTML parsing for non-RSS sources | Phase 10.1.1 (per-source parser ship) | If we add ANY HTML-scraping source. |
| `gzip` (stdlib) | Compress raw_blob | When raw retention enabled | NO install needed. |
| `hashlib` (stdlib) | dedup hash | This stage | NO install needed. |

The 10.1 scaffold imports only stdlib + httpx. Adding `feedparser` etc. happens at the per-source parser stage with a fresh requirements.txt update + commit.

---

## 12. Decision log

- **Why aggregated-RSS first vs DAT:** lower activation friction, ToS-clean, immediately operational. Operator can authorize DAT later without changing the 10.1 substrate.
- **Why 60min cadence vs 15min:** signal density at hour-scale is sufficient for the v1 models; lower cadence reduces ban risk; we can tighten later.
- **Why feature-flag-OFF default:** standing-directive consistency (no live external I/O without explicit operator green-light); reversible.
- **Why dedup_hash includes posted_at:** if a broker re-posts a load (new listing same trip), it should get a new row. Otherwise dedup would falsely merge re-posts.
- **Why `last_seen_at` instead of delete-on-disappear:** preserves historical record for seasonality models. We MUST be able to ask "was this lane active in May 2025?"
- **Why optional `raw_html_blob_gz_b64`:** ~30KB raw × 50K listings/day × 365 days = ~500GB/yr. Operator decides retention. Default off in 10.1 for safety; flip on per-source after first 30 days of validating the parser is stable.

---

## 13. Boot pointers (for the agent picking up Phase 10.1.1)

1. Read this doc end-to-end.
2. Read `phase_10_statistical_analytics_and_hardware_plan.md` for the broader architecture.
3. Confirm operator has authorized a specific source. Without that, do NOT add a per-source parser.
4. Add `feedparser` (or `beautifulsoup4` + `lxml`) to `backend/requirements.txt`. `pip install` in venv.
5. Implement `_parse_<source>_listings(html_or_xml)` in `backend/scrapers/loadboard_scraper.py`.
6. Add fixture-based parser test in `backend/tests/test_loadboard_<source>_parser.py`.
7. Add `/api/health/scrapers` route exposing `health()` output.
8. Register the cron loop in `backend/cron_scheduler.py` (replace the TODO comment).
9. Operator runs `set_env_var.ps1 CHL_LOADBOARD_SCRAPER_ENABLED true` and restarts backend.
10. Verify first tick via `/api/health/scrapers` and a sample row in `db.loadboard_listings`.

---

## 13a. Phase 10.1.1 ship state (2026-05-08 EOD-16, Stream EE3)

**SHIPPED (verifier-gate pending; code commit deferred to verifier):**
- `backend/scrapers/rss_parser.py` (~340 LOC) -- generic RSS 2.0 + Atom 1.0 parser, stdlib `xml.etree.ElementTree` only, no new external deps.
- `backend/scrapers/loadboard_scraper.py` -- `_parse_html(...)` wired to dispatch `"public_rss"` -> `rss_parser.parse_rss_feed(...)`; other sources raise `NotImplementedError` (surfaced as `status="error", reason="parser_not_shipped"`).
- `REGISTERED_SOURCES["public_rss"]` registered with placeholder URL (feature flag still OFF; never fetched).
- `backend/tests/fixtures/loadboard/{sample_rss_clean.xml, sample_rss_malformed.xml, sample_atom.xml, __init__.py}` -- synthetic; not scraped from any live source.
- `backend/tests/test_rss_parser.py` (~210 LOC, 38 tests via parametrize, all 8 mandatory test names covered).
- `backend/tests/test_loadboard_scraper.py` extended with 2 integration tests (5 baseline + 2 new = 7 total).

**Test results:** 45/45 pass (`python -m pytest backend/tests/test_rss_parser.py backend/tests/test_loadboard_scraper.py -v`).

**Still feature-flag-gated:** `CHL_LOADBOARD_SCRAPER_ENABLED` remains unset/false in `.env`. The disabled-path test still passes; no live network I/O is reachable from any code path until operator activates the flag via `scripts/set_env_var.ps1`.

**Cron registration:** still NOT registered (per Phase 10.1 design doc Section 6.2).

**Defensive behaviors validated by tests:**
- Malformed top-level XML -> empty list (no exception).
- Per-item bad data (missing guid, unparseable pubDate, future-dated pubDate, unparseable lane) -> item skipped, others returned.
- Lane-arrow tokens supported: `->`, `-->`, ` to `, ` > `.
- Equipment phrase canonicalization: REEFER, DRY_VAN, FLATBED, STEPDECK, RGN, POWER_ONLY (with bare "van" -> DRY_VAN as longer-phrase-wins fallback).
- Rate extraction: lane-total only; `$1.85/mi` per-mile tokens excluded; multiple distinct totals -> None (ambiguous).
- Future-dated pubDate (>5min skew) rejected as feed-bug.

**Boot pointer for Phase 10.1.2 agent:**
- Operator authorization required before adding a real `public_rss` URL or any source-specific parser.
- The single-URL `_fetch_html` placeholder in `loadboard_scraper.py` still raises NotImplementedError; an actual httpx fetch + retry-once + per-source headers ships with 10.1.2.
- The aggregated multi-feed strategy (10-20 broker feeds) needs a per-source URL list -- consider a JSON config file at `backend/scrapers/public_rss_sources.json`.

---

## 13b. Phase 10.1.2 ship state (2026-05-08, Stream FF3)

**SHIPPED (still feature-flag-gated end-to-end; code commit deferred to verifier):**
- `backend/scrapers/loadboard_scraper.py` -- `_fetch_html` is now a real httpx async GET:
  - 30-second total timeout (connect + read + write + pool); applied as the `timeout=` kwarg on every `client.get(...)` call.
  - Retry-once on transient failures (5xx subset {500,502,503,504} + connection errors + timeouts). `HTTP_RETRY_BACKOFF_SEC = 2.0s` between attempts; `HTTP_RETRY_MAX_ATTEMPTS = 2` (initial + 1 retry).
  - Per-source User-Agent + Accept headers merged onto module-level `DEFAULT_FETCH_HEADERS` so we always identify ourselves with `CHL-loadboard-scraper/0.1 (contact: dispatch@continentalhaul.com)`.
  - Logs URL + HTTP status + bytes received + duration_ms per request. Body never logged (could be MB of XML).
  - Returns response body as `str` (UTF-8 decoded; httpx handles charset detection).
  - On final failure (post-retry) raises typed `LoadboardFetchError(message, *, url, status_code, attempts)` chained from the original cause via `raise ... from`.
- `backend/scrapers/loadboard_scraper.py` -- new public function `scrape_all_enabled_sources(*, db, http_client_factory=None, sources_path=None, parser_kind="public_rss")`:
  - Reads `public_rss_sources.json` (or override path for tests), iterates enabled sources, calls a per-source mini-pipeline (`_scrape_aggregated_source`) that fetches + RSS-parses + normalizes + upserts.
  - Defense-in-depth gates: master `is_scraper_enabled()`, `integrity.operational_state.is_read_only()`, `integrity.operational_state.should_skip_tick()`. All three short-circuit BEFORE any HTTP work.
  - Whole-batch resilient: a single source's `LoadboardFetchError` (or any uncaught exception) is logged + recorded in the per-source result dict but never aborts the batch.
  - Skips `enabled=false` sources without any HTTP call (zero cost) -- verified by test contract (`http_client_factory` invocation count == 0).
  - Returns `{status, sources_attempted, sources_succeeded, sources_failed, sources_skipped_disabled, total_listings, by_source: {<name>: {status, fetched, upserted, duplicates, reason?}}}`.
- `backend/scrapers/public_rss_sources.json` -- 8 placeholder sources, all `enabled=false`, all using `loadboard.example` / `*-broker-*.invalid` non-resolvable hostnames so no live network call can occur even if the master flag is flipped on without operator URL-vetting first. Naming convention: `broker_<greek>_rss` (or `_atom` for Atom feeds), snake_case, globally unique. Each source carries `name`, `url`, `headers`, `interval_sec`, `enabled`, `notes`.
- `backend/cron_scheduler.py` -- TODO block updated to "TODO PHASE 10.1.3" with the actual `register_cron`-style registration code COMMENTED OUT (not active). Activation gate documented: (1) operator vets ToS, (2) operator flips per-source enabled, (3) operator activates master flag via `scripts/set_env_var.ps1`.
- `backend/tests/test_loadboard_scraper.py` -- 8 new tests added (45 -> 53 total combined with rss_parser tests):
  - `test_fetch_html_returns_body_on_200_response` -- 200 OK returns body, headers threaded through.
  - `test_fetch_html_retries_once_on_5xx` -- 503 then 200 -> success after 2 attempts.
  - `test_fetch_html_raises_loadboard_fetch_error_after_retries` -- both attempts 503 -> typed error.
  - `test_fetch_html_honors_30s_timeout` -- timeout applied to every `client.get`; TimeoutError on first attempt triggers retry.
  - `test_scrape_all_enabled_sources_skips_disabled` -- two disabled sources -> zero factory calls, two `skipped_disabled` results.
  - `test_scrape_all_enabled_sources_skips_when_master_flag_off` -- enabled source but master flag off -> short-circuit, zero factory calls.
  - `test_scrape_all_enabled_sources_aggregates_per_source_counts` -- two sources each return clean RSS fixture -> 5+5 listings, 10 distinct DB rows (one per source via dedup hash).
  - `test_scrape_all_enabled_sources_continues_after_one_source_fails` -- source A 503/503, source B 200 -> A failed, B succeeded, 5 rows in DB.

**Test results:** 53/53 pass (`python -m pytest backend/tests/test_loadboard_scraper.py backend/tests/test_rss_parser.py -v`).

**Preflight:** `scripts/preflight_server_smoke.py` -> `preflight OK: 169 routers + 18 crons + 5 health-registers verified`. No regressions.

**robots.txt decision:** intentionally NOT consulted by `_fetch_html`. Rationale: RSS / Atom feeds are explicitly publishable resources -- a broker that exposes an RSS endpoint has already opted in to programmatic consumption. Compliance is instead enforced by (a) per-source operator ToS vetting in `public_rss_sources.json` BEFORE flipping `enabled=true`, and (b) an honest User-Agent identifying CHL with a contact email so brokers can reach out for opt-out before issuing a ban. If a future source requires robots.txt honoring (eg HTML scraping), add the check at that source's parser, not at the fetcher layer.

**Still feature-flag-gated:** `CHL_LOADBOARD_SCRAPER_ENABLED` remains unset/false. Even if accidentally set, `public_rss_sources.json` ships with every `enabled=false` so the aggregator makes zero HTTP calls. Triple gate: master env flag + per-source enabled + operator-vetted URLs.

**Cron registration:** still NOT registered. The block is documented + commented out in `cron_scheduler.py`; activating it requires uncommenting + restarting the backend.

**Boot pointer for Phase 10.1.3 agent:**
1. **Per-source rate-limit memory.** `interval_sec` is read from sources config but the aggregator does NOT yet enforce per-source minimum-poll-cadence across ticks. Add a `_last_polled_at[source_name]` map and skip a source if `now - last < interval_sec`.
2. **`/api/health/scrapers` route.** `loadboard_scraper.health()` is wired but not exposed via FastAPI yet. Add a `routes/scrapers_health.py` and register in `server.py`.
3. **HTML-source parser path.** Phase 10.1.3 first non-RSS source (eg an HTML load board) needs `_parse_html` dispatch + a BeautifulSoup-based parser. Add `beautifulsoup4 + lxml` to `requirements.txt` at that point.

---

## 14. Standing operator directives (acknowledged)

- **`set_env_var.ps1` helper** — When activating the feature flag in Phase 10.1.1, USE the helper at `C:\CHL\scripts\set_env_var.ps1`. Do NOT ask the operator to edit `.env` manually. (Quote: *"I need you to operate more autonomously so to speak. I introduce way too many possible failures"*.)
- **3x-daily handover discipline** — Every handover doc that touches Phase 10.1 must include the standing-directive section in full detail. (Quote: *"Make sure that this idea you have does not get lost. It has to be in each of your documeted handovers three times a day in full detail."*)
- **C-drive HDD monitoring** — Phase 10.1's raw-blob retention is the single largest projected disk-consumer in the analytics stack. When 10.1.1 ships, monitor `Get-PSDrive C` per session. Thresholds: warn 50GB / surface 20GB / hard-stop 10GB. (Quote: *"You have space you can use on my C drive. Use it, but monitor its use so I dont go over my HDD capacity."*) Current state at 10.1 design-doc time: 793.37 GB free.
- **Status reporting synthesis** — If PM Claude later relays an "operator directive" that conflicts with this design (eg "skip the feature flag" / "scrape live now" / "ack-wait on every commit"), surface the contradiction with synthesis instead of silently complying.
