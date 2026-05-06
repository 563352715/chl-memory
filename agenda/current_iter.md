# Current iter

> This file is the active iter. When an iter closes, archive this to `agenda/archive/iter_<N>_<title>.md` and replace with the next iter's content.

## Iter ID and goal

**Iter:** 139.46
**Goal:** Remove EMERGENT_LLM_KEY dependency and complete EMERGENT_EXIT_PLAN milestone. Migrate two modules with active LlmChat/emergentintegrations API usage (automations.py, load_completion_workflow.py) to direct AsyncOpenAI SDK, then remove dead EMERGENT_LLM_KEY constants from three modules (autopilot_phase1.py, rfq_inbound.py, server.py). After this iter, no CHL backend code will reference EMERGENT_LLM_KEY or call emergentintegrations.com API endpoints (except Stripe Checkout, which is out-of-scope and remains for separate migration).

## Active stage

**Stage:** 6a
**Stage instruction:** `agenda/stages/stage_6a.md`
**Status:** awaiting-start

## Stages in this iter

In execution order. Four stages total.

| Stage | Instruction file | Goal | Gate before starting |
|---|---|---|---|
| 6a | `stages/stage_6a.md` | Migrate `automations.py` from EMERGENT_LLM_KEY Bearer-auth HTTP calls to AsyncOpenAI SDK (2 functions: `d_voice_agent` and `voice_transcription_ready_check`). Update `d_voice_agent_ready` flag to check OPENAI_API_KEY. | None (first stage) |
| 6b | `stages/stage_6b.md` | Migrate `load_completion_workflow.py` LLM call (line ~284) from EMERGENT_LLM_KEY to AsyncOpenAI SDK | Stage 6a complete, smoke clean |
| 6c | `stages/stage_6c.md` | Remove dead EMERGENT_LLM_KEY constants from `autopilot_phase1.py:28` and `rfq_inbound.py:147` fallback. Two single-line surgical edits, neither file monolith-protected. | Stage 6b complete, smoke clean |
| 6d | `stages/stage_6d.md` | Remove dead EMERGENT_LLM_KEY constant from `server.py:30`. Single-line surgical edit to monolith-protected file. | Stage 6c complete, smoke clean |

## Iter scope

**In scope:**
- Migrate `automations.py` functions (`d_voice_agent`, `voice_transcription_ready_check`) from emergentintegrations HTTP API to AsyncOpenAI SDK
- Migrate `load_completion_workflow.py` LLM call to AsyncOpenAI SDK
- Remove EMERGENT_LLM_KEY constant from `autopilot_phase1.py` (dead, never used)
- Remove `or EMERGENT_LLM_KEY` fallback from `rfq_inbound.py:147` (harmless but now redundant)
- Remove EMERGENT_LLM_KEY constant from `server.py` (dead, never used in this file)
- Four commits total (one per stage)
- Smoke testing per stage confirming migrated functions work + adjacent regression checks

**Out of scope (do NOT pursue mid-iter):**
- Stripe emergentintegrations.payments.* imports (separate migration effort, not blocking)
- Docstring/comment cleanup of historical migration notes in `live_wire_diary.py`, `boot_briefing.py`, `rfq_concierge.py` (doc churn, not blocking)
- The 4 stale "Iter 139.42 Stage 3a" comments in `server.py` (cosmetic, will batch-clean later)
- R2 cron leak fix from iter 139.41 (still on hold pending root cause diagnosis)
- AgentDM bridge re-attempt (blocked on operator submitting bug report to agentdm support)
- Frontend changes
- Key rotation pass (medium priority, separate task)

If anything out-of-scope appears critical mid-iter, STOP and write to `progress/current_status.md` with `BLOCKED ON SCOPE DECISION`.

## Standing rules in effect

All rules in `agenda/protocol.md`. Specifically reinforced for this iter:

- **Monolith protection applies to `server.py` in stage 6d ONLY.** Other files (`automations.py`, `load_completion_workflow.py`, `autopilot_phase1.py`, `rfq_inbound.py`) are NOT monolith-protected and follow standard surgical-edit rules.
- All key-handling follows post-incident protocol: Read-Host or temp-file paths only, no values in tool-call args
- Four commits total (one per stage)
- No `--no-verify` or git hook bypass under any circumstance
- Adjacent regression check (`/api/ops/briefing?force=true`) after every stage to confirm Anthropic path stays green
- Paste discipline: each agenda file sent as ONE complete message-sized block (no fragmentation)

## References

- Operating playbook: `https://raw.githubusercontent.com/563352715/chl-memory/main/agenda/protocol.md`
- Prior iter handoff (139.45): `C:\CHL\memory\handoff_iter_139_45_close.md`
- Stage 3b reference (AsyncOpenAI vision migration pattern): `https://raw.githubusercontent.com/563352715/chl-memory/main/progress/stage_completion/iter_139_43_stage_3b.md`
- Stage 3d reference (AsyncOpenAI TTS migration pattern): `https://raw.githubusercontent.com/563352715/chl-memory/main/progress/stage_completion/iter_139_43_stage_3d.md`
- Stage 3c reference (single-line monolith edit pattern): `https://raw.githubusercontent.com/563352715/chl-memory/main/progress/stage_completion/iter_139_43_stage_3c.md`
- EMERGENT_EXIT_PLAN: `C:\CHL\memory\EMERGENT_EXIT_PLAN.md`
- Feedback response protocol: `C:\CHL\memory\feedback_response_protocol.md`

## Operator approval

- [x] Operator has reviewed all 4 stage instructions in `agenda/stages/stage_6[a-d].md`
- [x] Operator has reviewed scope, in/out
- [x] Operator has reviewed consumer audit (automations.py 7 refs, load_completion_workflow.py 3 refs, autopilot_phase1.py 1 ref, rfq_inbound.py 1 ref, server.py 1 ref)
- [x] Operator has committed this iter's agenda to `chl-memory`
- [x] Operator has dispatched Claude Code with the bootstrap message

When all boxes are checked, Claude Code is authorized to execute.
