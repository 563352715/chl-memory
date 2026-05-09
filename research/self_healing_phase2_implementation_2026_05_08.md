# Self-Healing Phase 2 SCAFFOLD -- Implementation (QQ2, 2026-05-08 EOD)

## Context

Phase 2 of the MM4 self-healing advisory at
`chl-memory/research/self_healing_ai_architecture_advisory_2026_05_08.md`
section 4 Tier A + section 6 Phase 2: trust-tiered auto-merge for
AI-generated patches with the GREEN class auto-applied. Phase 1 (PP1)
shipped the cron self-resurrection supervisor + `db.self_heal_actions`
log + frontend Self-Heal tab. This stream lays Phase 2's foundation:

- `patch_classifier` (pure-function regex-based GREEN/YELLOW/RED gate)
- `auto_merge_harness` (worktree-isolated apply + targeted-pytest verify
  + `[selfheal:GREEN]`-prefixed commit + fast-forward merge)
- COMMENTED-OUT cron registration block in `cron_scheduler.py`
- `auto_merge_supervisor_loop` polls `db.patch_proposals` for
  `review_status='approved' AND tier='GREEN'` rows and dispatches the
  harness for each.

Phase 2 does NOT register the cron until operator green-lights via
`CHL_SELF_HEAL_PHASE2_ENABLED=true` AND the registration block is
manually uncommented.

## What shipped

### Backend modules (new)

| File                                                | Lines | Purpose                                                                |
| --------------------------------------------------- | ----- | ---------------------------------------------------------------------- |
| `backend/self_healing/patch_classifier.py`          | ~525  | Pure-function regex-based GREEN/YELLOW/RED tier gate. NO LLM calls.    |
| `backend/self_healing/auto_merge_harness.py`        | ~615  | Worktree-isolated apply + verify + commit-with-prefix flow.            |
| `backend/self_healing/__init__.py`                  | +15   | Export the new modules.                                                |

### Backend tests (new)

| File                                              | Lines | Tests | Status |
| ------------------------------------------------- | ----- | ----- | ------ |
| `backend/tests/test_patch_classifier.py`          | ~190  | 8     | PASS   |
| `backend/tests/test_auto_merge_harness.py`        | ~340  | 4     | PASS   |
| **Total**                                         | ~530  | **12**| **12/12 PASS** |

Combined Phase 1 + Phase 2 self-heal suite: **22/22 PASS** in 0.79s.

### Backend wiring

| File                                | Change                                                                                |
| ----------------------------------- | ------------------------------------------------------------------------------------- |
| `backend/cron_scheduler.py`         | +30 lines -- COMMENTED OUT CRON 11 (auto-merge supervisor) registration block.        |

### Doc (this file)

| File                                                                       | Lines |
| -------------------------------------------------------------------------- | ----- |
| `chl-memory/research/self_healing_phase2_implementation_2026_05_08.md`     | ~350  |

## The classifier -- deterministic trust gate

`patch_classifier.classify_patch(*, diff_text, files_touched, category_hints)`
takes a unified-diff string + the list of files the patch touches, and
returns a dict:

```python
{
  "tier": "GREEN" | "YELLOW" | "RED",
  "category": str,            # human-readable tier-justification key
  "reasoning": list[str],     # operator-readable explanation lines
  "confidence": float,        # propagated from category_hints if supplied
}
```

The classifier is a pure function. It performs NO I/O and makes NO LLM
calls. The LLM upstream (sub_agent_dispatcher) generates the patch; the
classifier is the deterministic trust boundary.

### Classification rules (priority order)

The classifier walks rules from highest-blast-radius to lowest. The
FIRST rule that matches wins. This means RED detectors run first and
cannot be promoted by any GREEN detector below them.

1. **RED-path file allowlist.** Any path matching one of these regex
   patterns is forced RED:
   - `backend/auth/`, `backend/auth*.py`, `backend/session*.py`
   - `backend/security/`
   - `backend/integrity/` -- the kill-switch + audit infrastructure
   - `backend/self_healing/` -- recursive-modification protection
   - `backend/health_monitor.py`, `backend/sla_monitor.py`,
     `backend/cron_scheduler.py`
   - `backend/payment_rails.py`, `backend/factors/`
   - `backend/fmcsa*.py`, `backend/insurance*.py`, `backend/stripe*.py`
   - `.env`, `secrets.toml`, `*credentials*`, `*api*key*`

2. **RED-keyword scan in diff body.** The diff contains any of:
   - `db.payments`, `db.broker_invoices`, `db.factor_payment_events`
   - `factor_*`, `stripe_*`, `fmcsa_*`, `telnyx_*`, `jwt_*`
   - `bcrypt`, `csrf`, `xss`

3. **Schema-drop scan.** Diff contains `drop_collection`, `.drop()`,
   `drop_index`, or SQL `DROP TABLE | DROP COLUMN | ALTER TABLE ... DROP`.

4. **Cron registration / health-monitor change.** Diff modifies any
   `asyncio.create_task(...)`, `register_health_check(...)`,
   `register_cron(...)`, `register_restart_hook(...)`, or
   `@track_sla(...)` line. Forced RED unless all touched files are
   tests (then YELLOW).

5. **Schema field rename heuristic.** Diff removes one schema field name
   and adds a different one in the same file with similar value type
   (data-migration risk).

6. **GREEN detectors** (priority order):
   - All touched files are `test_*.py` -> `test_fixture` GREEN
   - Diff body is comment / docstring lines only -> `comment_fix` GREEN
   - Diff body is `logger.<level>(...)` adjustments only ->
     `log_level_adjustment` GREEN
   - All files end in `requirements.txt` / `requirements-dev.txt` /
     `constraints.txt` and ALL pin bumps stay within the same major
     version -> `dependency_pin_minor_bump` GREEN
   - Diff is exactly one + import line, no - lines -> `missing_import`
     GREEN
   - Diff modifies `retry_count`/`max_retries`/`MAX_RETRIES` and the new
     value is within +/-50% of the old -> `retry_count_tuning` GREEN

7. **Default**: YELLOW. Unknown shapes go to dev-Claude review, never
   auto-applied.

### Confidence-based demotion

If `category_hints={"confidence": <value>}` is supplied and the value is
below `LOW_CONFIDENCE_THRESHOLD` (0.7), any GREEN classification is
demoted to YELLOW with category `low_confidence_demoted`. RED
classifications are NEVER demoted -- once RED, always RED.

### Reasoning examples

GREEN test-fixture:
```json
{
  "tier": "GREEN",
  "category": "test_fixture",
  "reasoning": ["all touched files are test_*.py files (test fixture update)"],
  "confidence": 1.0
}
```

YELLOW logic-change:
```json
{
  "tier": "YELLOW",
  "category": "logic_change",
  "reasoning": ["no GREEN-tier detector matched; defaulting to YELLOW (logic change)"],
  "confidence": 1.0
}
```

RED auth-path:
```json
{
  "tier": "RED",
  "category": "auth_or_security_path",
  "reasoning": ["file 'backend/auth_helpers.py' matches RED path pattern '(^|/)backend/auth[_a-zA-Z0-9]*\\.py$'"],
  "confidence": 1.0
}
```

## The harness -- worktree-isolated apply pipeline

`auto_merge_harness.apply_green_patch_isolated(*, db, patch_id, diff_text, ...)`
runs the GREEN-classified patch through this pipeline:

```
[1] feature_enabled() check  -> CHL_SELF_HEAL_PHASE2_ENABLED=true
[2] should_skip_tick() check -> integrity kill-switch
[3] defensive re-classification -> classifier must return GREEN
[4] db.patch_proposals lookup -> review_status must be 'approved'
[5] git worktree add .claude/worktrees/auto_merge_<patch_id>
[6] git apply --whitespace=nowarn <diff_file>      (NO --force)
[7] targeted-pytest in worktree (see below)
[8] commit with [selfheal:GREEN] <description>
[9] git merge --ff-only <branch> back into main
[10] cleanup worktree
[11] log to db.self_heal_actions
```

Any failure aborts immediately, cleans up the worktree, and writes a
`db.self_heal_actions` row with `outcome=failed` and a structured
`outcome_reason` (e.g. `tests_failed_in_isolated_worktree`,
`git_apply_failed`, `merge_ff_failed`, `defensive_reclassification_not_green`,
`patch_proposal_not_approved`, `phase2_disabled`, `kill_switch_active`).

### Targeted-pytest selection

For each modified file `f`:

1. `backend/tests/test_<basename>.py` if it exists.
2. If `f` matches `*_router.py`, ALL `test_*_router.py` files.
3. Any `test_*.py` whose body textually imports the basename
   (cheap regex grep -- best-effort).

If no targeted tests resolve, the patch proceeds without pytest
verification (e.g., a comment-only doc patch). This is conservative
GREEN-class only -- the classifier already gates blast radius.

### Commit prefix

Every harness-applied commit message starts with `[selfheal:GREEN]`.
Operator can grep `git log` for self-applied changes:

```bash
git log --grep='\[selfheal:GREEN\]'
```

### Rollback on failure

The harness rolls back deterministically by NEVER merging the worktree
commit if any pipeline step fails. The worktree is force-removed via
`git worktree remove --force` plus `shutil.rmtree` defensively. The
main branch is never touched until the fast-forward merge step.

If the fast-forward merge fails (rare -- main moved between worktree
create and merge), the harness logs `merge_ff_failed`. The operator
must manually inspect the worktree commit (it still exists in
`.git/worktrees/`) and decide whether to cherry-pick.

Phase 4 (post-merge metric watch + auto-revert) is OUT OF SCOPE for
this stream. The schema in `db.self_heal_actions` already supports
Phase 4 fields (`metric_watched`, `metric_baseline`,
`metric_post_action`, `rolled_back_at`).

## Hard-"never" classes (preserved across phases)

The classifier MUST NEVER classify these as GREEN regardless of LOC
delta or apparent simplicity:

- `backend/auth/`, `backend/auth*.py`, `backend/session*.py`
- `backend/security/`
- `backend/integrity/`
- `backend/self_healing/` (recursive-modification protection)
- `backend/health_monitor.py`, `backend/sla_monitor.py`,
  `backend/cron_scheduler.py`
- `backend/payment_rails.py`, `backend/factors/`
- `backend/fmcsa*.py`, `backend/insurance*.py`, `backend/stripe*.py`
- `.env`, `secrets.toml`, `*credentials*`, `*api*key*`
- Any diff containing `db.payments`, `db.broker_invoices`,
  `factor_*`, `stripe_*`, `fmcsa_*`, `telnyx_*`, `jwt_*`, `bcrypt`,
  `csrf`, `xss`
- Any diff containing schema-drop SQL or
  `drop_collection`/`.drop()`/`drop_index`
- Any diff modifying cron registration calls (asyncio.create_task,
  register_health_check, register_cron, register_restart_hook,
  @track_sla)

These are ENFORCED at TWO layers:

1. The classifier returns RED before the harness is ever invoked.
2. The harness defensively re-classifies and refuses to apply anything
   that doesn't return GREEN -- so even if the classifier is bypassed
   (e.g., a future LLM patch generator skips the classifier), the
   harness rejects with `defensive_reclassification_not_green`.

## Connection to existing patch-proposal flow

The existing patch_proposal pipeline (Stages 1a-1d, sub_agent_dispatcher,
patch_proposal_router) is unchanged:

- Stage 1a (anomaly_dispatcher) writes `queued` rows to
  `db.anomaly_dispatch_queue`.
- Stage 1b (context_bundler) advances rows to `bundled`.
- Stage 1c (sub_agent_dispatcher) generates the patch + writes
  `db.patch_proposals` with a trust-gate (LOW/MED/HIGH).
- Stage 1d (patch_proposal_router) exposes
  `POST /api/_self_healing/patch_proposals/{id}/approve` -- operator
  flips `review_status='approved'` here.

Phase 2 ADDS a new stage AFTER 1d: the `auto_merge_supervisor_loop`
polls `db.patch_proposals` for `review_status='approved' AND
tier='GREEN' AND auto_merge_status IN [None, 'pending']`, runs each
through `apply_green_patch_isolated`, and updates the row's
`auto_merge_status` to `'merged'` or `'failed'` with the harness result
in `auto_merge_result`.

The existing `tier` field on patch_proposals (LOW/MED/HIGH from the
sub-agent dispatcher) does NOT determine auto-merge eligibility on its
own; the classifier is what gates GREEN. Future work: wire
sub_agent_dispatcher to call `patch_classifier.classify_patch` at write
time and persist the GREEN/YELLOW/RED tier in addition to LOW/MED/HIGH.

## Activation runbook (3 commands)

When operator green-lights Phase 2:

```powershell
# 1. Set the feature flag (uses the standing-directive helper).
C:/CHL/scripts/set_env_var.ps1 -Key CHL_SELF_HEAL_PHASE2_ENABLED -Value "true"

# 2. Uncomment the CRON 11 supervisor cron block in cron_scheduler.py.
#    Look for "CRON 11 -- Self-Heal Phase 2 GREEN-tier auto-merge supervisor"
#    and remove the leading "# " from these 4 lines:
#    - if os.environ.get("CHL_SELF_HEAL_PHASE2_ENABLED", "").lower() == "true":
#    -     from self_healing import auto_merge_harness as _auto_merge
#    -     asyncio.create_task(_auto_merge.auto_merge_supervisor_loop(db))
#    -     logger.info("cron_scheduler: auto_merge_supervisor_loop armed (Phase 2).")

# 3. Restart the NSSM "chl-backend" service to pick up the change.
nssm restart chl-backend
```

After step 3:

- The supervisor runs every 120s (10-min cold-start delay).
- Activity is visible in the frontend Self-Heal tab (Phase 1 surface
  already shows `db.self_heal_actions` rows; Phase 2 just starts
  writing `action_taken='patch_applied'` rows).
- `git log --grep='\[selfheal:GREEN\]'` shows every auto-applied commit.
- The kill-switch (`CHL_READ_ONLY=true` or
  `CHL_CRONS_PAUSED=true`) pauses the supervisor's writes.

## Rollback path

If Phase 2 misbehaves after activation:

```powershell
# Disable the feature flag.
C:/CHL/scripts/set_env_var.ps1 -Key CHL_SELF_HEAL_PHASE2_ENABLED -Value "false"

# Restart the service to pick up the flag.
nssm restart chl-backend
```

The supervisor loop continues to run but `apply_green_patch_isolated`
short-circuits at the feature-flag check.

To revert specific auto-applied commits:

```bash
# List recent self-applied commits.
git log --oneline --grep='\[selfheal:GREEN\]'

# Revert a specific one.
git revert <commit-hash>
```

For a full Phase 2 rollback, re-comment the CRON 11 block in
`cron_scheduler.py` and restart. The collection `db.self_heal_actions`
is left intact -- no rollback path needs to delete prior actions.

## Test strategy

- **`test_patch_classifier.py`** (8 tests):
  - `test_test_fixture_change_classified_GREEN`
  - `test_log_level_adjustment_classified_GREEN`
  - `test_dependency_pin_minor_bump_classified_GREEN`
  - `test_auth_path_change_classified_RED`
  - `test_db_payments_keyword_classified_RED`
  - `test_schema_migration_classified_RED`
  - `test_low_confidence_auto_promotes_to_YELLOW`
  - `test_self_healing_module_change_classified_RED`
    (recursive-modification protection)

- **`test_auto_merge_harness.py`** (4 tests):
  - `test_harness_aborts_on_test_failure_in_isolated_worktree`
  - `test_harness_commits_with_selfheal_GREEN_prefix`
  - `test_harness_logs_self_heal_action_on_success`
  - `test_harness_honors_phase2_disabled_flag`

All 12 PASS in 0.5s on Python 3.14.4 / pytest-9.0.3. Combined with the
Phase 1 self-heal suite (10 tests): **22/22 PASS in 0.79s**.

Preflight clean: `175 routers + 18 crons + 5 health-registers verified`
(unchanged from Phase 1 -- this stream adds NO new routers/crons; the
auto-merge supervisor cron is COMMENTED OUT).

Tests use `subprocess.run` mocking via `monkeypatch.setattr` on
`auto_merge_harness._run_git` and `_run_pytest`, plus `tmp_path` for
the fake repo root. NO real git operations execute during the test
suite.

## Phase 3 / 4 follow-ups (priority-ordered)

The advisory's 4-phase plan continues:

1. **Phase 3 -- Tier C embedded LLM reasoning loop** (2 sprints).
   Continuously-running NSSM service that subscribes to event streams
   (Mongo change-streams or polling), maintains a working-memory ring
   buffer of degraded modules, reasons cross-source, issues actions.
   New budget category `llm_self_healing_loop` $30/day. Phase 3 will
   write `db.self_heal_actions` rows with `actor='embedded_llm_loop'`
   (already in the enum from Phase 1).

2. **Phase 4 -- Tier B rollback metric watch** (1 sprint). Wraps every
   self-applied change with a `watched_metric` row + 30-min
   auto-rollback window. Schema fields are already in place
   (`metric_watched`, `metric_baseline`, `metric_post_action`,
   `rolled_back_at`). Phase 4 adds the watcher loop that polls these
   rows + runs `git revert` on regression.

3. **Classifier ML refinement** (post-Phase 4, ongoing). The
   regex-based classifier is intentionally conservative; some
   legitimate GREEN-class patches will be misclassified YELLOW
   (false-negative GREEN). After 2-4 weeks of Phase 2 operation, audit
   the YELLOW pool for systematic patterns the classifier should learn
   to recognize. Promote those rules to the regex set. NO
   ML-judgment-promotes-RED-to-GREEN -- the trust boundary stays
   regex-based + deterministic.

4. **sub_agent_dispatcher integration**. Wire
   `patch_classifier.classify_patch` into `sub_agent_dispatcher`'s
   write path so every new `db.patch_proposals` row carries the
   GREEN/YELLOW/RED tier alongside the existing LOW/MED/HIGH risk
   level. The auto-merge supervisor already filters on `tier='GREEN'`
   -- when the dispatcher starts populating that field, the supervisor
   has work to do.

## Cross-references

- `chl-memory/research/self_healing_ai_architecture_advisory_2026_05_08.md`
  (MM4 advisory; the design)
- `chl-memory/research/self_healing_phase1_implementation_2026_05_08.md`
  (PP1 -- Phase 1 SCAFFOLD, prerequisite for Phase 2 logging)
- `backend/self_healing/__init__.py`
- `backend/self_healing/patch_classifier.py` (this stream)
- `backend/self_healing/auto_merge_harness.py` (this stream)
- `backend/self_healing/self_heal_log.py` (Phase 1 -- schema for
  `db.self_heal_actions`; Phase 2 starts writing
  `action_taken='patch_applied'` rows)
- `backend/self_healing/sub_agent_dispatcher.py` (Stage 1c -- the
  upstream LLM patch generator; Phase 2's classifier is the gate
  between dispatcher output and harness apply)
- `backend/self_healing/patch_proposal_router.py` (Stage 1d -- where
  operator approves patches)
- `backend/cron_scheduler.py` (CRON 11 commented-out registration)
- `backend/tests/test_patch_classifier.py` (this stream)
- `backend/tests/test_auto_merge_harness.py` (this stream)
- `~/.claude/projects/c--CHL/memory/feedback_set_env_var_helper_and_handover_rules.md`
  (set_env_var.ps1 helper -- used in the activation runbook)
- `~/.claude/projects/c--CHL/memory/feedback_verifier_gate_pattern.md`
  (verifier-gate pattern for batch-parallel orchestration)

End of implementation doc.
