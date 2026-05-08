# Phase 10 — Statistical Analytics Layer + Hardware Capacity Planning

**Status:** Captured 2026-05-08 EOD-9. Operator-mandated. Major scope expansion. Do NOT execute now — this is roadmap-tier scope (~Phase 10, after the current iters wind down toward functional-MVP).

**Operator framing 2026-05-08 EOD-9 (verbatim):**

> "We will need to be running statistival models for interpretation and use to support certain various feature of this platform that support our ability we extent to carriers that decide they want to be home at certain points at the end of their week. Statistics that search load boards at not just the loads we booked, but also patterns across all load boards that could support creating lanes and support efficiencies and increase levels of control to manage the enire workload of the country. After pondering this, imagine what kind of hardware we would need to support this and put it in the todo list not to be forgotten. I imagine we wuld need increased hdd space if we are storing historical data for statistical use. We will need to be abe to identify seasonality changes which probably means we need more than just two weeks rolling average numbers. Plus we have to be able to process this information. Do I need mare than one processor crunching this work?"

---

## What this enables

A statistical analytics substrate that lets the platform:

1. **Carrier home-time preference modeling** — per-carrier, learn the days/regions/lanes a driver prefers to end their week (home base, family events, etc.) and bias the assignment engine to route them home on a Friday-or-Saturday cadence. Lifts retention + reduces driver attrition.

2. **Cross-load-board pattern analysis** — continuous scrape + persist EVERY visible load on DAT / Truckstop / 123Loadboard / etc. (not just the ones we booked). Surfaces:
   - **Lane discovery** — origin-destination pairs that consistently have demand → formalize as CHL "preferred lanes" + pre-position carriers
   - **Rate efficiency** — identify deadhead-minimizing route pairs (load home → another load originating where the prior delivered)
   - **National workload control** — dashboard shows where freight is moving across the country in near-real-time; surfaces opportunities to consolidate carriers regionally

3. **Seasonality detection** — needs MORE than 2-week rolling averages. Year-over-year patterns: produce season (May-Aug Salinas/Yuma → Northeast), reefer demand around major holidays, reefer-to-dry-van rate shifts, regional weather impacts (winter Midwest closures), construction season (Apr-Oct), retail surges (Black Friday → Christmas backhaul). Requires 12+ months of historical data minimum; ideally 24+ for cross-year comparison.

4. **Predictive features** (downstream of #1-3):
   - Carrier-of-week recommendation (who's most likely to take this load given their home-time prefs)
   - Lane-of-month auto-suggest (high-margin lane based on rolling 12-month rate trends)
   - Demand surge forecasting (X days ahead of an expected rate spike)

---

## Module architecture (Phase 10)

```
backend/
  analytics/                          (NEW directory)
    __init__.py
    load_board_scraper.py             # continuous scrape -> db.load_board_snapshots
    load_board_scraper_cron.py        # every 5-15 min per source
    snapshot_store.py                 # time-series persistence + indexing
    seasonality_decomposer.py         # STL / Prophet / ARIMA over snapshots
    carrier_home_time_model.py        # logistic regression on accept/decline w/ home-prox feature
    lane_recommender.py               # clustering on origin-dest pairs
    rate_forecaster.py                # ARIMA / Prophet for $/mile per lane
    insights_router.py                # /api/analytics/* endpoints
    insights_cron.py                  # daily compute + cache to db.analytics_insights
  evaluators/
    market_evaluator.py               # FractalEvaluator at MACRO scale (already sketched in Phase 8)
                                       # consumes seasonality + rate_forecaster outputs
                                       # feeds back into LoadEvaluator + LaneEvaluator weights
```

**New collections:**
- `db.load_board_snapshots` — every visible load from every source, every scrape tick
- `db.lane_metrics_daily` — pre-aggregated daily rollups per lane (count, avg $/mile, avg miles, avg deadhead)
- `db.analytics_insights` — pre-computed daily insights surface for the operator dashboard
- `db.carrier_home_time_features` — per-carrier model features (recency-weighted weekly cadence)

**Operator surfaces:**
- New tab: "Market Intelligence" or "Analytics" — surfaces seasonality charts, lane recommendations, rate forecasts, carrier home-time profiles.

---

## Hardware capacity planning

**Operator's question:** "I imagine we wuld need increased hdd space if we are storing historical data for statistical use ... Do I need mare than one processor crunching this work?"

### Storage estimates

| Data | Ballpark volume | Storage |
|------|----------------|---------|
| Load-board snapshots (50K visible loads/day × 1KB/row × 365 days) | ~18M rows/year | ~18-25 GB/year compressed |
| Raw scrape archives (HTML/JSON cache for re-parse if schema changes) | ~50K loads × 30KB raw × 365 days | ~500 GB/year (OPTIONAL — operator decides) |
| Pre-aggregated lane_metrics_daily (~10K lanes × 365 days × 1KB/row) | ~3.6M rows/year | ~4 GB/year |
| analytics_insights (cached snapshots of rolling outputs) | bounded (~100MB total) | flat |
| Carrier home_time features (per-MC, ~5K active carriers × 1KB) | bounded (~5-10MB total) | flat |
| **Total compressed (no raw archives)** | | **~25-30 GB/year** |
| **Total with raw archives (recommended for first 12 months)** | | **~500-600 GB/year** |

**Current state:** C-drive 793.6 GB free as of 2026-05-08 EOD-9 (per `Get-PSDrive C` in tonight's handover). Plenty of runway for 12-24 months of compressed analytics; constrained on raw archives if we keep them locally.

**Recommendation:**
- **Year 1:** keep everything on C-drive; archive raw scrapes to D-drive snapshots OR an external NAS / S3 cold storage (~$5-15/month at S3 Glacier tier).
- **Year 2+:** depending on retention strategy, either expand C-drive (cheap; 2TB SSD ~$120-200) OR migrate hot data to a dedicated DB host (Postgres/TimescaleDB on a VPS).
- **Trigger to surface:** when C-drive free drops under 200 GB, surface to operator + recommend external storage migration. Hard-stop threshold of 10 GB free still applies per standing directive.

### CPU / processor requirements

Operator's question: "Do I need mare than one processor crunching this work?"

**Short answer:** NO, not at current scale. ONE modern multi-core CPU (8+ cores) is sufficient. Here's the breakdown:

| Workload | CPU profile | Notes |
|----------|-------------|-------|
| Continuous load-board scraping | I/O-bound (network) | Single thread per source; 3-5 sources async on one process is plenty. Minimal CPU. |
| Snapshot persistence (Mongo writes) | I/O-bound (disk) | Trivial CPU |
| Daily seasonality decompose (STL / Prophet on 12 months × 10K lanes) | CPU-bound batch | ~5-30 min on 8 cores; runs once per day in cron. |
| ARIMA rate forecaster | CPU-bound batch | Per-lane forecast; ~1-2 sec per lane × 10K lanes = ~3-5 hours single-threaded; parallelizable to ~30 min on 8 cores. |
| Real-time inference (carrier home-time score) | CPU-bound, very light | Pre-cached per-carrier features; lookup is microseconds. No throughput concern. |
| Lane clustering (k-means or DBSCAN over snapshots) | CPU-bound batch | ~5-15 min on 8 cores; runs weekly. |

**Bottom line:**
- **Current dev hardware** (operator's Windows machine — typically 8-16 core modern desktop): adequate for v1 + first ~6 months of operation.
- **Upgrade trigger:** if daily batch jobs start exceeding 1-2 hours (saturating the CPU), THEN consider migrating analytics to a dedicated worker host. NOT before.
- **NOT needed:** GPU. None of the v1 models (Prophet, ARIMA, sklearn linear models, k-means) benefit from GPU. Only deep-learning models would, and we don't need those at v1.

**RAM:**
- Current dev hardware (assumed 16-32 GB): fine for v1.
- Upgrade trigger: if seasonality decomposition over 24+ months × 10K+ lanes consistently runs out of memory. Likely a 32 GB minimum recommendation when we get there.

**Network:**
- Continuous scraping pulls maybe 50-200 MB/day per source. Trivial bandwidth.
- Concern: rate-limits / IP-bans from load boards. Address via per-source pacing + rotating proxies (~$10-30/month from datacenter proxy service if/when needed).

### Hardware-upgrade decision tree

```
Do daily analytics batch jobs run in <2h?
├─ YES -> Stay on current hardware. Re-evaluate quarterly.
└─ NO  -> Profile: is bottleneck CPU, RAM, or disk I/O?
   ├─ CPU -> Add cores OR migrate to dedicated worker host
   ├─ RAM -> Upgrade RAM to 32 GB / 64 GB
   └─ Disk -> Migrate hot data to NVMe SSD; archive cold to HDD or cloud
```

**Capacity-planning check item:** add to the standing health-monitor: if any of (a) C-drive < 200 GB free, (b) daily analytics cron > 2h runtime, (c) RAM > 80% utilized during analytics batch — surface to operator with this decision tree.

---

## Build sequencing (Phase 10)

Phase 10 is downstream of:
- Phase 1-7 (foundation + carrier dispatch + factoring + email + self-healing) — DONE / NEAR-DONE through EOD-9
- Phase 8 sketches (MarketEvaluator + Redis caching) — partially designed, not built
- Functional MVP first revenue (~Tier 1 backlog finished)
- Repeatable operation milestone (10 loads/wk steady-state)

**Why wait:** statistical analytics is most valuable AFTER we have meaningful data flowing. With <100 booked loads, seasonality detection is noise; with 10K+ scraped loads/day across all boards, it becomes signal. Bootstrapping the scraper EARLY (even before analytics consume the data) is wise — it accumulates the historical record we'll need.

**Recommended Phase 10 sequencing:**

1. **Phase 10.1 — Bootstrap the load-board scraper EARLY** (can run in parallel with current iter work; no analytics consumers yet, just collecting). Build `load_board_scraper.py` + cron + `db.load_board_snapshots` collection. Start collecting now even before analytics consume.
2. **Phase 10.2 — Daily aggregation cron** (`lane_metrics_daily` rollup). Cheap to build; sets the foundation for everything downstream.
3. **Phase 10.3 — Seasonality decomposer + rate forecaster** (when 6+ months of snapshot data exist).
4. **Phase 10.4 — Carrier home-time model** (when 100+ booked loads with carrier-accept/decline history exist).
5. **Phase 10.5 — Lane recommender + Market Intelligence frontend tab** (depends on 10.3-10.4).
6. **Phase 10.6 — MarketEvaluator integration into FractalEvaluator weight loop** (closes the loop with iter 145.1 stage 3 weight-tuning proposer; analytics-driven weight adjustments).

---

## Hardware-capacity TODO list (do not forget)

- [ ] **Now:** monitor C-drive at every session close. Currently 793.6 GB free; warn at 200 GB; surface at 100 GB; hard-stop at 10 GB (per standing directive).
- [ ] **Phase 10.1 trigger:** when load-board scraper goes live, add a daily-snapshot-volume metric to BUILD_STATUS_REPORT. Track GB/day actual vs predicted.
- [ ] **Phase 10.3 trigger:** before seasonality decomposer goes live, run a one-off resource-profile on dev hardware: time the first decompose run + measure peak RAM. Capture as baseline.
- [ ] **Quarterly:** re-evaluate the hardware-upgrade decision tree. Surface findings in handover.
- [ ] **Year 1 milestone:** decide on raw-archive retention strategy (local vs S3 Glacier vs delete). Ship operator-facing decision request when first 12 months of scrape complete.
- [ ] **Standing alert:** if any of (a) C-drive <200GB free, (b) daily analytics cron >2h runtime, (c) RAM >80% during analytics batch — operator surface required.

---

## Why this doc exists

Operator-explicit 2026-05-08 EOD-9 — this scope is downstream of the current build but big enough that without persistent capture, "we should run statistical models" risks being lost across sessions. This doc + the BUILD_STATUS_REPORT entry + the capture-every-feature memory rule together ensure the analytics + hardware vision is durable.

**For the agent picking this up at Phase 10:** read this doc fully before designing the analytics layer. The operator's framing emphasizes "control to manage the entire workload of the country" — this is bigger than backend module work; it's a strategic surface that becomes a first-class differentiator vs other small brokers. Treat the build sequencing seriously: early scraper bootstrap = cheap; late seasonality build = waste without data.
