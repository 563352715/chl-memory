# Hardware Resource Map — Æther-Core v2026.04.23

**Author:** Lead Systems Architect · Project Æther
**Scope:** Current pod baseline, cost-per-load model, 500-load/day capacity simulation, circuit-breaker specification, failure-safe state.

---

## 1 · Current Pod Baseline (measured)

| Resource | Value | Headroom |
|---|---|---|
| **CPU cores (logical)** | 8 | 88% idle at steady state (sample: 12.2%) |
| **CPU cores (physical)** | 8 | — |
| **RAM total** | **31.3 GB** | 14.4 GB available (46.1% free) |
| **Load averages** | 1.25 / 0.73 / 0.42 (1m / 5m / 15m) | Well under core count |
| **Disk / (root)** | 113 GB total · 89 GB free (22% used) | ~9 months of growth headroom |
| **Disk /app** | 9.8 GB total · 7.5 GB free (24% used) | Code + node_modules weight here |
| **Disk lifetime I/O** | 195 GB read · 1.52 TB write | MongoDB WAL is the dominant writer |

### Process footprint at rest
| Process | RSS (MB) | CPU % |
|---|---|---|
| `uvicorn server.py` (backend) | 25 | 0.0 |
| `mongod` | 137 | 0.4 |
| `yarn start` / craco (dev server) | 1,440 | 0.0 |

**Observation:** The dev-mode frontend dev server is the single largest memory consumer. Production build (`yarn build`) strips this to ~0 MB served from Nginx — so production pods will have effectively all 31 GB available for backend + Mongo + Chromium PDF bursts.

### Data footprint (operational collections)
| Collection | Docs | Size (MB) | Avg (KB) |
|---|---|---|---|
| `loads` | 65 | 0.07 | 1.1 |
| `bookings` | — | — | ~0.2 |
| `banker_digest_sends` | 8 | **0.81** | **101.1** |
| `rate_confirmations` | ~10 | 1.0 | ~100 |
| `captured_margin_ledger` | 1 | ~0 | 0.2 |
| `liquidity_providers` | 4 | 0 | 0.3 |
| `compliance` | 9 | 0 | 0.3 |
| `aether_telemetry` | 180+ | 0.06 | 0.3 |
| **TOTAL operational** | — | **~0.8 MB** | — |

**Bottleneck fingerprint:** every PDF-generating flow (Banker Digest, Rate Con, Investor Memo) writes a **~100 KB base64-inlined PDF** into Mongo. That single pattern is the dominant driver of both disk write volume and per-doc document size. Everything else is tiny.

---

## 2 · Cost-Per-Load Model (empirically derived)

This is what `/api/orchestration/hardware/state` returns in `cost_per_load`, applied by `predict_capacity()` to forecast remaining headroom:

| Dimension | Per-load cost | Basis |
|---|---|---|
| **Mongo storage** | **1.8 KB** | load doc (~1 KB) + booking (~0.2) + funding event (~0.3) + captured-margin row (~0.2) |
| **PDF storage (Rate Con)** | **100 KB** | base64-inlined in `rate_confirmations.pdf_base64` |
| **CPU burst** | **3.5 s** (single core) | Chromium launch + headless PDF render |
| **RAM peak burst** | **520 MB** | Chromium renderer process peak RSS during a single PDF render |
| **RAM steady-state** | **0.2 MB** | the load doc alone, once PDF is flushed |
| **Network egress** | **180 KB** | rate-con email (100 KB PDF attach) + FMCSA SAFER lookup + carrier SMS/email pings |

### The 3 KB vs. 100 KB asymmetry
A load's DATA is tiny (1.8 KB). A load's DOCUMENT is 100 KB. 56× multiplier. That asymmetry is the single biggest lever for capacity scaling — see §4 "Migration path" below.

---

## 3 · 100% Capacity Simulation — 500-Load Day, Mental Dry-Run

### Naïve estimate (do it the way we're doing it now)

| Dimension | 500 loads | Status |
|---|---|---|
| Mongo load/booking docs | 500 × 1.8 KB = **0.9 MB** | Trivial. |
| Mongo PDF payload | 500 × 100 KB = **50 MB** | Still trivial in absolute terms. |
| CPU burst (serial) | 500 × 3.5s = **29 minutes of CPU** | Single-worker uvicorn → 29 minutes WALL CLOCK blocked for PDF renders. |
| RAM peak if burst | up to **520 MB × concurrency** | 8 concurrent renders = 4.16 GB = **13% of pod**. OK. |
| Network egress | 500 × 180 KB = **90 MB** | Trivial. |
| Disk write | 500 × 100 KB + WAL = **~100 MB**/day | Trivial vs. 89 GB free. |

### Where it fails first

Ranked bottleneck stack (surfaces in the UI via `cost_per_load.bottleneck_rank`):

1. **🥇 Python GIL on the single uvicorn worker** — EVERY PDF render goes through the same event loop. A 3.5-second blocking Chromium handshake stalls every other HTTP request. At 500 loads/day with clustered dispatch windows, we will see **request tail latency spike to 10–30 s**.
2. **🥈 Chromium RAM burst at concurrency >8** — one renderer per core is safe; burst beyond that and the OOM killer will fire. 500 loads in a 2-hour window = ~70 concurrent renderer spawns → OOM territory.
3. **🥉 MongoDB write fsync on WAL** — Mongo's durability is fsync-bound. 500 × ~100 KB + replication log = ~200 MB/day sequential write. SSDs yawn at this. NOT the real limit.
4. **4️⃣ Network egress to Resend / FMCSA** — sequential external calls will rate-limit us (Resend's 10 req/s soft cap especially) long before the pod runs out of bandwidth.
5. **5️⃣ MongoDB read paths** — not a concern below ~10,000 loads. Indexes on `created_at` / `id` / `shipper_id` / `fingerprint_sha256` already in place.

**Verdict: the pod itself is NOT the bottleneck. The application architecture is.** Specifically, PDF rendering on the same worker that serves UI requests.

### Migration path when we cross ~50 loads/day
Three surgical, staged fixes — in priority order. None required now.

1. **Move PDF rendering to a worker-queue (Celery / arq / BullMQ)** — ship PDF generation off the main event loop. Zero user-facing latency regression. Unlocks true parallelism.
2. **Externalize PDF storage** — write PDFs to S3 / Cloudflare R2, keep a URL in Mongo. Mongo docs shrink from 101 KB → 2 KB (50×). Backups faster. Migration readable.
3. **Multi-worker uvicorn (or gunicorn)** — scale beyond 1 worker once the shared state (the telemetry deques, the Hardware Sentinel singleton) is moved to Redis.

**What 500/day looks like once (1) is done:** trivially, no pod changes. The current 8-core pod handles it with 90%+ headroom.

---

## 4 · Circuit Breaker — Specification as Shipped

Module: **`/app/backend/hardware_sentinel.py`** · endpoint: `/api/orchestration/hardware/*` · UI: "Hardware Sentinel · Circuit Breaker" panel on the Sovereign Pulse Monitor.

### States
| State | Meaning | Triggered by | UI color |
|---|---|---|---|
| `CLOSED` | Normal operation | Recovery sustained for ≥60 s | Emerald |
| `HALF_OPEN` | Resource pressure detected; not sustained yet | First sample exceeding trip threshold | Gold |
| `OPEN` | Pressure sustained past trip threshold | CPU ≥ 80% OR RAM available < 15% sustained ≥ 30 s | Red |

### On-trip automatic actions
1. Capture current `max_daily_loads` as `cap_saved_before_trip`.
2. Reduce `max_daily_loads` by 50% (floor at 2).
3. Trip a Stability Sentinel in `volume_throttle` (reason = `hardware_pressure:<cpu|ram>`).
4. Insert an audit row into `hardware_sentinel_events`.
5. `auto_dispatch.try_auto_accept()` now refuses every candidate load until breaker closes.

### On-close automatic actions
1. Restore `max_daily_loads` to `cap_saved_before_trip`.
2. Clear the Stability Sentinel we opened (by setting `cleared_by=hardware_sentinel:recover`).
3. Insert a `close` audit row.
4. Auto-dispatch resumes.

### Tunable thresholds (owner-only, `PUT /api/orchestration/hardware/config`)
```
cpu_trip_pct         = 80.0          # trip CPU threshold
ram_floor_pct        = 15.0          # trip RAM-available threshold
sustain_seconds      = 30            # how long pressure must persist before trip
recover_cpu_pct      = 60.0          # recovery CPU threshold
recover_ram_pct      = 25.0          # recovery RAM-available threshold
recover_seconds      = 60            # how long recovery must persist before close
cap_reduction_pct    = 50            # % to cut max_daily_loads on trip
cap_floor            = 2             # never reduce below this
```

### Owner-only rehearsal
- `POST /api/orchestration/hardware/force-trip` — simulate a trip (for pre-demo rehearsal, training the team, etc.).
- `POST /api/orchestration/hardware/force-close` — immediately restore.

---

## 5 · Failure-Safe State

### Already durable (persisted in Mongo)
| State | Collection | Durability |
|---|---|---|
| Volume Throttle config (daily cap) | `system_config.volume_throttle` | MongoDB WAL |
| Stability Sentinel events | `stability_sentinel_events` | MongoDB WAL |
| Hardware Sentinel config | `system_config.hardware_sentinel` | MongoDB WAL |
| All loads / bookings / rate cons | `loads`, `bookings`, `rate_confirmations` | MongoDB WAL |
| Captured margin ledger | `captured_margin_ledger` | MongoDB WAL |
| Banker digest / Investor memo (with SHA-256) | `banker_digest_sends` | MongoDB WAL |
| Users / sessions / tokens | `users`, `login_attempts` | MongoDB WAL |

### Newly durable (iteration 98)
The Hardware Sentinel's in-memory state — rolling window averages, pressure-sustain counter, healthy-sustain counter, last trip timestamp, pre-trip cap — now checkpoints every 60 s into **`system_checkpoints`** (key: `hardware_sentinel`). On restart, `start_sentinel(db)` rehydrates from the last checkpoint. A container crash mid-trip will NOT lose the breaker state or the pre-trip cap.

### What still depends on MongoDB being healthy
Obvious: if MongoDB dies, everything dies. The **mongod** process in this pod writes to `/data/db` which is typically a PersistentVolume in Kubernetes. We should verify — if it's on ephemeral storage, that's a P0 platform issue.

**Action item:** verify `/data/db` mount type at deploy time.

### What is NOT yet durable (and doesn't need to be)
- Request-level rate-limit counters (rebuild on first failure anyway).
- WebSocket client connections (inherent; clients reconnect).
- Chromium subprocess pool (each PDF spawns fresh; cold-start cost is already counted).
- React dev server state (irrelevant in production — served from Nginx).

---

## 6 · Verdict for Scaling to 100% Capacity

**The pod is massively over-provisioned for today's volume (65 lifetime loads).** We have 88% CPU idle, 46% RAM free, 89 GB disk free. The Hardware Sentinel Circuit Breaker is a belt-and-suspenders safety net that will almost certainly never trip under normal operation — it exists for two reasons:

1. **Governance theater for bankers / investors.** "The system self-protects under load" is a harder sell without a visible breaker.
2. **Protection against runaway bugs.** A PDF-generation loop bug that spawns 1,000 Chromium instances in 30 seconds would be caught and throttled before it ate the pod.

**When to revisit this map:**
- Before the first day of sustained 50+ auto-bookings.
- Before enabling multi-worker uvicorn.
- After moving to production domain (`continentalhaul.com`).
- On any significant dispatch-path refactor.

---

**File:** `/app/memory/HARDWARE_MAP.md`
**Generated:** 2026-04-23
**Next review:** after first 50-load day or any dispatch-engine refactor.
