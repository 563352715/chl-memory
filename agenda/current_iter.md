# Current iter

> This file is the active iter. When an iter closes, archive this to `agenda/archive/iter_<N>_<title>.md` and replace with the next iter's content.

## Iter ID and goal

**Iter:** 139.44
**Goal:** Fix DEFAULT_PARSER_MODEL data quality bug in rfq_inbound.py. The constant currently holds `("gemini", "gemini-2.5-pro")` but the actual model used since iter 139.43 stage 3b is `gpt-4o`. This causes the `parser_model` field in `_RFQExtract` responses to misreport the model used, affecting logs and analytics.

## Active stage

**Stage:** 4a
**Stage instruction:** `agenda/stages/stage_4a.md`
**Status:** awaiting-start

## Stages in this iter

In execution order. Single-stage iter.

| Stage | Instruction file | Goal | Gate before starting |
|---|---|---|---|
| 4a | `stages/stage_4a.md` | Update `DEFAULT_PARSER_MODEL` constant from `("gemini", "gemini-2.5-pro")` to `("openai", "gpt-4o")` and verify consumers report correctly | None — single stage iter |

## Iter scope

**In scope:**
- Update `DEFAULT_PARSER_MODEL` constant in `rfq_inbound.py`
- Verify `_safe_json_parse` and all other consumers use the updated value
- Smoke test confirming `parser_model` field in response now reports `openai/gpt-4o`
- Single commit with clean smoke

**Out of scope (do NOT pursue mid-iter):**
- PDF→image preprocessing (iter 139.45 candidate)
- Comment cleanup in server.py (cosmetic, low priority)
- EMERGENT_LLM_KEY constant removal (requires migrating 3+ consumers, separate iter)
- Stripe Checkout migration
- R2 cron leak fix
- Frontend changes
- Any edits outside `rfq_inbound.py`

If anything out-of-scope appears critical mid-iter, STOP and write to `progress/current_status.md` with `BLOCKED ON SCOPE DECISION`.

## Standing rules in effect

All rules in `agenda/protocol.md`. Specifically reinforced for this iter:

- `rfq_inbound.py` is NOT in the monolith protection list — standard surgical-edit rules apply
- All key-handling follows the post-incident protocol: Read-Host or temp-file paths only, no values in tool-call args
- Single commit per stage (only one stage in this iter, so single commit for the entire iter)

## References

- Operating playbook: `https://raw.githubusercontent.com/563352715/chl-memory/main/agenda/protocol.md`
- Feedback response protocol: `C:\CHL\memory\feedback_response_protocol.md`
- EMERGENT_EXIT_PLAN: `C:\CHL\memory\EMERGENT_EXIT_PLAN.md`
- Prior iter handoff (139.43): `C:\CHL\memory\handoff_iter_139_43_close.md`
- Stage 3b completion (source of bug): `https://raw.githubusercontent.com/563352715/chl-memory/main/progress/stage_completion/iter_139_43_stage_3b.md`

## Operator approval

- [x] Operator has reviewed stage instruction in `agenda/stages/stage_4a.md`
- [x] Operator has reviewed scope, in/out
- [x] Operator has committed this iter's agenda to `chl-memory`
- [ ] Operator has dispatched Claude Code with the bootstrap message

When all four boxes are checked, Claude Code is authorized to execute. Operator typically commits agenda + dispatches in one session.
