# Detention Service Design

Iter 145.x -- Streams FF2 (initial ship) + GG2 (closure).

## Purpose

Auto-charge detention on delivered loads. Eliminates manual review of
arrival/departure timestamps to identify billable wait time past free-time
thresholds. A row in `db.detention_charges` is a billable line item that
downstream invoice generation picks up; the service does NOT move money.

## Architecture

```
backend/detention/
    detention_calculator.py      pure-math + service functions
    detention_router.py          read-only health + state surface (FF2)
backend/cron_scheduler.py        registers the scan tick (FF2)
```

Module entry points:

- **Pure math**
  - `calculate_detention_hours(arrival_at, departure_at, free_time_hours)`
    -- billable hours past free time, rounded DOWN to 15-min increments.
  - `calculate_detention_charge(hours, hourly_rate, max_hours)` -- charge
    dict with per-stop max-hours cap.
- **Stateful**
  - `evaluate_load_detention(db, load_id)` -- read load (+ optional
    shipper), compute per-stop detention, write idempotent rows to
    `db.detention_charges`.
  - `scan_loads_for_detention(db)` -- batch entry: candidate-loads query,
    skip-if-already-billed, evaluate each.

## Industry-convention defaults

| Constant                       | Value | Source                                  |
|--------------------------------|-------|-----------------------------------------|
| `DEFAULT_FREE_TIME_HOURS`      | 2.0   | Industry standard for non-contracted    |
| `DEFAULT_HOURLY_RATE_USD`      | 50.0  | Median small-broker schedule            |
| `DEFAULT_MAX_HOURS_PER_STOP`   | 8.0   | Per-stop cap before re-quote required   |
| `ROUND_DOWN_INCREMENT_HOURS`   | 0.25  | 15-min increments, always floor         |

## Per-shipper override + multi-stop (GG2 closure)

Two follow-ups from FF2 close together in this iter:

### Resolution priority

`evaluate_load_detention` looks up `free_time_hours`, `hourly_rate`, and
`max_hours_per_stop` in the following priority order. The shipper doc is
fetched once per call (via `db.shippers.find_one({"shipper_id":
load["shipper_id"]})`) and cached locally for the call -- never
re-queried per stop.

| Field             | 1. Per-load override                                | 2. Per-shipper default                       | 3. Module default                |
|-------------------|------------------------------------------------------|----------------------------------------------|----------------------------------|
| `free_time_hours` | `load["detention_free_time_hours_override"]` (fallback alias: `load["free_time_hours"]`) | `shipper["default_free_time_hours"]`        | `DEFAULT_FREE_TIME_HOURS = 2.0`  |
| `hourly_rate`     | `load["detention_hourly_rate_override"]` (fallback alias: `load["detention_rate_usd_per_hour"]`) | `shipper["default_detention_rate"]`         | `DEFAULT_HOURLY_RATE_USD = 50.0` |
| `max_hours_per_stop` | `load["detention_max_hours_per_stop"]`            | `shipper["default_detention_max_hours_per_stop"]` | `DEFAULT_MAX_HOURS_PER_STOP = 8.0` |

The pre-FF2 alias keys (`free_time_hours`, `detention_rate_usd_per_hour`)
are honored for backward compat with existing fixtures. New code should
write the explicit `*_override` keys.

### Multi-stop schema

When `load.stops` is a non-empty list, the calculator iterates per-stop:

```jsonc
{
    "id": "load-uuid",
    "status": "delivered",
    "shipper_id": "shipper-acme-001",
    "stops": [
        {
            "arrival_at":   "2026-05-08T10:00:00Z",
            "departure_at": "2026-05-08T15:00:00Z",
            "stop_type":    "pickup"      // optional: pickup | delivery | midstop
        },
        {
            "arrival_at":   "2026-05-08T17:00:00Z",
            "departure_at": "2026-05-08T20:00:00Z",
            "stop_type":    "midstop"
        },
        {
            "arrival_at":   "2026-05-08T22:00:00Z",
            "departure_at": "2026-05-09T02:00:00Z",
            "stop_type":    "delivery"
        }
    ]
}
```

Heuristic when `stop_type` is missing on an array-form stop:
- index 0       -> `pickup`
- last index    -> `delivery`
- any middle    -> `midstop`

A caller-supplied `stop_type` value (one of pickup/delivery/midstop)
always overrides the heuristic.

### Idempotency / composite-_id convention

| Path                       | `_id` form                          | Note                                                        |
|----------------------------|-------------------------------------|-------------------------------------------------------------|
| Multi-stop (`load.stops`)  | `f"{load_id}:stop:{stop_idx}"`      | New convention -- explicitly namespaced for clarity         |
| Legacy synthesis           | `f"{load_id}:{stop_idx}"`           | Preserved verbatim from FF2 -- existing rows must not break |

Re-running `evaluate_load_detention` on the same load is a no-op via
`$setOnInsert` on the composite `_id`. Idempotency holds independently
on each path.

### Backward compatibility

When `load.stops` is missing OR an empty list, the legacy flat-fields
synthesis path runs (pickup at `stop_idx=0` from `shipper_arrived_at` /
`shipper_departed_at` (+ aliases), delivery at `stop_idx=1` from
`consignee_arrived_at` / `consignee_departed_at` (+ aliases)). All FF2
fixtures (walkthrough stage 19 + the 8 baseline tests) flow through this
path unchanged.

## Read-only kill-switch

Both `evaluate_load_detention` and `scan_loads_for_detention` honor
`integrity.operational_state.is_read_only()` and short-circuit with
`status="skipped_read_only"` when active. The cron loop in
`backend/cron_scheduler.py` additionally honors `should_skip_tick()` for
defense-in-depth.

## Test coverage

`backend/tests/test_detention_calculator.py` -- 14 tests:

- 8 FF2 baseline (math correctness, idempotency, scan filters,
  read-only short-circuit).
- 6 GG2 closure:
  - per-shipper default applies when no per-load override
  - per-load override beats shipper default
  - module default when neither
  - multi-stop emits per-stop charges
  - multi-stop idempotency
  - legacy pickup/delivery synthesis fallback when no `stops` array

`backend/tests/synthetic_load_walkthrough.py` stage 19 calls the
production service via `evaluate_load_detention`. All 22 walkthrough
tests still pass under the GG2 changes (legacy-synthesis backward compat
verified end-to-end).

## Open follow-ups (post-GG2)

1. **Weekend / holiday differentials** (FF2 follow-up #3, deferred):
   premium hourly rate when wait spans Sat/Sun or federal holidays.
   Needs operator pricing-policy decision + per-shipper opt-in flag.
2. **Shipper-rate negotiation snapshot**: capture historical
   detention-rate disputes per shipper to inform contract renegotiation
   and/or auto-flag chronic offenders to ops.
3. **Geofence-based arrival auto-stamp**: tie into the GPS check-call
   service so arrival/departure timestamps populate automatically when
   the truck enters/exits the shipper-provided geofence -- eliminates
   driver-self-report friction that currently makes detention
   under-billed by ~30% per industry benchmarks.
4. **Per-shipper `default_detention_max_hours_per_stop`**: schema field
   already wired through `_resolve` -- needs UI surface in shipper-admin
   (front-end work).
5. **Detention-charge reversal flow**: when a dispute is approved,
   need a documented reversal path that writes a credit row rather than
   deletes the original (audit integrity).

## File map (GG2)

| File                                                       | Lines (delta) | Purpose                                |
|------------------------------------------------------------|---------------|----------------------------------------|
| `backend/detention/detention_calculator.py`                | ~+95          | shipper lookup + multi-stop iter       |
| `backend/tests/test_detention_calculator.py`               | ~+275         | 6 new tests                            |
| `chl-memory/research/detention_service_design.md` (this)   | new           | spec doc                               |
