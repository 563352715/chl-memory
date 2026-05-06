# Current iter

> This file is the active iter. When an iter closes, archive this to `agenda/archive/iter_<N>_<title>.md` and replace with the next iter's content.

## Iter ID and goal

**Iter:** 141.1  
**Goal:** Open Phase 2 (Load Discovery & Evaluation) per CHL_STRATEGIC_PLAYBOOK. Build fractal lane-level evaluation framework on top of Phase 7 throttle foundation. Score origin-destination lanes using 90-day historical profitability/reliability/risk/capacity metrics from CHL's existing `db.loads`, surface top historical lanes to operator for manual booking. Establishes FractalEvaluator architecture for future market-level (Phase 8) and autonomous booking (Phase 3) capabilities.

**Stage 1d (DAT live wiring) DEFERRED to iter 141.2** — operator's FMCSA authority pending until ~May 13+, after which DAT subscription + API credentials become available. This iter (141.1) executes stages 1a/1b/1c/1e using only CHL's existing internal data; stage 1d adds live DAT polling later as iter 141.2.

Auto-bid (originally proposed stage 1f) deferred to iter 141.3 or 142.x — this iter delivers discover/score/review only.

## Active stage

**Stage:** 1a  
**Stage instruction:** `agenda/stages/stage_1a.md`  
**Status:** awaiting-start

## Stages in this iter

In execution order. Five stages total.

| Stage | Instruction file | Goal | Gate before starting |
|---|---|---|---|
| 1a | `stages/stage_1a.md` | Build FractalEvaluator base class + refactor auto_dispatch into LoadEvaluator subclass (behavior-preserving) | None (first stage) |
| 1b | `stages/stage_1b.md` | Build LaneEvaluator subclass + /api/lanes/{origin_city}/{origin_state}/{dest_city}/{dest_state}/score endpoint | Stage 1a complete, LoadEvaluator proven equivalent to prior auto_dispatch |
| 1c | `stages/stage_1c.md` | Build lane scoring background loop (cron every 6h, updates db.lane_scores for active lanes) | Stage 1b complete, lane scoring endpoint working |
| 1d | `stages/stage_1d.md` | **DEFERRED to iter 141.2** — DAT load board adapter live wiring (blocked on FMCSA authority + DAT subscription, ~May 13+) | n/a (deferred) |
| 1e | `stages/stage_1e.md` | Build operator dashboard endpoint /api/lanes/top/{N} (ranked by score from internal data; "last 24h activity" view becomes available once 1d lands in 141.2) | Stage 1c complete (1d skipped this iter) |

## Iter scope

**In scope:**
- FractalEvaluator base class implementing four-parameter framework (profitability, reliability, risk, capacity)
- LoadEvaluator subclass (refactor of existing auto_dispatch.try_auto_accept hard rules - behavior preserved)
- LaneEvaluator subclass with 90-day origin-destination aggregation
- Lane scoring endpoint `/api/lanes/{origin_city}/{origin_state}/{dest_city}/{dest_state}/score`
- Background cron (every 6h) updating `db.lane_scores` for active lanes
- Operator dashboard `/api/lanes/top/{N}` showing ranked opportunities from internal data
- SLA tracking integration (@track_sla on lane evaluation, target <60s)
- Throttle gate integration (DAT poller pauses if throttle RED/ORANGE)
- Health check registration (lane_evaluator module reports status)
- Threshold storage in `business_settings.lane_thresholds` (margin 18%, reliability 85%, risk 8%, capacity <20/day as hypothesis defaults)

**Out of scope (do NOT pursue mid-iter):**
- **Stage 1d (DAT live wiring)** — DEFERRED to iter 141.2 (post-FMCSA authority + DAT subscription, ~May 13+). Stage 1d.md remains in agenda for reference but not executed this iter.
- **Stage 1f (auto-bid)** - deferred to iter 141.3 or 142.x. This iter delivers manual booking only.
- **Autonomous booking** - Phase 3 work (iter 142.x when carrier network proven)
- **Truckstop / other load board integrations** - DAT only this iter, others in 141.2+
- **Market-level evaluator** - Phase 8 work (MarketEvaluator subclass)
- **Cold-start lane handling** - defer to 141.2 (lanes with zero historical loads use fallback market rate lookup)
- **Multi-carrier outreach** - Phase 3 work
- **Load board posting** (reverse direction - posting our loads to boards) - separate feature
- **Threshold auto-tuning** - Phase 10 work (ML-based). This iter uses fixed hypothesis thresholds with manual adjustment capability.

If anything out-of-scope appears critical mid-iter, STOP and write to `progress/current_status.md` with `BLOCKED ON SCOPE DECISION`.

## Standing rules in effect

All rules in `agenda/protocol.md`. Specifically reinforced for this iter:

- **FractalEvaluator is net-new** - standard surgical-edit rules apply (not monolith-protected)
- **LoadEvaluator refactor must be behavior-preserving** - smoke tests must prove equivalence to prior auto_dispatch before stage 1a closes
- **LaneEvaluator methods are async** - Motor cursors require `await db.loads.find({}).to_list()`, not synchronous `.find()`
- **Empty-list edge cases** - `mean([])` raises StatisticsError; handle with fallback or skip
- **Lane key composite** - origin_city/origin_state/destination_city/destination_state tuple, not single string
- **Concrete predicates** - "reliable carrier" = `vetted_trusted=True AND mc_verified=True AND no recent failure_alert rows`; "failed load" = `status IN ('cancelled', 'disputed', 'cargo_claim') OR has failure_alert row`
- **DAT credentials** - not required this iter; stage 1d deferred to iter 141.2
- **Four commits total** (stages 1a/1b/1c/1e — stage 1d skipped this iter)
- No `--no-verify` or git hook bypass
- Adjacent regression check (`/api/ops/briefing?force=true`) after every stage
- Each stage produces working increment (testable via API or smoke harness)

## References

- **Strategic Playbook:** `C:\CHL\memory\CHL_STRATEGIC_PLAYBOOK.md` (Phase 2 - Load Discovery & Evaluation)
- **Fractal Decision Framework:** `https://raw.githubusercontent.com/563352715/chl-memory/main/FRACTAL_DECISION_FRAMEWORK.md` (architecture pattern, thresholds, code examples)
- Operating playbook: `https://raw.githubusercontent.com/563352715/chl-memory/main/agenda/protocol.md`
- Prior iter handoff (140.1): `C:\CHL\memory\handoff_iter_140_1_close.md`
- Discovery docs:
  - Operations SOP: `https://raw.githubusercontent.com/563352715/chl-memory/main/operations_sop_code_derived.md`
  - Capability inventory: `https://raw.githubusercontent.com/563352715/chl-memory/main/capability_inventory.md`
  - Module dependency map: `https://raw.githubusercontent.com/563352715/chl-memory/main/module_dependency_map.md`

## Strategic context

**Why this iter is critical:**

Per CHL_STRATEGIC_PLAYBOOK.md Phase 2:
> "Load Discovery & Evaluation enables autonomous load intake. System scans load boards, evaluates profitability/risk, surfaces opportunities without operator doing manual board-watching."

**Phase 7 foundation (iter 140.1) enables Phase 2 safety:**
- Throttle system prevents operator overload if DAT floods opportunities
- Health monitoring detects if lane scoring degrades
- SLA tracking ensures lane evaluation stays <60s

**Fractal architecture unlocks Phase 8:**
- Same four-parameter framework scales to market-level strategy
- Load-level + lane-level proven → market-level (Phase 8) is mechanical extension
- Parameter learning transfers: load margin data → lane profitability → market entry decisions

**Manual booking retained this iter:**
- Operator reviews top-scored lanes before committing
- Builds confidence in lane scoring accuracy
- Auto-bid (stage 1f) deferred until operator trusts the algorithm

**Business impact:**
- Current: Operator manually scans DAT → 5-10 loads/day capacity limit
- After 141.1: System scans DAT → surfaces top 20 lanes → operator books best → 20-30 loads/day capacity
- After 141.2+ (auto-bid): System books autonomously → 50-100 loads/day capacity

## Operator approval

Before Claude Code executes:

- [x] Operator has reviewed all 5 stage instructions in `agenda/stages/stage_1[a-e].md` (Path A delegation)
- [x] Operator has reviewed scope (in/out), understands stage 1f (auto-bid) is deferred AND stage 1d (DAT live) is deferred to iter 141.2 (post-FMCSA-authority May 13+)
- [x] DAT credentials deferred — not blocking this iter (stage 1d in iter 141.2)
- [x] Operator has committed this iter's agenda to `chl-memory` (Claude Code committing on operator's behalf via Path A)
- [x] Operator has dispatched Claude Code with the bootstrap message (verbal "make the call" 2026-05-06 ~13:25)

When all boxes are checked, Claude Code is authorized to execute.

**Next iter candidates after 141.1:**
- **141.2: DAT live wiring** (originally stage 1d — runs once FMCSA authority lands ~May 13+ and DAT subscription + API credentials are in place)
- 141.3: Auto-bid on top-tier lanes (originally proposed stage 1f)
- 141.4: Additional load board integrations (Truckstop, 123Loadboard)
- 142.1: Phase 3 - Carrier Network & Dispatch (multi-carrier outreach, AI carrier matching)
