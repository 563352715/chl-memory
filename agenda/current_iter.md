# Current iter

> This file is the active iter. When an iter closes, archive this to `agenda/archive/iter_<N>_<title>.md` and replace with the next iter's content.

## Iter ID and goal

**Iter:** 139.43 (template — replace with actual)
**Goal:** Bootstrap the agenda/progress coordination structure into normal operation by executing one real iter through it. The first real work in this iter is Stage 3b of the EMERGENT_EXIT_PLAN (`rfq_inbound.py:136` vision/PDF migration to direct OpenAI SDK).

## Active stage

**Stage:** 3b
**Stage instruction:** `agenda/stages/stage_3b.md`
**Status:** awaiting-start

## Stages in this iter

In execution order. Each links to its instruction file. Decision gates between stages are explicit.

| Stage | Instruction file | Goal | Gate before starting |
|---|---|---|---|
| 3b | `stages/stage_3b.md` | Migrate `rfq_inbound.py:136` from `LlmChat` (vision/PDF) to direct `AsyncOpenAI` with image_url content blocks for vision and external text-extraction for PDFs | None — first stage of this iter |
| 3c | `stages/stage_3c.md` | Remove `from emergentintegrations.llm.chat import ...` at `server.py:16` and unused `EMERGENT_LLM_KEY` constant. Requires per-edit authorization for the structural import change. | 3b green: smoke clean, single commit landed |
| 3d | `stages/stage_3d.md` | Migrate `live_wire_diary.py:85` (TTS) to direct OpenAI `client.audio.speech.create`. Different shape than chat completions. | 3c green |
| close | `stages/close.md` | Final iter handoff doc, key rotation pass on chat-exposed keys (per `feedback_response_protocol.md` API key intake protocol), update `operating_playbook.md` with lessons | 3d green |

## Iter scope

**In scope:**
- All four stages above
- Updates to `feedback_response_protocol.md` if new lessons surface mid-iter
- Updates to `operating_playbook.md` reflecting how the agenda/progress system performed in its first real use

**Out of scope (do NOT pursue mid-iter):**
- Stripe Checkout migration (`server.py:17`) — separate effort
- R2 cron leak fix from iter 139.41 — on hold
- R1 (`boot_briefing._gather_payload` Day-1 gate counter divergence) — separate
- Frontend (`App.js`) edits of any kind
- New feature work

If something out-of-scope appears critical mid-iter, STOP and write to `progress/current_status.md` with `BLOCKED ON SCOPE DECISION`. Do not silently expand scope.

## Standing rules in effect

All rules in `agenda/protocol.md`. Specifically reinforced for this iter:

- Stage 3c will request per-edit authorization to add/remove a top-level import in `server.py`. That authorization is granted in `stages/stage_3c.md` and only there.
- All key-handling follows the post-incident protocol: Read-Host or temp-file paths only, no values in tool-call args, ever.

## References

- Operating playbook: `https://raw.githubusercontent.com/563352715/chl-memory/main/operating_playbook.md`
- Feedback response protocol: `C:\CHL\memory\feedback_response_protocol.md`
- EMERGENT_EXIT_PLAN: `C:\CHL\memory\EMERGENT_EXIT_PLAN.md`
- Prior iter handoff (139.42): `C:\CHL\memory\handoff_iter_139_42_*.md`

## Operator approval

- [ ] Operator has reviewed all stage instructions in `agenda/stages/`
- [ ] Operator has reviewed scope, in/out
- [ ] Operator has committed this iter's agenda to `chl-memory`
- [ ] Operator has dispatched Claude Code with the bootstrap message in `DEPLOY.md` §"First-iter checklist"

When all four boxes are checked, Claude Code is authorized to execute. Until then, this iter is in planning.
