# Current iter

> This file is the active iter. When an iter closes, archive this to `agenda/archive/iter_<N>_<title>.md` and replace with the next iter's content.

## Iter ID and goal

**Iter:** 139.45
**Goal:** Restore PDF attachment support in RFQ vision parsing by adding PDF-to-image preprocessing. Iter 139.43 stage 3b deliberately regressed PDF handling — PDFs now raise `HTTPException(422)`. This iter converts PDF pages to JPEG images before the AsyncOpenAI vision call in `_parse_with_ai`, restoring full attachment support while maintaining the vision migration architecture.

## Active stage

**Stage:** 5a
**Stage instruction:** `agenda/stages/stage_5a.md`
**Status:** awaiting-start

## Stages in this iter

In execution order. Single-stage iter.

| Stage | Instruction file | Goal | Gate before starting |
|---|---|---|---|
| 5a | `stages/stage_5a.md` | Install `pymupdf`, add PDF→JPEG conversion to `_parse_with_ai`, remove 422 HTTPException block, restore full PDF support with 3-page cap | None — single stage iter |

## Iter scope

**In scope:**
- Install `pymupdf` to `C:\CHL\.venv-local`
- Add PDF-to-image conversion logic in `rfq_inbound.py:_parse_with_ai` (before content_blocks assembly)
- Remove the 422 HTTPException block added in iter 139.43 stage 3b
- Multi-page handling: convert first 3 pages, log total page count
- Edge cases: corrupt PDF (422), empty PDF (422), oversized PDF (422 with page cap message)
- Smoke with real PDF fixture and image regression tests
- Single commit with clean smoke

**Out of scope (do NOT pursue mid-iter):**
- OCR for scanned PDFs (assume native text extraction sufficient)
- PDF page selection UI (auto first-N-pages is sufficient)
- Caching preprocessed images (inline conversion per request)
- Text extraction fallback on conversion failure (fail cleanly with 422)
- Comment cleanup in server.py (cosmetic, separate iter)
- EMERGENT_LLM_KEY constant removal (separate iter)
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
- Pre-flight includes license check for pymupdf (AGPL acceptable for internal tooling, confirm with operator if uncertain)

## References

- Operating playbook: `https://raw.githubusercontent.com/563352715/chl-memory/main/agenda/protocol.md`
- Feedback response protocol: `C:\CHL\memory\feedback_response_protocol.md`
- EMERGENT_EXIT_PLAN: `C:\CHL\memory\EMERGENT_EXIT_PLAN.md`
- Prior iter handoff (139.44): `C:\CHL\memory\handoff_iter_139_44_close.md`
- Stage 3b completion (source of regression): `https://raw.githubusercontent.com/563352715/chl-memory/main/progress/stage_completion/iter_139_43_stage_3b.md`

## Operator approval

- [x] Operator has reviewed stage instruction in `agenda/stages/stage_5a.md`
- [x] Operator has reviewed scope, in/out
- [x] Operator has reviewed library choice (pymupdf / AGPL license acceptable for internal tooling)
- [x] Operator has committed this iter's agenda to `chl-memory`
- [x] Operator has dispatched Claude Code with the bootstrap message

When all boxes are checked, Claude Code is authorized to execute.
