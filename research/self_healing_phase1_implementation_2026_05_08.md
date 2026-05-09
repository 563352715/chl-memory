# Self-Healing Phase 1 SCAFFOLD -- Implementation (PP1, 2026-05-08 EOD-16-late)

## Context

Operator-mandated 2026-05-08 EOD-15-late: build a self-healing platform.
Architectural advisory landed at
`chl-memory/research/self_healing_ai_architecture_advisory_2026_05_08.md`
(MM4 advisory). Sub-agent stream PP1 ships the Phase 1 SCAFFOLD.

Per the advisory section 6 + section 8, Phase 1 = lowest code-mod risk +
highest immediate operational value. Three pieces:

1. **Tier D: cron self-resurrection** -- a supervisor that watches
   `/api/health/system`, attempts soft-restart of dead cron loops, and
   escalates persistent failures to `db.urgent_alerts`.
2. **Tier E: memory log** -- `db.self_heal_actions`, append-only record
   of every self-heal action.
3. **Frontend Self-Heal tab** -- read-only operator visibility surface.

This stream ships ALL three pieces but leaves the cron registration in
`cron_scheduler.py` COMMENTED OUT. Operator green-lights live activation
by setting `CHL_SELF_HEAL_PHASE1_ENABLED=true` and uncommenting the
registration block (the activation runbook below documents the exact
3 commands).

## What shipped

### Backend modules (new)

| File                                                | Lines | Purpose                                                          |
| --------------------------------------------------- | ----- | ---------------------------------------------------------------- |
| `backend/self_healing/self_heal_log.py`             | ~350  | `db.self_heal_actions` schema + helpers (record/list/health).    |
| `backend/self_healing/cron_supervisor.py`           | ~430  | Tier D supervisor logic + restart hook registry + escalation.    |
| `backend/self_healing/self_heal_router.py`          | ~140  | 3 GET endpoints under `/api/_self_heal/*`. Owner+broker gated.   |
| `backend/self_healing/__init__.py`                  | +20   | Export the new modules.                                          |

### Backend tests (new)

| File                                              | Lines | Tests | Status |
| ------------------------------------------------- | ----- | ----- | ------ |
| `backend/tests/test_self_heal_log.py`             | ~290  | 4     | PASS   |
| `backend/tests/test_cron_supervisor.py`           | ~290  | 3     | PASS   |
| `backend/tests/test_self_heal_router.py`          | ~290  | 3     | PASS   |
| **Total**                                         | ~870  | **10**| **10/10 PASS** |

### Backend wiring

| File                                | Change                                                             |
| ----------------------------------- | ------------------------------------------------------------------ |
| `backend/server.py`                 | +20 lines -- mount self_heal_router + register supervisor health.  |
| `backend/cron_scheduler.py`         | +25 lines -- COMMENTED OUT supervisor cron registration block.     |

### Frontend

| File                                | Change                                                             |
| ----------------------------------- | ------------------------------------------------------------------ |
| `frontend/src/App.js`               | +1 import (`ShieldCheck`), +1 nav entry, +1 tab route, +`SelfHealView` component (~310 lines). |

### Doc (this file)

| File                                                                       | Lines |
| -------------------------------------------------------------------------- | ----- |
| `chl-memory/research/self_healing_phase1_implementation_2026_05_08.md`     | ~300  |

## Schema -- `db.self_heal_actions`

Document shape (v1, append-only):

```python
{
  "_id": uuid,
  "trigger_kind": "cron_failure" | "anomaly" |
                  "patch_proposal_approved" | "manual_test",
  "trigger_ref": str,            # cron name | anomaly_id | patch_id
  "action_taken": "cron_restart" | "patch_applied" |
                  "weight_adjusted" | "noop",
  "actor": "self_heal_supervisor" | "embedded_llm_loop" |
            "operator_override",
  "tier": "GREEN" | "YELLOW" | "RED",
  "before_state": dict,          # snapshot of relevant state
  "after_state": dict,           # snapshot after action
  "outcome": "success" | "failed" | "rolled_back" | "in_progress",
  "outcome_reason": str | None,
  "started_at": iso,
  "completed_at": iso | None,
  "metric_watched": str | None,  # for Phase 4 rollback
  "metric_baseline": float | None,
  "metric_post_action": float | None,
  "rolled_back_at": iso | None,
  "created_at": iso,
}
```

Indexes recommended (not yet declared -- collection is created on first
write, indexes can be added later via a one-shot migration without
disrupting the writer):

- `(started_at, -1)` -- powers `list_recent_actions` time-window scan.
- `(tier, outcome)` -- powers the frontend dashboard filters.
- `(trigger_ref, started_at, -1)` -- powers per-cron history queries
  (future Phase 2 + Phase 4 rollback queries).

## What's COMMENTED OUT

In `backend/cron_scheduler.py`, immediately before the Phase 10.1.3
load-board scraper TODO block:

```python
# ------------------------------------------------------------------
# Self-Heal Phase 1 SCAFFOLD (PP1, 2026-05-08 EOD-16-late) -- COMMENTED
# OUT pending operator green-light. The supervisor module is FULLY
# IMPLEMENTED and unit-tested; the only thing missing is the live
# registration below.
# ------------------------------------------------------------------
# from self_healing import cron_supervisor as _cron_supervisor
# asyncio.create_task(_cron_supervisor.supervisor_loop(db))
# logger.info("cron_scheduler: cron_supervisor loop armed (Phase 1).")
# ------------------------------------------------------------------
```

This is the SOLE difference between "scaffold" and "armed".

Defense-in-depth: even if the block is uncommented prematurely,
`supervise_crons()` is gated by the env var
`CHL_SELF_HEAL_PHASE1_ENABLED`. Without that set to `"true"`, the
function returns a noop summary and writes nothing.

## Activation runbook (3 commands)

When operator green-lights Phase 1:

```powershell
# 1. Set the feature flag (uses the standing-directive helper).
C:/CHL/scripts/set_env_var.ps1 -Key CHL_SELF_HEAL_PHASE1_ENABLED -Value "true"

# 2. Uncomment the supervisor cron block in cron_scheduler.py.
#    (Manual edit -- 3 lines + remove the surrounding comment markers.)

# 3. Restart the NSSM "chl-backend" service to pick up the change.
nssm restart chl-backend
```

After step 3:

- The supervisor runs every 60s (5-min cold-start delay).
- Activity is visible in the frontend Self-Heal tab.
- API surfaces:
    - `GET /api/_self_heal/recent?since_hours=24&tier=&outcome=&limit=100`
    - `GET /api/_self_heal/{action_id}`
    - `GET /api/_self_heal/_health`
- Health: the supervisor registers itself in `/api/health/system` as
  `cron_supervisor`. Status reads:
    - `healthy` when loop_running=True AND last tick was OK
    - `healthy` when feature flag is OFF (loop_running=False is fine)
    - `degraded` when feature flag is ON but the loop is dead

## Observability via the frontend tab

Tab nav entry: `{ id: "self_heal", label: "Self-Heal", icon: ShieldCheck }`.
Owner-gated. Located between Renewals and Ops Console in the top-bar.

Header: 4 tiles
1. **last 24h count** + total_logged
2. **by tier**: GREEN / YELLOW / RED counts (GREEN-tier actions are
   restart-class, never code-mod)
3. **by outcome**: success / failed / rolled_back counts
4. **supervisor status**: operational / degraded indicator

Filters
- Time window: last 1h / 24h / 7d / 30d
- Tier: All / GREEN / YELLOW / RED
- Outcome: All / success / failed / rolled_back / in_progress
- Action pills: All / Cron Restart / Patch Applied / Weight Adjusted / Noop

Table columns
- Trigger (kind + ref)
- Action
- Tier
- Actor
- Outcome
- Started (ISO timestamp, formatted)
- Completed
- Reason (outcome_reason)

Click row -> drill-down with before_state + after_state JSON pretty-print
+ watched-metric details (baseline / post-action) when present.

Auto-refresh: 60s.

Read-only: NO buttons. Phase 1 doesn't include manual override surfaces.

## Rollback path

If Phase 1 misbehaves after activation:

```powershell
# Disable the feature flag.
C:/CHL/scripts/set_env_var.ps1 -Key CHL_SELF_HEAL_PHASE1_ENABLED -Value "false"

# Restart the service to pick up the flag.
nssm restart chl-backend
```

The supervisor loop continues to run but `supervise_crons()` returns a
noop on every tick (feature flag check at the top of the function).

For a full rollback, re-comment the cron registration block in
`cron_scheduler.py` and restart. The collection `db.self_heal_actions`
is left intact -- no rollback path needs to delete prior actions.

## Hard guardrails (in v1)

The supervisor will NEVER:

- Restart itself or sibling self-heal modules (`HARDBLOCK_MODULES`
  frozenset enforces this).
- Modify code. Phase 1 only restarts processes.
- Take action when `integrity.operational_state.should_skip_tick()`
  returns True. Honors the global kill-switch.
- Write outside the `db.self_heal_actions` + `db.urgent_alerts`
  collections.

Cap on hammer: `max_restart_attempts=3` consecutive failures within
`restart_window_seconds=60` -> escalate to `db.urgent_alerts` (severity
HIGH) + stop trying. Operator must intervene to clear the rolling
window (in-process; restart of the service also clears it).

## Test strategy

- **`test_self_heal_log.py`**:
    - `test_record_action_inserts_row_with_required_fields` -- schema
      compliance, all required fields populated, enum validation.
    - `test_list_recent_filters_by_since_hours` -- time-window filter
      correctness.
    - `test_list_recent_filters_by_tier` -- tier filter correctness.
    - `test_health_counters_by_outcome` -- counter rollups match
      seeded data.

- **`test_cron_supervisor.py`**:
    - `test_supervisor_skips_when_all_crons_healthy` -- no false
      positives + feature-flag gate respected.
    - `test_supervisor_logs_restart_action_on_failed_cron` -- success
      path + no-hook noop path both write to the log.
    - `test_supervisor_escalates_after_3_consecutive_failures` --
      threshold-based escalation, no further attempts post-threshold.

- **`test_self_heal_router.py`**:
    - `test_recent_returns_actions_with_filter` -- query params + 400
      on bad enum.
    - `test_get_action_returns_single_drill_down` -- 200 + 404 paths.
    - `test_routes_require_appropriate_auth` -- owner-gate + broker-gate
      enforced.

All 10 PASS in 0.66s on Python 3.14.4 / pytest-9.0.3.

Preflight clean: `175 routers + 18 crons + 5 health-registers verified`
(was 174 before this stream; +1 is the new self_heal_router).

Frontend build: `+1.14 kB gzip` to main.js. Builds clean (only
pre-existing eslint warnings).

## Phase 2 / 3 / 4 follow-ups (priority-ordered)

The advisory's 4-phase plan informs the next streams:

1. **Phase 2 -- Tier A GREEN-class auto-merge** (2 sprints).
   Worktree-isolated `git apply` + targeted-pytest harness +
   `[selfheal:GREEN]` commit prefix. Six safe categories: log-level
   adjustments, dependency pin bumps, test-fixture updates, typo fixes,
   missing-import imports, retry-count bumps. Daily AgentDM summary
   to operator + `@pm-lead`. The self_heal_log already has
   `action_taken="patch_applied"` in its enum; Phase 2 just starts
   writing those rows.

2. **Phase 3 -- Tier C embedded LLM reasoning loop** (2 sprints).
   Continuously-running NSSM service that subscribes to event streams
   (Mongo change-streams or polling), maintains a working-memory ring
   buffer of degraded modules, reasons cross-source, issues actions.
   New budget category `llm_self_healing_loop` $30/day. The
   self_heal_log already has `actor="embedded_llm_loop"` in its enum;
   Phase 3 starts writing those rows.

3. **Phase 4 -- Tier B rollback metric watch** (1 sprint).
   Wraps every self-applied change with a `watched_metric` row +
   30-min auto-rollback window. The self_heal_log already has the
   `metric_watched` / `metric_baseline` / `metric_post_action` /
   `rolled_back_at` columns; Phase 4 uses them. New `outcome`
   value `rolled_back` is already in the enum.

The schema deliberately oversteps Phase 1's needs to avoid schema
migrations across phases.

## Cross-references

- `chl-memory/research/self_healing_ai_architecture_advisory_2026_05_08.md`
  (MM4 advisory; the design)
- `backend/self_healing/__init__.py`
- `backend/self_healing/self_heal_log.py`
- `backend/self_healing/cron_supervisor.py`
- `backend/self_healing/self_heal_router.py`
- `backend/cron_scheduler.py` (commented-out registration block)
- `backend/server.py` (router mount + health-check registration)
- `frontend/src/App.js` (SelfHealView component)
- `backend/tests/test_self_heal_log.py`
- `backend/tests/test_cron_supervisor.py`
- `backend/tests/test_self_heal_router.py`
- `~/.claude/projects/c--CHL/memory/feedback_set_env_var_helper_and_handover_rules.md`
  (set_env_var.ps1 helper -- used in the activation runbook)

End of implementation doc.
