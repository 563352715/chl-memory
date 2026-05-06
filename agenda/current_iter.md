# Current iter

> This file is the active iter. When an iter closes, archive this to `agenda/archive/iter_<N>_<title>.md` and replace with the next iter's content.

## Iter ID and goal

**Iter:** 140.1
**Goal:** Build Phase 7 foundation - Autonomous Throttle System and Health Monitoring Framework. This is safety-critical infrastructure that must exist BEFORE we enable autonomous load intake (Phase 2). The throttle system monitors overall system health and automatically reduces load acceptance when problems are detected, preventing operator overload. This iter establishes the GREEN/YELLOW/ORANGE/RED state machine, per-module health check endpoints, and SLA monitoring that will protect autonomous operation at scale.

## Active stage

**Stage:** 1a
**Stage instruction:** `agenda/stages/stage_1a.md`
**Status:** awaiting-start

## Stages in this iter

In execution order. Four stages total.

| Stage | Instruction file | Goal | Gate before starting |
|---|---|---|---|
| 1a | `stages/stage_1a.md` | Build health check framework - every module reports status via `/health` endpoint, unified `health_monitor.py` aggregates and exposes system-wide health | None (first stage) |
| 1b | `stages/stage_1b.md` | Build throttle state machine - `throttle_system.py` implements GREEN/YELLOW/ORANGE/RED states with auto-transitions based on health metrics | Stage 1a complete, health endpoints working |
| 1c | `stages/stage_1c.md` | Build SLA monitoring - track performance targets (RFQ parse <4s, load eval <60s, etc.), alert on misses, feed into throttle state | Stage 1b complete, throttle states functional |
| 1d | `stages/stage_1d.md` | Integration + failure simulation - wire throttle into load acceptance paths, simulate failures to verify throttle triggers correctly, document recovery patterns | Stage 1c complete, SLA monitoring live |

## Iter scope

**In scope:**
- Health check framework (`health_monitor.py`) - per-module status reporting
- Throttle state machine (`throttle_system.py`) - GREEN/YELLOW/ORANGE/RED with auto-transitions
- SLA monitoring (`sla_monitor.py`) - performance target tracking + alerting
- Integration points: auto-dispatch, auto-repost, failure detectors consume throttle state
- Failure simulation harness for smoke testing
- SMS/email alerts on state transitions (YELLOW→ORANGE→RED)
- Auto-recovery monitoring (15+ min stable → step up state)

**Out of scope (do NOT pursue mid-iter):**
- Load board API integration (Phase 2 - iter 141.x)
- Carrier matching improvements (Phase 3)
- GPS integration (Phase 4)
- ML-based anomaly detection (future Phase 7 enhancement)
- Stripe Checkout migration (deferred hygiene)
- Comment cleanup (deferred hygiene)
- R2 cron leak fix (deferred hygiene)
- Frontend dashboard for throttle state (operator can check via API, UI is future work)
- Any changes to load lifecycle stages 1-6 (throttle is cross-cutting, doesn't change workflows)

If anything out-of-scope appears critical mid-iter, STOP and write to `progress/current_status.md` with `BLOCKED ON SCOPE DECISION`.

## Standing rules in effect

All rules in `agenda/protocol.md`. Specifically reinforced for this iter:

- **New modules, NOT monolith-protected** - `health_monitor.py`, `throttle_system.py`, `sla_monitor.py` are net-new files, standard surgical-edit rules apply
- **Existing module edits follow protection rules** - `server.py` is monolith-protected (only import additions for router registration), `auto_dispatch.py` / `failure_detectors.py` are NOT protected
- All key-handling follows post-incident protocol: Read-Host or temp-file paths only
- Four commits total (one per stage)
- No `--no-verify` or git hook bypass
- Adjacent regression check (`/api/ops/briefing?force=true`) after every stage
- Each stage produces working increment (testable via API or smoke harness)

## References

- **Strategic Playbook:** `C:\CHL\memory\CHL_STRATEGIC_PLAYBOOK.md` (Phase 7 - Monitoring & Self-Healing, Autonomous Throttle System section)
- Operating playbook: `https://raw.githubusercontent.com/563352715/chl-memory/main/agenda/protocol.md`
- Prior iter handoff (139.46): `C:\CHL\memory\handoff_iter_139_46_close.md`
- Discovery docs:
  - Operations SOP: `https://raw.githubusercontent.com/563352715/chl-memory/main/operations_sop_code_derived.md`
  - Capability inventory: `https://raw.githubusercontent.com/563352715/chl-memory/main/capability_inventory.md`
  - Module dependency map: `https://raw.githubusercontent.com/563352715/chl-memory/main/module_dependency_map.md`
- EMERGENT_EXIT_PLAN: `C:\CHL\memory\EMERGENT_EXIT_PLAN.md`
- Feedback response protocol: `C:\CHL\memory\feedback_response_protocol.md`

## Strategic context

**Why this iter is critical:**

Per CHL_STRATEGIC_PLAYBOOK.md, the autonomous throttle is the "safety valve that prevents catastrophic operator overload." Without it:
- System failure at 100 loads/day = 100 loads requiring manual intervention
- Operator (solo) cannot handle overflow if automation breaks
- Cascading failures across multiple loads simultaneously

**With throttle system in place:**
- System detects degraded performance (API latency, error rates)
- Auto-reduces intake (100 loads/day → 10 loads/day in ORANGE state)
- Operator can manually handle reduced volume while system recovers or engineers debug
- System auto-recovers when metrics stabilize (ORANGE → YELLOW → GREEN)

**This iter must complete successfully before autonomous load intake (Phase 2) begins.**

## Operator approval

- [x] Operator has reviewed all 4 stage instructions in `agenda/stages/stage_1[a-d].md`
- [x] Operator has reviewed scope, in/out
- [x] Operator understands this is foundational - Phase 2 (load boards) cannot start until throttle is proven
- [x] Operator has committed this iter's agenda to `chl-memory`
- [x] Operator has dispatched Claude Code with the bootstrap message

When all boxes are checked, Claude Code is authorized to execute.
