# NCS-3 Multi-Leg Constrained Solver — Design Spec

**Purpose:** the routing engine that turns NCS-2 driver-mode-toggle + NCS-1 universal-load-board observation into a real driver-side feature. Given a driver's mode (HOME_ARRIVAL or REVENUE_MAX) + their current location + constraints, find the optimal chain of loads they should take next.

**Status: DESIGN, not implementation.** Captures the algorithm + data shape so the build can begin once 64 GB RAM lands (2026-05-16) and NCS-1 has accumulated enough observation data to feed it.

**Strategic rationale:** see `~/.claude/projects/c--CHL/memory/project_network_control_strategy_2026_05_09.md` (NCS-3 section). Operator-mandated 2026-05-09 EOD-17.

## Problem statement

Given:
- A driver at location `(lat, lng)` at time `T`
- Driver's mode (HOME_ARRIVAL with home + target_date, OR REVENUE_MAX with min_$/mi)
- Constraints: HOS (Hours of Service), equipment type, equipment match per load, time windows, geofencing
- A pool of candidate loads (from `db.market_loads_observed` if NCS-1 deployed, else `db.loads` available status only)

Find: an ordered chain of loads `[L1, L2, ..., Ln]` such that:
- L1 can be picked up by the driver (timing + location feasible)
- Each Li+1 can be picked up after Li delivered (deadhead time + drive time fits)
- HOS rules respected (max 11 hours driving / 14 hours on-duty per day, 10-hour rest)
- Equipment matches throughout
- For HOME_ARRIVAL: Ln's destination is within `max_distance_from_home_mi` of home AND delivered before `target_date`
- For REVENUE_MAX: total $/mi maximized minus deadhead penalty
- Total chain length ≤ N (default N=4 — beyond that, the solver becomes intractable + driver preferences shift)

This is a **constrained shortest-path problem on a directed weighted graph** — closely related to Vehicle Routing Problem (VRP) but simplified to single-driver / no-explicit-base-return.

## Algorithm choice

Three contenders evaluated:

### A. Modified Dijkstra + capacity constraints
- Pros: deterministic, runs in O((V+E) log V), simple to debug
- Cons: doesn't naturally handle HOS / time-window constraints; would need to encode time-state in the node ID (state-expanded graph)

### B. A* with admissible heuristic
- Pros: cuts search space dramatically with good heuristic; naturally handles constraints
- Cons: heuristic design is the hard part; for HOME_ARRIVAL the heuristic is "great-circle distance to home + time-to-target"; for REVENUE_MAX it's "remaining capacity × max-$/mi seen"
- **PREFERRED CHOICE.** Heuristic well-defined for both modes; performance scales.

### C. Branch-and-bound + constraint propagation (e.g., OR-tools)
- Pros: optimal solutions for small N, handles arbitrary constraints
- Cons: dependency footprint (Google OR-tools is 200+ MB), often slower than A* for our N≤4
- DEFER: revisit if A* performance degrades

**Decision: A* with mode-specific heuristic.**

## Algorithm pseudocode

```
def solve(driver_state, candidate_loads, mode_config, max_chain=4):
    # State = (current_lat, current_lng, current_time, accumulated_revenue,
    #         accumulated_hos_drive_hours, accumulated_hos_on_duty_hours,
    #         loads_taken_so_far)

    initial = State(driver.lat, driver.lng, driver.time, 0.0, 0.0, 0.0, [])
    open_set = priority_queue()
    open_set.put(initial, priority=heuristic(initial, mode_config))
    best_chain = None

    while open_set not empty:
        current = open_set.pop()

        # Goal check (mode-specific)
        if is_goal(current, mode_config):
            if better_than(current, best_chain, mode_config):
                best_chain = current
            # Don't break -- might find better with longer chains

        if len(current.loads_taken) >= max_chain:
            continue

        # Expand: for each candidate load reachable from current
        for load in feasible_next_loads(current, candidate_loads, mode_config):
            next_state = apply_load(current, load)
            if violates_hos(next_state):
                continue
            if violates_geofence(next_state, load):
                continue
            cost = compute_cost(current, load, next_state, mode_config)
            priority = cost + heuristic(next_state, mode_config)
            open_set.put(next_state, priority)

    return best_chain
```

## Mode-specific heuristics

### HOME_ARRIVAL heuristic

```
def heuristic_home_arrival(state, mode_config):
    # Distance to home (great-circle, optimistic lower bound on remaining miles)
    dist_home = haversine(state.lat, state.lng, home.lat, home.lng)
    # Time fit: penalty if we can't make target_date even at max speed
    remaining_hours = (target_date - state.current_time).total_hours()
    max_hours_to_home = dist_home / 55.0  # avg speed 55mph
    time_penalty = 0
    if max_hours_to_home > remaining_hours:
        time_penalty = (max_hours_to_home - remaining_hours) * 1000  # heavy penalty
    return dist_home + time_penalty
```

### REVENUE_MAX heuristic

```
def heuristic_revenue_max(state, mode_config):
    # Best-case remaining revenue: max-$/mi-seen × max remaining drive hours × 55mph
    remaining_drive_hours = max(0, HOS_DAILY_DRIVE_LIMIT - state.accumulated_hos_drive)
    optimistic_remaining_miles = remaining_drive_hours * 55.0
    optimistic_remaining_revenue = optimistic_remaining_miles * max_rpm_seen
    # Negative because we minimize cost; A* expects lower=better
    return -optimistic_remaining_revenue
```

## Data structures

### Input: candidate_loads from NCS-1 observation pool

Each load needs:
- `id`, `origin_lat/lng`, `destination_lat/lng`, `pickup_window_start/end`, `delivery_by`
- `equipment_type`, `commodity`, `specials` (list)
- `posted_rate`, `distance_miles`
- `shipper_id` (for vetting filter)

### Output: chain

```python
{
  "driver_id": "...",
  "mode": "HOME_ARRIVAL",
  "chain": [
    {"load_id": "...", "leg": 1, "pickup_eta": "...", "delivery_eta": "...",
     "deadhead_to_pickup_mi": 23.4, "leg_revenue": 2400, "leg_$/mi": 3.2},
    ...
  ],
  "total_revenue": ...,
  "total_miles": ...,
  "total_deadhead_mi": ...,
  "ends_within_target": True,
  "ends_distance_from_home_mi": 45.2,
  "ends_at_iso": "...",
}
```

## Performance targets

- Driver-facing query: < 5 seconds for N≤4
- Cold cache (no precomputed lane stats): < 30 seconds
- Memory per query: < 200 MB (so 50 concurrent queries fit in ~10 GB RAM)
- Caching: per-driver result cached 5 minutes (driver re-queries are cheap)

## Constraints encoded

**HOS (Hours of Service) — FMCSA part 395:**
- Max 11 hours driving in a 14-hour on-duty window
- 10-hour off-duty break required between shifts
- 60-hour rolling on-duty / 7 days OR 70 / 8 days
- 30-min break after 8 cumulative driving hours

**Time windows:**
- Each load has `pickup_window_start/end` and `delivery_by`
- Driver must arrive at pickup within window
- Driver must deliver before `delivery_by`

**Equipment match:**
- Driver's truck type must match load.equipment_type
- HAZMAT loads require driver hazmat endorsement (queryable from FMCSA cache)
- Oversized loads require permits (operator-supplied)

**Geofencing:**
- The pickup/delivery 50yd inclusive radius enforced at the state-machine layer (LLL17), not in the solver — solver assumes both can be reached

## Dependencies

- **NCS-1** (universal load-board observation) — needs to be populated with real observation data; without it, the candidate pool is small and the solver's value is limited
- **NCS-2** (driver-mode toggle) — provides DriverPreferences input
- **lane_intelligence.py** — provides historical achieved-margin data per lane (used to filter low-margin candidates for REVENUE_MAX mode)
- **carrier_outreach.py** — driver location feed via GPS pings
- **vetting_checks.py** — filters out loads from unvetted shippers (per-shipper credit gate)

## Implementation phases

### Phase 1 — single-leg suggester (1 week, foundation)
- Implement `score_load_for_driver()` (already done in NCS-2 `driver_mode_toggle.py`)
- Add HOS feasibility check
- Add equipment match
- Return top 5 single-load suggestions

### Phase 2 — 2-leg chain (1 week, simplest case)
- Implement `feasible_next_loads()` — given current load completion, find loads reachable next
- Heuristic-driven A* with depth=2
- Test against synthetic 2-leg scenarios

### Phase 3 — N-leg chain (2 weeks, full algorithm)
- Generalize A* to depth=N (default 4)
- Add caching + result memoization
- Benchmark performance: should hit 5-second target

### Phase 4 — Integration into PWA (1 week)
- New endpoint: `POST /api/public/carrier-offer/{token}/suggest-chain`
- Returns top 1-3 chains
- PWA renders the chain as a sequence of loads driver can accept

### Total: ~5 weeks

## When to start

After:
1. RAM upgrade lands (2026-05-16) — solver wants ≥32 GB to run 50+ concurrent queries comfortably
2. NCS-1 populated with ≥30 days of observation data (~July 2026)
3. NCS-2 (driver_mode_toggle.py) wired into PWA UI

Realistic build window: **August-September 2026**.

## Cross-references

- `~/.claude/projects/c--CHL/memory/project_network_control_strategy_2026_05_09.md` (strategic context)
- `~/.claude/projects/c--CHL/memory/project_business_model_decision_2026_05_09.md` (Model C generalist mandate)
- `backend/driver_mode_toggle.py` (NCS-2, already shipped LLL18d)
- `backend/market_loads_observed.py` (NCS-1, already shipped LLL18d, awaiting board credentials)
- `backend/lane_intelligence.py` (LLL18 — feeds margin data to NCS-3)
- `backend/multi_leg.py` (existing scaffolding — read this when building Phase 2)
