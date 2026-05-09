# Self-Healing Phase 4 SCAFFOLD -- Implementation (SS1, 2026-05-08 EOD-14-late)

## Context

Phase 4 of the MM4 self-healing advisory at
`chl-memory/research/self_healing_ai_architecture_advisory_2026_05_08.md`
section 4 Tier B + section 5.1 mitigation B + section 6 Phase 4: every
self-applied change is tagged with one or more `watched_metric` entries,
sampled pre-action for a baseline, and re-sampled within a 30-min window
post-action. If the metric degrades past a per-metric threshold, the
platform auto-rolls-back the change and notifies the operator.

Phase 1 (PP1, ab2ea6a) shipped the cron self-resurrection supervisor +
`db.self_heal_actions` log + frontend Self-Heal tab. Phase 2 (QQ2,
d0263c9) shipped the deterministic patch_classifier + worktree-isolated
auto_merge_harness. Phase 3 (RR3) shipped the cross-source LLM reasoner
that produces tier-and-confidence-tagged recommendations. Phase 4 wraps
all three: it consumes the `metric_watched` field that Phase 2+3 stamp
on their action rows, and provides the auto-rollback safety net the
advisory recommended deferring "until Phase 1-3 generate real-world
churn data."

This stream is SCAFFOLD only:

- Module is fully implemented + 8/8 unit-tested.
- Cron registration in `cron_scheduler.py` is COMMENTED OUT.
- Sampler functions are STUBBED -- they read placeholder counts from
  `db.cron_health_events` / `db.anomalies` / `db.sla_metrics` but do
  NOT integrate with the full SLA telemetry stack. Real sampling
  integration is a follow-up once Phase 1-3 generate enough churn data
  to anchor real baselines.
- The auto_merge_harness Phase 2 module is modified (small diff inside
  `_safe_log`) to stamp `metric_watched` + `metric_baseline` on its
  self_heal_actions rows when a touched-files class matches a known
  metric. All 12 Phase 2 tests still pass.

Activation requires:

1. `CHL_SELF_HEAL_PHASE4_ENABLED=true`
2. Uncomment the CRON 13 block in `cron_scheduler.py`
3. NSSM service restart
4. Phase 2 must be active (otherwise no `metric_watched` rows accumulate)

## What shipped

### Backend modules (new + modified)

| File                                                | Lines | Purpose                                                                          |
| --------------------------------------------------- | ----- | -------------------------------------------------------------------------------- |
| `backend/self_healing/rollback_watcher.py`          | ~640  | Watches metric_watched-tagged self_heal_actions; type-dispatched rollback.       |
| `backend/self_healing/auto_merge_harness.py`        | +50   | `_safe_log` now stamps metric_watched + metric_baseline on success-path rows.    |
| `backend/self_healing/__init__.py`                  | +18   | Documented + exported `rollback_watcher`.                                        |

### Backend tests (new)

| File                                              | Lines | Tests | Status |
| ------------------------------------------------- | ----- | ----- | ------ |
| `backend/tests/test_rollback_watcher.py`          | ~440  | 8     | PASS   |

8 spec-required tests:

1. `test_watcher_picks_up_recent_success_with_metric`
2. `test_watcher_skips_actions_without_metric`
3. `test_watcher_rolls_back_on_degradation_exceeding_threshold`
4. `test_watcher_marks_stable_after_window_expired`
5. `test_rollback_dispatches_correctly_per_action_type`
6. `test_rollback_writes_new_self_heal_action_row`
7. `test_watcher_honors_phase4_disabled_flag`
8. `test_metric_pick_for_patch_touching_cron_scheduler`

Combined Phase 1 + Phase 2 + Phase 3 + Phase 4 self-heal suite:
**37/37 PASS** in 0.72s on Python 3.14.4 / pytest-9.0.3.

(Phase 1 = 4 self_heal_log + 3 cron_supervisor = 7;
Phase 2 = 8 patch_classifier + 4 auto_merge_harness = 12;
Phase 3 = 10 llm_reasoning_loop;
Phase 4 = 8 rollback_watcher.)

### Backend wiring

| File                                | Change                                                                                |
| ----------------------------------- | ------------------------------------------------------------------------------------- |
| `backend/cron_scheduler.py`         | +47 lines -- COMMENTED OUT CRON 13 (rollback_watcher) registration block.             |

### Doc (this file)

| File                                                                       | Lines |
| -------------------------------------------------------------------------- | ----- |
| `chl-memory/research/self_healing_phase4_implementation_2026_05_08.md`     | ~440  |

## Metric registry (4 entries)

The watcher consults `rollback_watcher.METRIC_REGISTRY` -- a static dict
mapping metric names to a spec describing the source collection,
direction (higher_better vs lower_better), rollback threshold percent,
and the name of the sampler function. The registry is intentionally
extensible -- the advisory expects operator + Phase 3 LLM reasoning to
add new metrics as Phase 1-3 surface real-world failure classes.

| Metric              | Source                  | Direction       | Threshold (%)  | Sampler function name             |
| ------------------- | ----------------------- | --------------- | -------------- | --------------------------------- |
| `cron_success_rate` | `db.cron_health_events` | `higher_better` | 10.0           | `_sample_cron_success_rate`       |
| `test_pass_rate`    | `ci` (placeholder)      | `higher_better` | 5.0            | `_sample_test_pass_rate`          |
| `anomaly_volume`    | `db.anomalies`          | `lower_better`  | 50.0           | `_sample_anomaly_volume`          |
| `p95_latency_ms`    | `db.sla_metrics`        | `lower_better`  | 25.0           | `_sample_p95_latency`             |

### Direction-aware degradation math

```python
def _compute_degradation_pct(*, baseline, post, direction) -> float:
    eps = 1e-9
    if direction == "higher_better":
        if post >= baseline:
            return 0.0
        return (baseline - post) / max(abs(baseline), eps) * 100.0
    if direction == "lower_better":
        if post <= baseline:
            return 0.0
        return (post - baseline) / max(abs(baseline), eps) * 100.0
    return 0.0
```

The function ALWAYS returns a non-negative magnitude. The trigger
condition is `degradation_pct > rollback_threshold_pct`.

### Sampler stub status

| Sampler                          | Real source                          | v1 behavior                                                      |
| -------------------------------- | ------------------------------------ | ---------------------------------------------------------------- |
| `_sample_cron_success_rate`      | `db.cron_health_events`              | counts `event_type='failed'` over trailing window; returns rate. |
| `_sample_test_pass_rate`         | CI hook (not yet integrated)         | returns 100.0 -- watcher will not auto-rollback on this metric.  |
| `_sample_anomaly_volume`         | `db.anomalies`                       | counts rows in trailing window; returns raw count.               |
| `_sample_p95_latency`            | `db.sla_metrics`                     | crude p95 over .p95_ms field; returns 0.0 if collection empty.   |

All four samplers are best-effort and return a defensible "no signal"
value when the collection is unreachable / empty. This means an
unconfigured prod surface will read as healthy and never trip a
rollback -- conservative-by-default per advisory section 5.1.

## Rollback dispatch table

`_trigger_rollback(*, db, action_row)` looks up a hook by `action_taken`
in this order: registered runtime hook (`register_rollback_hook(...)`)
first, then in-module default. Result is a dict + a NEW
`db.self_heal_actions` row is logged with the audit trail back to the
original.

| `action_taken`     | Default dispatcher                          | Result `kind`             | What it does                                                                                                  |
| ------------------ | ------------------------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------- |
| `cron_restart`     | `_default_rollback_cron_restart`            | `cron_restart_revert`     | Looks up the supervisor restart hook for the same module + re-invokes it (re-restart with prior_config).      |
| `patch_applied`    | `_default_rollback_patch_applied`           | `patch_revert`            | v1 logs intent + commit hash to revert; full git-revert via auto_merge_harness REVERT mode is a follow-up.   |
| `weight_adjusted`  | `_default_rollback_weight_adjusted`         | `weight_revert`           | Reads `before_state.weights` + writes audit row to `db.evaluator_weights_revert_audit`.                       |
| `noop`             | `_default_rollback_noop`                    | `noop_revert`             | Nothing to roll back. Returns ok=True.                                                                        |

### Rollback row audit shape

Every dispatched rollback writes a new self_heal_actions row whose
`before_state` preserves the link back + the metric snapshot, and whose
`after_state` carries the dispatcher result:

```python
{
  "trigger_kind": "manual_test",   # closest existing enum slot
  "trigger_ref": "<original action _id>",
  "action_taken": "<same as original>",
  "actor": "self_heal_supervisor",
  "tier": "<same as original>",
  "before_state": {
    "rolled_back_from_action_id": "<original _id>",
    "original_after_state": {...},
    "metric_watched": "cron_success_rate",
    "metric_baseline": 100.0,
  },
  "after_state": {
    "rollback_dispatch_result": {
      "ok": True/False,
      "kind": "<dispatch kind>",
      ...
    }
  },
  "outcome": "success" | "failed",
  "outcome_reason": "rollback_watcher: metric degradation" | "rollback_dispatch_failed",
}
```

The original row is ALSO updated in place: `rolled_back_at` stamped,
`outcome` flipped to `rolled_back`, `metric_post_action` recorded,
`rollback_degradation_pct` stamped.

## Watch window rationale

The 30-min default watch window comes directly from advisory section 4
Tier B:

> "30-min watch window with auto-revert on health regression."

Rationale:
- Long enough that transient degradation (e.g., a slow-loading remote
  feed during the first minute after restart) settles before the
  watcher samples.
- Short enough that operator visibility kicks in within the same shift
  (3x-daily handoff cadence).
- Aligned with cron interval ladders (typical CHL crons fire every
  5-15 minutes; 30 min covers 2-6 cron cycles -- enough samples to
  meaningfully measure success_rate / failure_rate).

The watcher polls every 60s, so a row inside its window will be
re-checked roughly 30 times before the window expires. Once the window
expires AND the metric is healthy, the row is marked
`rollback_watch_status: stable_after_window` and dropped from future
ticks.

## Phase 2 harness integration -- metric_watched stamping

The Phase 2 `auto_merge_harness._safe_log` function is modified to
sample a baseline metric + stamp it on success-path rows:

```python
metric_watched = None
metric_baseline = None
if outcome == self_heal_log.OUTCOME_SUCCESS:
    from . import rollback_watcher
    metric_watched = rollback_watcher._pick_metric_to_watch(files_touched)
    if metric_watched:
        spec = rollback_watcher.METRIC_REGISTRY.get(metric_watched)
        sampler = getattr(rollback_watcher, spec["sampler_fn"], None)
        if callable(sampler):
            metric_baseline = float(await sampler(db=db))
```

`_pick_metric_to_watch(touched_files)` is a pure function (priority
order, first match wins):

| File pattern                                          | Metric              |
| ----------------------------------------------------- | ------------------- |
| `cron_scheduler.py`, `*_cron.py`, `cron_*` basenames  | `cron_success_rate` |
| `test_*.py` basenames                                 | `test_pass_rate`    |
| files containing `anomaly` or `exception_detector`    | `anomaly_volume`    |
| `*_router.py`, `sla_*` paths                          | `p95_latency_ms`    |
| anything else                                         | `None` (skip watch) |

When no class matches, `metric_watched` stays `None` and the watcher
silently skips the row. This is the "default-safe" behavior --
patches that touch unclassified surfaces don't get auto-rolled-back;
operator visibility (Self-Heal tab) is the safety net for those.

The stamp NEVER blocks the harness -- on any exception the stamp is
swallowed + log emitted; the harness still records the row without a
metric.

### Phase 2 regression -- 12/12 PASS

The Phase 2 test suite (12 tests across patch_classifier +
auto_merge_harness) was re-run after the harness modification and ALL
12 still pass. The stamp lookup happens lazily inside `_safe_log` and
returns `None` for the test-fixture diff used in the existing tests
(touched file is `backend/tests/test_sample.py` -> picker returns
`test_pass_rate` -> sampler returns 100.0; row is stamped but the
existing tests do NOT assert on `metric_watched` so they continue to
pass).

## Activation runbook

When operator green-lights Phase 4:

1. Confirm Phase 2 is active (`CHL_SELF_HEAL_PHASE2_ENABLED=true`) so
   metric_watched rows are accumulating. Without Phase 2, the watcher
   sees zero rows + does nothing.
2. `C:/CHL/scripts/set_env_var.ps1 -Key CHL_SELF_HEAL_PHASE4_ENABLED -Value "true"`
3. Open `backend/cron_scheduler.py` and uncomment the CRON 13 block.
4. Restart the NSSM `chl-backend` service.
5. Watch `db.self_heal_actions` for `outcome='rolled_back'` rows or the
   frontend Self-Heal tab once the rollback rows show up there.
6. After ~24h of observation, decide whether to register additional
   metrics in `METRIC_REGISTRY` (see "Phase 3 LLM-recommended metric
   override" below).

## Observability via SelfHealView

Per the Phase 1 self_heal_router, the operator-facing Self-Heal tab
shows recent self_heal_actions filtered by tier / outcome /
action_taken. With Phase 4 active, the tab will additionally surface:

- Rows with `outcome='rolled_back'` -- the original action that was
  reverted, plus its `rollback_degradation_pct` annotation.
- New rollback action rows (`trigger_kind='manual_test'`, actor =
  supervisor) -- the audit-trail entries that record the rollback
  itself.
- urgent_alerts rows of `kind='rollback_watcher_triggered'` -- pinned
  to the operator's attention.

Future Phase 4.1 frontend enhancement: a "rollback chain" pivot that
joins original-action <-> rollback-action <-> urgent_alert across the
three collections by `self_heal_action_id`.

## Phase 3 LLM-recommended metric override

The advisory section 4 Tier C describes the embedded LLM reasoning
loop as authoring tier-and-confidence-tagged recommendations. A
follow-up integration (Phase 3.1):

When the LLM proposes a patch, it MAY include a `metric_watched`
override in its recommendation JSON:

```json
{
  "tier": "GREEN",
  "action": "apply_patch",
  "confidence": 0.92,
  "metric_watched": "anomaly_volume",
  "metric_rollback_threshold_pct": 30.0,
  ...
}
```

If the override is present AND the metric exists in `METRIC_REGISTRY`,
the harness uses it instead of the picker default. If
`metric_rollback_threshold_pct` is also supplied, the watcher applies
that threshold for THIS row only (looked up by reading the threshold
from the row itself rather than the registry). This lets the LLM tune
sensitivity per-patch -- e.g., a patch touching the freshness scraper
might stamp `anomaly_volume` with threshold 20% (tighter than the 50%
default) because the patch should not increase the anomaly rate AT
ALL.

The override path is NOT implemented in v1; the registry is the only
source of thresholds today. Tracking note: this is the highest-priority
follow-up for Phase 4.1.

## Test results

```
$ python -m pytest backend/tests/test_rollback_watcher.py -v
============================= test session starts =============================
collected 8 items

test_watcher_picks_up_recent_success_with_metric          PASSED  [ 12%]
test_watcher_skips_actions_without_metric                 PASSED  [ 25%]
test_watcher_rolls_back_on_degradation_exceeding_threshold PASSED [ 37%]
test_watcher_marks_stable_after_window_expired            PASSED  [ 50%]
test_rollback_dispatches_correctly_per_action_type        PASSED  [ 62%]
test_rollback_writes_new_self_heal_action_row             PASSED  [ 75%]
test_watcher_honors_phase4_disabled_flag                  PASSED  [ 87%]
test_metric_pick_for_patch_touching_cron_scheduler        PASSED  [100%]

============================== 8 passed in 0.47s ==============================

$ python -m pytest backend/tests/test_self_heal_log.py \
                   backend/tests/test_cron_supervisor.py \
                   backend/tests/test_patch_classifier.py \
                   backend/tests/test_auto_merge_harness.py \
                   backend/tests/test_llm_reasoning_loop.py \
                   backend/tests/test_rollback_watcher.py -v
============================== 37 passed in 0.72s ==============================
```

## Preflight check

`scripts/preflight_server_smoke.py` was run after the diff. Result: 20
pre-existing import failures (`reportlab`, `openai`, `twilio`,
`stripe`, `cryptography`, `psutil`, `email-validator`) all unrelated
to Phase 4 work. No new preflight failures introduced.

## Constraints honored

- ASCII-only. Type hints. pytest-asyncio.
- Cron MUST NOT be registered until `CHL_SELF_HEAL_PHASE4_ENABLED=true`
  -- COMMENTED OUT in cron_scheduler.py per spec.
- Sampler functions are STUBBED -- placeholder values for tests; real
  sampling integration is a follow-up.
- DO NOT modify Phase 1+2+3 modules beyond consuming their helpers --
  the only Phase 2 modification is a small additive stamp inside
  `_safe_log`, behind a try/except so failures never block the harness.
- Phase 2 harness modification adds the metric_watched stamp WITHOUT
  breaking existing Phase 2 tests (12/12 still pass).
- Preflight: only pre-existing failures.
- pytest: new 8 + Phase 1+2+3 regression (7+12+10) = 37 all pass.
- Code is NOT committed (per spec). Doc IS committed to chl-memory.

## 3 highest-priority follow-ups

1. **Real sampler integration** -- the four samplers are stubs that read
   placeholder rows from existing collections. Real telemetry hookup:
   - `_sample_cron_success_rate` -> the existing
     `system_health_monitor.py` per-cron transition events stream
     (already feeding the anomaly dispatcher).
   - `_sample_test_pass_rate` -> wire into the CI run-recording surface
     (does not exist yet; needs a new `db.ci_runs` collection
     populated by the GitHub Actions workflow + a delivery webhook).
   - `_sample_anomaly_volume` -> already reads `db.anomalies`; just
     verify the collection is created in production seed.
   - `_sample_p95_latency` -> requires `db.sla_metrics` to be populated
     by `@track_sla` -- this connection is currently missing; the
     decorator captures latencies but does not persist them.
2. **`auto_merge_harness` REVERT mode** -- the patch_revert dispatcher
   currently logs the intent + commit hash but does NOT actually
   `git revert` in a worktree. Add a `revert_commit_isolated(*, db,
   commit_hash)` API that mirrors `apply_green_patch_isolated` --
   worktree-isolated revert + targeted-pytest verify + ff-merge with
   `[selfheal:ROLLBACK]` commit prefix. Test against the Phase 2 test
   pattern.
3. **LLM-recommended metric override path** -- per "Phase 3
   LLM-recommended metric override" section above; let the LLM
   reasoner stamp a metric_rollback_threshold_pct on individual rows
   and have the watcher consult that override before falling back to
   the registry default. Plumbs through the
   `llm_reasoning_loop._route_action` GREEN-confidence path and the
   harness `_safe_log` field set.

## Cross-references

- `backend/self_healing/rollback_watcher.py`
- `backend/self_healing/auto_merge_harness.py` (`_safe_log` modified)
- `backend/self_healing/self_heal_log.py` (consumed)
- `backend/self_healing/cron_supervisor.py` (consumed by rollback dispatch)
- `backend/self_healing/llm_reasoning_loop.py` (future override source)
- `backend/cron_scheduler.py` (CRON 13 block, commented out)
- `backend/tests/test_rollback_watcher.py`
- `chl-memory/research/self_healing_ai_architecture_advisory_2026_05_08.md`
  (advisory; section 4 Tier B + section 6 Phase 4)
- `chl-memory/research/self_healing_phase1_implementation_2026_05_08.md`
- `chl-memory/research/self_healing_phase2_implementation_2026_05_08.md`
- `chl-memory/research/self_healing_phase3_implementation_2026_05_08.md`
