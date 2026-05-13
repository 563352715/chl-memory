# Stage 1b — Remediation Router Architecture (2026-05-13)

**Source:** Plan agent dispatched 2026-05-13 ~05:35Z during continuation session. Plans the missing layer between `anomaly_dispatcher` (queues) and `context_bundler/sub_agent_dispatcher` (LLM-diff path).

## Terminology disambiguation

There's a collision in the original roadmap. Both this doc and `context_bundler.py` are labeled "Stage 1b". Resolution:

- **context_bundler.py** = "Stage 1b-bundle" (existing): gathers git log + sample evidence for LLM-diff-proposing chain (heavy, $/call)
- **NEW remediation_router.py** = "Stage 1b-act" (this doc): deterministic action layer that fires BEFORE/INSTEAD-OF the LLM chain on anomalies that have a known fix

The router decides: deterministic action → execute immediately → mark resolved/needs_human, OR escalate to LLM diff path.

## Decision tree

```
PARSER_FAILURE  / LOW or MED → recheck_one_board (1 retry)
PARSER_FAILURE  / HIGH        → recheck_one_board + sandbox_replay_latest
PARSER_FAILURE  / CRITICAL    → escalate to LLM diff + operator notification
DATA_FRESHNESS  / any         → trigger_refresh_cron (in-process tick)
BACKEND_HEALTH  / LOW or MED  → cron_supervisor.restart_one (existing)
BACKEND_HEALTH  / HIGH        → cron restart + LLM diff path
BACKEND_HEALTH  / CRITICAL    → human-only (AUTH/BILLING/DISPATCH)
CLIENT_ERROR    / LOW         → noop, mark resolved
CLIENT_ERROR    / MED         → LLM diff path
CLIENT_ERROR    / HIGH        → LLM diff + operator notification
```

HIGH_SEV_CATEGORIES (AUTH/BILLING/DISPATCH per existing `anomaly_dispatcher._HIGH_SEV_CATEGORIES`) get hard short-circuit to human-only regardless.

## Action library

**SAFE (autonomous, single-call, idempotent)**:
- `recheck_one_board(key)` — calls `loadboard_health_monitor._check_one_adapter` + `_persist_one`
- `sandbox_replay_latest(source_name)` — `payload_replay_sandbox.sandbox_replay_all`
- `trigger_refresh_cron(module_name)` — imports + calls `run_*_tick()`
- `restart_cron(name)` — `cron_supervisor.supervise_crons` single-target
- `mark_resolved(anomaly_id, reason)` — close queue row with audit
- `noop(anomaly_id)` — observability-only

**REQUIRES_OWNER**:
- Actions touching HIGH_SEV_CATEGORIES
- &gt;3 boards in &lt;1h (storm-mode guard)
- Sandbox replay on credentialed-adapter payloads (plivo/twilio/stripe regex match)
- Code patches (delegate to context_bundler chain)

**NEVER autonomous (architectural floor)**:
- Mutations to pricing/booking gates (`pnl_monitor`, `margin_floor`, `compute_load_pnl`)
- Env var changes
- Secret rotations
- DB writes outside `anomaly_dispatch_queue` / `self_heal_actions` / `parser_failure_payloads.replays`

## Status lifecycle (extend existing enum)

```
queued (existing)
  → pending_remediation     (router picked up, deciding)
    → remediation_executing (action firing)
      → resolved            (success; audit in queue row)
      → needs_human         (failed or escalated)
    → dispatched            (escalated to LLM chain; context_bundler picks up)
cooldown (existing, unchanged)
```

New status: `needs_human`. Writes accompanying row to `db.urgent_alerts`.

Audit field added to every queue row: `remediation_history: [{at, action, outcome, actor, details}, ...]`

## Cost guards

- **Per-tick batch cap**: 10 anomalies/tick
- **Per-action-per-hour cap**: max 3 `recheck_one_board(same_key)` calls/hour (TTL collection `remediation_action_rate_limits`)
- **Storm-mode trip**: &gt;20 queued rows in one tick → autonomous-continue OFF for category; routes to `needs_human` until next tick
- **Daily USD cap**: inherited from `sub_agent_dispatcher.BUDGET_DAILY_USD` ($15/day)
- **Per-action wall-clock**: 60s, kill + mark `needs_human` on exceed

## Phased implementation plan

### Phase 1 — Scaffold + dry-run (FIRST tractable, ~2h)

**Files:**
- `backend/self_healing/remediation_router.py` (NEW)
  - `init(db)`, `decide_action(queue_row)` (pure fn, decision tree)
  - `execute_action(db, anomaly_id, decision, dry_run=True)` — defaults DRY-RUN
  - `route_one_anomaly`, `route_pending_anomalies`
  - `@track_sla("remediation_router", target_ms=30_000)`
  - Health-check + `register_remediation_router_health(register_health)`
- `backend/self_healing/self_heal_log.py` — extend `VALID_ACTIONS` to include 5 new action names
- `backend/tests/test_remediation_router.py` (NEW)
  - Decision tree as pure fn (no DB)
  - Dry-run writes audit but doesn't mutate
  - Cost guards trip correctly

### Phase 2 — Live deterministic actions (parser + freshness)

- 4 SAFE action handlers
- `backend/self_healing/remediation_router_cron.py` (NEW)
- Wire `init` in server.py
- Env flag `CHL_REMEDIATION_ROUTER_LIVE_PARSER_LOW=true` first

### Phase 3 — LLM escalation hand-off

- `context_bundler.py` pickup query extend to include `escalated_after_deterministic_failure=true`
- `_schedule_post_action_recheck` with settle window
- Re-enqueue logic

### Phase 4 — Human-escalation + storm-mode

- `db.urgent_alerts` writes
- 20-rows-per-tick trip wire
- `anomaly_queue_admin_api.py` surfaces `needs_human` separately

### Phase 5 — Dashboard wiring + manual escalation

- `POST /api/admin/anomaly-queue/{id}/escalate-llm`
- `POST /api/admin/anomaly-queue/{id}/mark-resolved`
- `public_site/admin/index.html` two buttons per row

### Phase 6 — pnl_monitor floor enforcement

- `_HARDBLOCK_MODULE_PATTERNS` mirroring `sub_agent_dispatcher._DEFAULT_HIGH_RISK_PATTERNS`
- `decide_action` asserts: any param matching these patterns forces `ACTION_ESCALATE_HUMAN`
- `test_remediation_router_pnl_floor.py` proves no decision path returns a deterministic action against pnl/booking/factor/mercury/stripe

## Operator decisions blocking Phase 1

1. Confirm naming: `remediation_router.py` vs renaming existing `context_bundler` to `stage_1b_bundle.py`? (Recommend keep current name to avoid breaking existing imports)
2. Phase 1 ships dry-run only? (Recommended)
3. Extending `self_heal_log.VALID_ACTIONS` is acceptable? (Additive only, low risk)

If all three default-yes, Phase 1 is fully unblocked.

## Critical files referenced

- `backend/self_healing/anomaly_dispatcher.py`
- `backend/self_healing/sub_agent_dispatcher.py`
- `backend/self_healing/self_heal_log.py`
- `backend/loadboard_health_api.py`
- `backend/payload_replay_sandbox.py`
- `backend/cron_supervisor.py`
