# Current iter

> This file is the active iter. When an iter closes, archive this to `agenda/archive/iter_<N>_<title>.md` and replace with the next iter's content.

## Iter ID and goal

**Iter:** 139.43
**Goal:** Complete the EMERGENT_EXIT_PLAN's three remaining LLM-call migrations off `emergentintegrations` and onto direct OpenAI/Anthropic SDKs. After this iter, the only `emergentintegrations` references in `backend/` should be `StripeCheckout` (separate effort, out of scope here). This iter also serves as the first real shakedown of the agenda/progress coordination structure installed in Stage 0 (commit `46c01da` on chl-memory main).

## Active stage

**Stage:** 3b
**Stage instruction:** `agenda/stages/stage_3b.md`
**Status:** awaiting-start

## Stages in this iter

| Stage | Instruction file | Goal | Status | Gate before starting |
|---|---|---|---|---|
| 0 | (no instruction file — was the bootstrap commit itself) | Install the `agenda/` and `progress/` coordination structure into chl-memory; commit templates and protocol | LANDED — chl-memory `46c01da`, CHL repo `d21a141` | n/a |
| 3b | `stages/stage_3b.md` | Migrate `rfq_inbound.py:_parse_with_ai` (lines 124-173) from `LlmChat` to `AsyncOpenAI` with `image_url` content blocks for vision. PDFs deferred via clean 422 (regression). | pending | None — first executable stage of this iter |
| 3c | `stages/stage_3c.md` | Remove the now-unused `from emergentintegrations.llm.chat import LlmChat, UserMessage` at `server.py:16`. Single-line removal. Per-edit authorization required (and granted in stage doc). | pending | 3b green: smoke clean, single commit landed, residual grep confirms no `LlmChat` usage outside server.py:16 |
| 3d | `stages/stage_3d.md` | Migrate `live_wire_diary.py:_synth` (lines 82-93) TTS call from `OpenAITextToSpeech` (emergentintegrations) to direct `client.audio.speech.create` on `AsyncOpenAI`. | pending | 3c green |
| close | (no instruction file required for this iter) | Iter handoff doc written to `C:\CHL\memory\handoff_iter_139_43_close.md`. Update `progress/current_status.md` to `ITER COMPLETE`. Note any chat-exposed keys for rotation pass. | pending | 3d green |

## Iter scope

**In scope:**
- Stages 3b, 3c, 3d above (Stage 0 already landed)
- `progress/stage_completion/` doc per stage (3b, 3c, 3d)
- `progress/current_status.md` updates between phases
- Updates to `feedback_response_protocol.md` if new lessons surface (e.g., AsyncOpenAI vision quirks)
- Iter-close handoff doc in `C:\CHL\memory\`

**Out of scope (do NOT pursue mid-iter):**
- Stripe Checkout migration (`server.py:17`) — separate effort
- `EMERGENT_LLM_KEY` constant removal at `server.py:31` — still referenced by `automations.py`, `autopilot_phase1.py`, `load_completion_workflow.py`. Future iter.
- PDF-to-image preprocessing for RFQ vision (deferred from 3b — known regression, candidate for iter 139.44)
- R2 cron leak fix from iter 139.41 — on hold
- R1 (`boot_briefing._gather_payload` Day-1 gate counter divergence) — separate
- Frontend (`App.js`) edits of any kind
- AgentDM wire-up — parked. Server-side identity bug at AgentDM: all 4 rotated `@dev-engineer` keys authenticate as `@pm-lead` from AgentDM's side, producing `self_message` errors when `@pm-lead` (this PM Claude session) attempts to DM `@dev-engineer`. Operator has a support ticket open with AgentDM. Until they resolve, agent-to-agent messaging is non-functional and Claude Code coordination flows entirely through this `chl-memory` repo (raw URL fetches by PM Claude, git commits by Claude Code) plus the operator. Do NOT attempt further AgentDM debugging from inside iter 139.43.
- New feature work

If something out-of-scope appears critical mid-iter, STOP and write to `progress/current_status.md` with `BLOCKED ON SCOPE DECISION`. Do not silently expand scope.

## Standing rules in effect

All rules in `agenda/protocol.md`. Specifically reinforced for this iter:

- Stage 3c requests per-edit authorization to remove a top-level import in `server.py`. That authorization is granted in `stages/stage_3c.md` and only there. No other top-level changes to `server.py` are authorized.
- Stage 3b adds a new top-level import (`from openai import AsyncOpenAI`) to `rfq_inbound.py`. `rfq_inbound.py` is NOT in the monolith protection list (`server.py`, `App.js`, `*auth*.py`), so this is allowed under standard surgical-edit rules — but the import should be added once at file top, not duplicated inside the function body.
- All key-handling follows the post-incident protocol: Read-Host or temp-file paths only, no values in tool-call args, ever.
- If at any point Claude Code finds itself wanting to use `print(api_key[:4])` or similar to "verify" a key value, STOP. Use SHA-256[:16] prefix instead.

## References

- Protocol: `agenda/protocol.md`
- Operating playbook: `https://raw.githubusercontent.com/563352715/chl-memory/main/operating_playbook.md`
- Feedback response protocol: `C:\CHL\memory\feedback_response_protocol.md`
- EMERGENT_EXIT_PLAN: `C:\CHL\memory\EMERGENT_EXIT_PLAN.md`
- Prior iter handoff (139.42): `C:\CHL\memory\handoff_iter_139_42_*.md`
- Stage 0 install handoff: `C:\CHL\memory\handoff_iter_139_43_stage_0_install.md` (commit `d21a141` on CHL repo main)

## Operator approval

- [x] Operator has reviewed all stage instructions in `agenda/stages/`
- [x] Operator has reviewed scope, in/out
- [x] Operator has committed this iter's agenda to `chl-memory`
- [ ] Operator has dispatched Claude Code with the bootstrap message in `DEPLOY.md` §"First-iter checklist"

When all four boxes are checked, Claude Code is authorized to execute. The first three are checked by the act of operator committing this file with stage instructions filled in. The fourth is the dispatch step.
