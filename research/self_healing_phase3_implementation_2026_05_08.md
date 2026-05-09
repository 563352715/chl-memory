# Self-Healing Phase 3 SCAFFOLD -- Implementation (RR3, 2026-05-08 EOD)

## Context

Phase 3 of the MM4 self-healing advisory at
`chl-memory/research/self_healing_ai_architecture_advisory_2026_05_08.md`
section 4 Tier C + section 6 Phase 3: a continuously-running LLM
reasoning loop that subscribes to anomaly + cron-failure + URGENT-email
+ new-patch-proposal streams and routes recommendations by tier and
confidence into one of four downstream queues.

Phase 1 (PP1, ab2ea6a) shipped the cron self-resurrection supervisor +
`db.self_heal_actions` log + frontend Self-Heal tab. Phase 2 (QQ2,
d0263c9) shipped the deterministic patch_classifier + worktree-isolated
auto_merge_harness. Phase 3 ships the cross-source LLM reasoner that
sits ON TOP of those two surfaces -- the LLM produces recommendations
but the deterministic classifier remains the trust boundary, and the
auto_merge_harness remains the only thing that ever applies a patch to
disk.

This stream is SCAFFOLD only:
- Module is fully implemented + 10/10 unit-tested (8 spec-required +
  2 bonus pure-function tests).
- Cron registration in `cron_scheduler.py` is COMMENTED OUT.
- No real Anthropic/OpenAI API call is made anywhere. Tests inject a
  fake httpx-style client via `http_client_factory`.
- Activation requires BOTH `CHL_SELF_HEAL_PHASE3_ENABLED=true` AND
  `CHL_SELF_HEAL_LLM_DAILY_CAP_USD` set, plus uncommenting the CRON 12
  block, plus an NSSM service restart.

## What shipped

### Backend modules (new)

| File                                                | Lines | Purpose                                                                        |
| --------------------------------------------------- | ----- | ------------------------------------------------------------------------------ |
| `backend/self_healing/llm_reasoning_loop.py`        | ~720  | Continuously-running LLM reasoning loop with 4-stream subscription + routing.  |
| `backend/self_healing/__init__.py`                  | +20   | Export the new module.                                                         |

### Backend tests (new)

| File                                              | Lines | Tests | Status |
| ------------------------------------------------- | ----- | ----- | ------ |
| `backend/tests/test_llm_reasoning_loop.py`        | ~510  | 10    | PASS   |

8 spec-required tests:

1. `test_tick_skips_when_phase3_disabled`
2. `test_tick_pulls_pending_events_from_4_streams`
3. `test_route_action_GREEN_high_confidence_goes_to_auto_merge`
4. `test_route_action_YELLOW_goes_to_dev_review_queue`
5. `test_route_action_RED_goes_to_urgent_alerts`
6. `test_budget_exhaustion_degrades_to_read_only_reasoning`
7. `test_context_bundle_caps_at_8000_tokens`
8. `test_tick_honors_should_skip_tick`

2 bonus pure-function tests:

9. `test_route_action_matrix_pure` -- exhaustive tier x confidence
   matrix.
10. `test_recursion_guard_forces_red_for_self_heal_paths` -- defense-in-
    depth against the LLM recommending a self-heal-touching change.

Combined Phase 1 + Phase 2 + Phase 3 self-heal suite: **32/32 PASS** in
0.76s on Python 3.14.4 / pytest-9.0.3 (Phase 1 = 10, Phase 2 = 12,
Phase 3 = 10).

### Backend wiring

| File                                | Change                                                                                |
| ----------------------------------- | ------------------------------------------------------------------------------------- |
| `backend/cron_scheduler.py`         | +56 lines -- COMMENTED OUT CRON 12 (LLM reasoning loop) registration block.           |

### Doc (this file)

| File                                                                       | Lines |
| -------------------------------------------------------------------------- | ----- |
| `chl-memory/research/self_healing_phase3_implementation_2026_05_08.md`     | ~450  |

## Subscribed event streams (4 sources)

The loop polls four Mongo collections every 60s. Mongo change-streams
are a future-iter improvement; poll-mode keeps Phase 3's ops surface
identical to Phase 1+2.

### Stream 1 -- `db.anomalies`

- Filter: `{"reasoning_status": "pending"}`
- Source: existing anomaly_dispatcher pipeline (Phase 7 / iter 145.1).
- Trigger kind in self_heal_log: `anomaly`.

### Stream 2 -- `db.cron_health_events`

- Filter: `{"event_type": "failed", "reasoning_status": {"$nin":
  ["reasoned", "skipped"]}}`
- Source: `system_health_monitor.py` per-cron transition events.
- Trigger kind in self_heal_log: `cron_failure`.

### Stream 3 -- `db.email_triage_log`

- Filter: `{"verdict": "URGENT", "reasoning_status": {"$nin":
  ["reasoned", "skipped"]}}`
- Source: `inbox_triage/triage_classifier.py` URGENT classifications.
- Trigger kind in self_heal_log: `manual_test` (closest semantic in the
  Phase 1 enum -- Phase 4 will extend the enum).

### Stream 4 -- `db.patch_proposals`

- Filter (OR-style):
  - `{"review_status": "pending_high", "tier": "YELLOW"}` -- new
    YELLOW proposals awaiting dev-Claude review
  - `{"review_status": "approved", "tier": "GREEN", "auto_merge_status":
    {"$in": [None, "pending"]}}` -- approved GREEN patches not yet
    merged by Phase 2's auto_merge_supervisor
- Trigger kind in self_heal_log: `patch_proposal_approved`.

Per-tick caps:
- `MAX_EVENTS_PER_STREAM = 5` per stream
- `MAX_EVENTS_PER_TICK = 12` total
- After-tick: each processed event row gets `reasoning_status` flipped
  to `reasoned` or `skipped` so it's not re-processed next tick.

## Routing logic

Pure function `_route_action(recommendation: dict) -> str`:

| Tier   | Confidence         | Route               |
| ------ | ------------------ | ------------------- |
| RED    | (any)              | `urgent_alert`      |
| (any)  | < 0.30 (floor)     | `read_only_log`     |
| YELLOW | >= 0.30            | `dev_review_queue`  |
| GREEN  | >= 0.85            | `auto_merge`        |
| GREEN  | 0.30 - 0.85        | `dev_review_queue`  |
| unknown tier | (any)        | `read_only_log`     |

Constants:
- `HIGH_CONFIDENCE_THRESHOLD = 0.85` (GREEN auto-merge gate).
- `MIN_CONFIDENCE_FLOOR = 0.30` (below this we don't trust the call).

Handlers:
- `auto_merge` -> writes `phase3_llm_endorsement` onto the
  `db.patch_proposals` row + flips `auto_merge_status` to `pending`.
  The actual merge happens via the Phase 2 supervisor + harness; Phase
  3 is read-only-with-respect-to-disk.
- `dev_review_queue` -> inserts a `db.dev_review_queue` row for the
  human-Claude review queue.
- `urgent_alert` -> inserts a `db.urgent_alerts` row with severity
  HIGH + source `llm_reasoning_loop`.
- `read_only_log` -> writes a `db.self_heal_actions` row with
  `action_taken='noop'` + `actor='embedded_llm_loop'` capturing the
  recommendation without taking action.

## Context bundling

`_build_context_bundle(*, db, event_kind, event_id) -> dict` produces:

```python
{
  "event_kind": str,
  "event_id": str,
  "event": dict | None,        # the source row
  "similar": list[dict],        # up to 5 similar past events
  "truncated": bool,            # True if we hit the cap
  "char_count": int,            # serialized payload size
}
```

Token cap: `CONTEXT_TOKEN_CAP = 8000`. Conservative
char-per-token estimate of 4 chars/token gives `CONTEXT_CHAR_CAP =
32000`. The bundle is serialized via `json.dumps(default=str,
sort_keys=True)`. If the serialized payload exceeds the cap, the
bundler:

1. Drops `similar` to its first 2 elements.
2. Re-serializes; if still over cap, hard-caps the serialized text and
   replaces the bundle with `{event_kind, event_id, event_summary,
   truncated: True}`.

This keeps the LLM context bounded even when an event row carries a
huge debugging payload (e.g., anomaly detail blobs > 100 KB).

## LLM call pattern

`_call_llm_for_recommendation(*, context, http_client_factory) -> dict`

The factory is the only network surface. Tests inject a fake; production
will inject a closure that returns a singleton `httpx.AsyncClient` with
`Authorization: Bearer <ANTHROPIC_API_KEY>`. The wiring of the real
client is DEFERRED to post-activation -- the operator wires the API key
via `set_env_var.ps1` + a wrapper module imports it.

Default behavior when factory is None:
- Returns a YELLOW "no_http_client_factory_provided" recommendation
  with confidence 0.5.
- This means the loop runs in stub mode and routes everything to
  `dev_review_queue` until the operator wires the real factory. SAFE
  default -- never auto-merges in stub mode (YELLOW < HIGH_CONFIDENCE).

Anthropic-style request body (sent to
`https://api.anthropic.com/v1/messages`):

```json
{
  "model": "claude-opus-4-7",
  "max_tokens": 1024,
  "messages": [{"role": "user", "content": "<prompt>"}]
}
```

The prompt instructs the model to reply with strict JSON:

```json
{
  "tier": "GREEN|YELLOW|RED",
  "action": "<short human-readable>",
  "confidence": 0.0 - 1.0,
  "reasoning": ["..."],
  "affected_files": ["<repo-relative paths>"]
}
```

Response parsing in `_parse_llm_response` is tolerant: code fences are
stripped, missing fields default to YELLOW + 0.5 confidence + empty
reasoning. The LLM cannot crash the loop -- worst case the
recommendation drops into the YELLOW dev-review path.

## Budget enforcement

Single budget category: `BUDGET_CATEGORY = "llm_self_healing_loop"`.

This category is intentionally NOT in
`integrity/budget_caps._DEFAULT_CAPS_USD` -- it's gated by the env var
`CHL_SELF_HEAL_LLM_DAILY_CAP_USD` (default $30/day per the MM4 advisory
section 5.3). Reasoning: the budget surface is "off by default" until
operator activates Phase 3, mirroring how the cron registration is off
by default.

Enforcement points:

1. `_check_budget(db)` -- runs BEFORE every LLM call. Returns False if
   today's spend + per-call estimate would exceed the cap.
2. `_record_spend(db)` -- runs AFTER every LLM call. Increments the
   `db.budget_ledger` for today's UTC bucket. Best-effort; never raises.
3. `_budget_remaining(db)` -- read-only surface for the per-tick
   summary + the health endpoint.

Per-call estimate: `ESTIMATED_LLM_USD_PER_CALL = $0.05`. Calibrated
conservatively against `sub_agent_dispatcher`'s observed gpt-4o cost.
Real spend will be measured + recorded by the production wrapper once
the LLM client is wired.

### Budget exhaustion behavior

When `_check_budget` returns False:

- The LLM is NOT called.
- A `db.self_heal_actions` row is written with:
  - `action_taken='noop'`
  - `actor='embedded_llm_loop'`
  - `tier='YELLOW'`
  - `outcome='success'`
  - `outcome_reason='budget_exhausted_read_only'`
  - `before_state.event = <the event doc>`
- The source event is marked `reasoning_status='skipped'` (NOT
  `reasoned` -- we deferred, the next budget cycle should re-process).
- `summary["budget_exhausted"] = True` so the supervisor health surface
  + frontend tab can flag the degraded state.

This is the "read-only-reasoning" mode from the advisory section 5.3
mitigation B. The platform retains visibility (every event is logged)
without burning more LLM spend.

## Recursion guard implementation

Defense-in-depth on top of `patch_classifier`'s RED list. Every
recommendation passes through `_apply_recursion_guard` BEFORE routing.

```python
RECURSION_GUARD_PATTERNS = (
    re.compile(r"(^|/)backend/self_healing/"),
    re.compile(r"(^|/)backend/integrity/"),
    re.compile(r"(^|/)backend/cron_scheduler\.py$"),
    re.compile(r"(^|/)backend/health_monitor\.py$"),
    re.compile(r"(^|/)backend/sla_monitor\.py$"),
)
```

If any of `recommendation["affected_files"]` matches any pattern, the
recommendation's tier is FORCED to RED regardless of what the LLM
returned. The reasoning list is updated to record the override:

```python
"recursion_guard: forced tier RED (was 'GREEN'); recommendation
 touches self-heal infrastructure path"
```

This is the first of three layers preventing the system from modifying
itself:

1. (THIS LAYER) `llm_reasoning_loop._apply_recursion_guard` -- forces
   RED at routing time.
2. `patch_classifier.classify_patch` -- regex-based RED gate on diff
   files (Phase 2; layer 2 if a hand-authored patch ever bypasses
   layer 1).
3. `auto_merge_harness.apply_green_patch_isolated` -- re-classifies
   defensively before applying; bails on non-GREEN (Phase 2; layer 3
   if both upstream layers are bypassed).

The recursion guard test (`test_recursion_guard_forces_red_for_self_heal_paths`)
verifies that an LLM returning `{"tier": "GREEN", "confidence": 0.99,
"affected_files": ["backend/self_healing/llm_reasoning_loop.py"]}`
gets routed to `urgent_alert` not `auto_merge`.

## Activation runbook (4 commands)

When operator green-lights Phase 3:

```powershell
# 1. Set the feature flag (uses the standing-directive helper).
C:/CHL/scripts/set_env_var.ps1 -Key CHL_SELF_HEAL_PHASE3_ENABLED -Value "true"

# 2. Set the daily LLM budget cap (REQUIRED -- registration won't fire
#    without it).
C:/CHL/scripts/set_env_var.ps1 -Key CHL_SELF_HEAL_LLM_DAILY_CAP_USD -Value "30"

# 3. Uncomment the CRON 12 supervisor cron block in cron_scheduler.py.
#    Look for "CRON 12 -- Self-Heal Phase 3 LLM reasoning loop" and
#    remove the leading "# " from these 7 lines:
#    - if (
#    -     os.environ.get("CHL_SELF_HEAL_PHASE3_ENABLED", "").lower() == "true"
#    -     and os.environ.get("CHL_SELF_HEAL_LLM_DAILY_CAP_USD", "").strip()
#    - ):
#    -     from self_healing import llm_reasoning_loop as _llm_reasoning_loop
#    -     asyncio.create_task(_llm_reasoning_loop.llm_reasoning_loop_supervisor(db))
#    -     logger.info("cron_scheduler: llm_reasoning_loop_supervisor armed (Phase 3).")

# 4. Restart the NSSM "chl-backend" service to pick up the change.
nssm restart chl-backend
```

After step 4:

- The supervisor runs every 60s (10-min cold-start delay).
- The supervisor calls `reasoning_tick(db, http_client_factory=...)`
  -- the operator must wire `http_client_factory` to a real
  `httpx.AsyncClient` factory at the same time the registration is
  uncommented (a follow-up stream will land the production wrapper).
- Activity is visible in:
  - The frontend Self-Heal tab (Phase 1 surface) -- shows the noop
    rows written when a recommendation routed to `read_only_log`.
  - `GET /api/_self_heal/recent?action_taken=noop&actor=embedded_llm_loop`
    -- Phase 1 read API.
  - `db.dev_review_queue` -- for human-Claude follow-up.
  - `db.urgent_alerts` -- for operator escalations.

## Post-activation observability via SelfHealView

The Phase 1 frontend Self-Heal tab already renders rows from
`db.self_heal_actions`. Phase 3 starts writing rows where:

- `actor = "embedded_llm_loop"` (existing enum value from Phase 1)
- `action_taken = "noop"` for read-only-log + budget-exhausted paths
- `tier` = whatever the LLM recommended (after recursion-guard override)

Filter pills available on the tab:
- Action: `noop` -> isolates LLM-loop activity
- Actor: `embedded_llm_loop` -> isolates LLM-loop activity (more
  precise -- excludes Phase 1 cron-supervisor noop rows)

Header tile #4 (`supervisor status`) reads
`llm_reasoning_loop.health()` once Phase 3 is wired into the
health-monitor framework (post-activation; trivial follow-up).

Two future surfaces to add (not in this stream):

1. New tab "LLM Reasoning" surfacing
   `db.dev_review_queue` rows with approve/reject buttons.
2. New tab "Urgent Alerts" surfacing `db.urgent_alerts` rows with ack
   button + 24h ack-deadline.

## Rollback path

If Phase 3 misbehaves after activation:

```powershell
# Disable the feature flag.
C:/CHL/scripts/set_env_var.ps1 -Key CHL_SELF_HEAL_PHASE3_ENABLED -Value "false"

# Restart the service to pick up the flag.
nssm restart chl-backend
```

The supervisor loop continues to run but `reasoning_tick` short-circuits
at the feature-flag check + returns
`{feature_enabled: False, events_seen: 0, ...}`.

To revert specific endorsements written to `db.patch_proposals`:

```javascript
// In the Mongo shell:
db.patch_proposals.updateMany(
  {"phase3_llm_endorsement": {"$exists": true}},
  {"$unset": {"phase3_llm_endorsement": "", "auto_merge_status": ""}}
)
```

To clear queued dev-review rows:

```javascript
db.dev_review_queue.deleteMany({"review_status": "pending"})
```

For a full Phase 3 rollback, re-comment the CRON 12 block in
`cron_scheduler.py` and restart. The collections
`db.self_heal_actions`, `db.dev_review_queue`, `db.urgent_alerts` are
left intact -- no rollback path needs to delete prior rows.

## Hard guardrails (Phase 3 v1)

The reasoning loop will NEVER:

- Apply a patch to disk directly. Every "GREEN auto-merge" route is a
  HANDOFF to the Phase 2 `auto_merge_harness`, which has its own
  feature flag + worktree isolation + targeted-pytest verification.
- Modify backend/self_healing/, backend/integrity/, cron_scheduler.py,
  health_monitor.py, or sla_monitor.py recommendations. The recursion
  guard forces RED for any recommendation touching these paths --
  RED routes to `urgent_alert`, never `auto_merge`.
- Run when `CHL_SELF_HEAL_PHASE3_ENABLED` is unset or != "true".
- Run when `CHL_CRONS_PAUSED=true` or `CHL_READ_ONLY=true` (both
  trigger `should_skip_tick()`).
- Spend more than `CHL_SELF_HEAL_LLM_DAILY_CAP_USD` per UTC day.
- Make a real LLM API call during unit tests. The
  `http_client_factory` injection point ensures tests pass a fake
  client; the production wiring is a separate post-activation stream.

## Connection to existing patch-proposal flow

The existing patch_proposal pipeline (Stages 1a-1d, sub_agent_dispatcher,
patch_proposal_router, Phase 2 auto_merge_supervisor) is unchanged:

- Stage 1a (anomaly_dispatcher) writes `queued` rows to
  `db.anomaly_dispatch_queue`.
- Stage 1b (context_bundler) advances rows to `bundled`.
- Stage 1c (sub_agent_dispatcher) generates the patch + writes
  `db.patch_proposals` with a trust-gate (LOW/MED/HIGH).
- Stage 1d (patch_proposal_router) exposes
  `POST /api/_self_healing/patch_proposals/{id}/approve` -- operator
  flips `review_status='approved'` here.
- Phase 2 auto_merge_supervisor polls for `approved + GREEN` rows +
  runs them through the worktree harness.

Phase 3 ADDS a parallel "endorsement" surface on top of the existing
flow:

- The reasoning loop polls `db.patch_proposals` for both
  `pending_high + YELLOW` (new) AND `approved + GREEN + not yet
  auto-merged` (already in Phase 2's queue).
- For YELLOW proposals: writes a `db.dev_review_queue` row with the
  LLM's reasoning -- gives the human-Claude reviewer a head-start.
- For approved GREEN proposals: writes a `phase3_llm_endorsement` field
  on the proposal row + flips `auto_merge_status` to `pending` if
  unset. Phase 2's supervisor then runs the harness as before.

The two systems are loosely-coupled by design: Phase 2 keeps the disk-
writing authority, Phase 3 contributes reasoning + cross-source
context.

## Test strategy

Tests run against an in-memory async-Mongo stub mirroring
`test_self_heal_log` + `test_auto_merge_harness`. The stub adds `$or`,
`$nin`, `$in` query operators (for the patch_proposals OR-filter +
the cron_health_events / email_triage_log $nin filter).

LLM calls are mocked via a fake `http_client_factory` that returns a
fake httpx-style client. Each fake client is canned with a single
recommendation; the test asserts the routing decision + downstream
side-effects.

The 8 spec-required tests cover:

1. **Feature-flag gate** -- environment unset -> tick is a no-op.
2. **4-stream pull** -- seed one event per stream + assert all 4 are
   pulled + reasoned.
3. **GREEN + high conf -> auto_merge** -- assert the patch_proposals
   row gets `phase3_llm_endorsement` + `auto_merge_status='pending'`.
4. **YELLOW -> dev_review_queue** -- assert one `db.dev_review_queue`
   row written.
5. **RED -> urgent_alerts** -- assert one `db.urgent_alerts` row
   written with severity HIGH.
6. **Budget exhaustion -> read-only-reasoning** -- monkey-patch
   `_check_budget` to return False; assert NO LLM call, but a
   `db.self_heal_actions` noop row IS written.
7. **Context cap at 8000 tokens** -- seed an event with a 100KB payload
   + 8 similar rows; assert `bundle["char_count"] <=
   CONTEXT_CHAR_CAP` + `bundle["truncated"] is True`.
8. **Kill-switch honored** -- monkeypatch `CHL_CRONS_PAUSED=true`;
   assert tick returns with `skipped_kill_switch: True` + zero events
   processed.

The 2 bonus tests:

9. **Routing matrix** -- exhaustive table of (tier, confidence) ->
   route assertions.
10. **Recursion guard** -- LLM returning GREEN-with-high-confidence on
    a self-healing path file gets forced to RED (urgent_alert route).

All 10 PASS in 0.44s. Combined Phase 1+2+3 self-heal regression suite:
**32/32 PASS** in 0.76s.

Preflight: `20 issues found` -- all 20 are pre-existing missing-package
import failures (`reportlab`, `openai`, `twilio`, `stripe`,
`cryptography`, `email-validator`, `psutil`). NONE reference
`llm_reasoning_loop` or any Phase 3 surface.

## Phase 4 follow-up (priority-ordered)

The advisory's 4-phase plan ends with:

1. **Phase 4 -- Tier B rollback metric watch** (1 sprint). Wraps every
   self-applied change with a `watched_metric` row + 30-min
   auto-rollback window. Schema fields are already in place
   (`metric_watched`, `metric_baseline`, `metric_post_action`,
   `rolled_back_at`). Phase 4 adds the watcher loop that polls these
   rows + runs `git revert` on regression. Phase 3's
   `phase3_llm_endorsement` field on patch_proposals is a natural
   landing spot for the watched-metric specification (the LLM names
   the metric to watch alongside the action recommendation).

2. **Real Anthropic client wrapper** (next stream after Phase 3
   activation). Lands a `backend/self_healing/_llm_client_anthropic.py`
   module that exposes `make_factory() -> Callable[[], httpx.AsyncClient]`
   reading `ANTHROPIC_API_KEY` from env. The cron-scheduler registration
   wires it in. NO change to the reasoning loop's interface --
   `http_client_factory` is the only injection point.

3. **dev_review_queue frontend tab** (post-Phase-3 activation). Surface
   the LLM's recommendations with approve/reject buttons. Owner-gated.

4. **urgent_alerts ack flow + AgentDM bridge** (post-Phase-3
   activation). When `urgent_alerts.severity='HIGH'` is written, the
   alerter cron DMs `@operator` via AgentDM -- closes the loop on the
   "AI changed something and I don't know" Mitigation 5.4 from the
   advisory.

5. **Mongo change-streams** (post-Phase-4). Replace poll-mode with
   change-streams so latency drops from 60s to sub-second. Only
   feasible if the deployed Mongo replica-set is configured for it.

## Cross-references

- `chl-memory/research/self_healing_ai_architecture_advisory_2026_05_08.md`
  (MM4 advisory; the design)
- `chl-memory/research/self_healing_phase1_implementation_2026_05_08.md`
  (PP1 -- Phase 1 SCAFFOLD: cron supervisor + self_heal_log + frontend
  tab; Phase 3 writes `actor='embedded_llm_loop'` rows into the same
  log)
- `chl-memory/research/self_healing_phase2_implementation_2026_05_08.md`
  (QQ2 -- Phase 2 SCAFFOLD: patch_classifier + auto_merge_harness;
  Phase 3's GREEN+high-confidence route hands off to Phase 2's
  supervisor)
- `backend/self_healing/__init__.py`
- `backend/self_healing/llm_reasoning_loop.py` (this stream)
- `backend/self_healing/patch_classifier.py` (Phase 2 -- Phase 3
  consumes the same TIER_GREEN/TIER_YELLOW/TIER_RED constants)
- `backend/self_healing/auto_merge_harness.py` (Phase 2 -- Phase 3's
  GREEN auto_merge route hands off here)
- `backend/self_healing/self_heal_log.py` (Phase 1 -- Phase 3 writes
  noop rows for read-only-log + budget-exhausted paths)
- `backend/integrity/budget_caps.py` (Phase 3 reads `today_spend` +
  records spend under category `llm_self_healing_loop`)
- `backend/integrity/operational_state.py` (Phase 3 honors
  `should_skip_tick` for the kill-switch)
- `backend/cron_scheduler.py` (CRON 12 commented-out registration)
- `backend/tests/test_llm_reasoning_loop.py` (this stream)
- `~/.claude/projects/c--CHL/memory/feedback_set_env_var_helper_and_handover_rules.md`
  (set_env_var.ps1 helper -- used in the 4-command activation runbook)
- `~/.claude/projects/c--CHL/memory/feedback_verifier_gate_pattern.md`
  (verifier-gate pattern for batch-parallel orchestration)

---

## Visibility Surfaces (SS3 closure -- 2026-05-08 EOD)

Stream SS3 ships the operator-facing surfaces over the two persisted
queues that Phase 3's `llm_reasoning_loop` writes to. Without these
surfaces, `db.dev_review_queue` (YELLOW-tier handoffs) and
`db.urgent_alerts` (RED-tier escalations) are write-only graveyards.

### Backend -- `backend/self_healing/dev_review_alerts_router.py`

New sub-router mounted alongside `self_heal_router.py` under the same
`/api/_self_heal` prefix. All endpoints `require_owner`.

  GET  `/api/_self_heal/dev_review_queue/pending?since_hours=72&limit=100`
       Returns pending YELLOW-tier reviews newest-first with by-tier
       breakdown. since_hours window default 72h, max 720h.

  POST `/api/_self_heal/dev_review_queue/{review_id}/approve`
       Body: `{"notes": "..."}` (optional)
       Flips `review_status='approved'` + stamps `approved_at` /
       `approved_by`. For `event_kind=patch_proposal` events, performs
       a best-effort handoff to Phase 2's
       `auto_merge_harness.apply_green_patch_isolated`. ALWAYS logs a
       `db.self_heal_actions` row with `actor='operator_override'` +
       `outcome_reason` reflecting whether the Phase 2 handoff
       succeeded. Returns 409 if the row is already approved/rejected.

  POST `/api/_self_heal/dev_review_queue/{review_id}/reject`
       Body: `{"notes": "..."}` (optional)
       Flips `review_status='rejected'` + stamps `rejected_at` /
       `rejected_by`. Logs a `db.self_heal_actions` noop row with
       `outcome_reason='operator_rejected'`. Returns 409 if not pending.

  GET  `/api/_self_heal/urgent_alerts/active?since_hours=168&source=&limit=100`
       Returns unacked alerts newest-first with by-source breakdown.
       Optional `source` filter (one of `triage`, `self_heal`, `inbox`,
       `llm_reasoning_loop`). Default since_hours=168h (7d), max 720h.

  POST `/api/_self_heal/urgent_alerts/{alert_id}/ack`
       Body: `{"notes": "..."}` (optional)
       Stamps `ack_status='acked'` + `acked_at` + `acked_by` +
       `ack_notes`. Idempotent -- re-acking returns 200 with
       `already_acked=true`.

The router is `require_owner`-only (no broker access). Mounted in
`backend/server.py` immediately after `self_heal_router.py` -- safe to
mount unconditionally because the underlying collections are written
by the Phase 3 loop only when its feature flag is set.

### Frontend -- `frontend/src/App.js`

Two new owner-only tabs adjacent to the existing Self-Heal tab:

  1. **Reviews tab** (id: `dev_reviews`, label: "Reviews", icon:
     `ListChecks`)
     - `DevReviewQueueView` component.
     - Header tiles: pending count + by-tier breakdown.
     - Sortable table: Trigger | Recommendation | Tier | Confidence |
       Reasoning preview | Created | Actions.
     - Drill-down on row click: full reasoning text + affected_files
       list + patch_id pointer (read-only `<pre className="whitespace-pre-wrap font-mono text-xs">`).
     - Decision UI: notes textarea + Approve / Reject buttons.
     - Auto-refresh every 60s.

  2. **Alerts tab** (id: `urgent_alerts`, label: "Alerts", icon:
     `Warning`)
     - `UrgentAlertsView` component.
     - Header tiles: unacked count + by-source breakdown.
     - Filter pills: All / Triage / Self-Heal / Inbox.
     - Sortable table: Source | Subject | Severity | Created | Actions.
     - Drill-down: full alert payload pretty-printed.
     - Acknowledge UI: notes textarea + Acknowledge button.
     - Auto-refresh every 30s.

Both tabs are gated behind `currentUser?.is_owner` in the nav array
construction and the conditional render block. NO new frontend
dependencies (no syntax-highlight library; diff preview pointer uses
`<pre>` only).

### Tests -- `backend/tests/test_dev_review_and_alerts_router.py`

Six tests, all using the `_FakeDB` async-Mongo stub pattern:

  - `test_list_dev_review_queue_filters_by_since` -- since_hours
    window + limit + by-tier breakdown.
  - `test_approve_dev_review_hands_off_to_phase2_harness` -- stubs
    `auto_merge_harness.apply_green_patch_isolated`; verifies handoff
    captured + `self_heal_actions` row logged with
    `action_taken='patch_applied'` when handoff succeeded.
  - `test_reject_dev_review_logs_self_heal_action` -- noop row +
    `outcome_reason='operator_rejected'`.
  - `test_list_urgent_alerts_filters_by_source` -- source filter +
    400 on bad source + acked rows excluded from active list.
  - `test_ack_urgent_alert_stamps_acked_at` -- field stamping +
    idempotent re-ack + 404 on missing.
  - `test_routes_require_owner_auth` -- all 5 endpoints 403 when
    `require_owner` raises 403.

Combined Phase 1+2+3+SS3 regression: 27 tests pass (Phase 1 = 7,
Phase 2 = 4, Phase 3 = 10, SS3 = 6).

### Build delta

Frontend production build: +2.47 kB gzipped main bundle (734.94 kB).
No new ESLint warnings on SS3 code.

### Activation

The surfaces are mounted unconditionally and safe-by-default. Until
the Phase 3 loop's feature flag (`CHL_SELF_HEAL_PHASE3_ENABLED=true`)
is set, the underlying collections stay empty and both tabs render
their "no pending / no unacked" empty-state cards.

### Files added / modified (SS3)

- ADD `backend/self_healing/dev_review_alerts_router.py` (~480 lines)
- ADD `backend/tests/test_dev_review_and_alerts_router.py` (~480 lines)
- MOD `backend/server.py` (+9 lines mount block)
- MOD `frontend/src/App.js` (+~530 lines: 2 nav entries, 2 conditional
  renders, 2 view components, 4 helper constants/functions)
- MOD `chl-memory/research/self_healing_phase3_implementation_2026_05_08.md`
  (this section)

End of SS3 closure section. End of implementation doc.
