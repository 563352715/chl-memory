# Stage 3c: server.py top-level cleanup — remove LlmChat import

## Stage objective

Remove the now-unused `from emergentintegrations.llm.chat import LlmChat, UserMessage` at `server.py:16`. After Stage 3b lands, no code path in `backend/` references `LlmChat` or `UserMessage` (verified by grep). This stage is a one-line removal. The `EMERGENT_LLM_KEY` constant at `server.py:31` is NOT touched in this stage — it remains referenced by `automations.py`, `autopilot_phase1.py`, `load_completion_workflow.py`, and (until Stage 3d lands) `live_wire_diary.py`. Stripe-related imports at `server.py:17` are also NOT touched (out of EMERGENT_EXIT_PLAN scope tonight).

This stage requires explicit per-edit authorization for a top-level structural change to a monolith file. **That authorization is granted below.**

## Per-edit authorization (explicit)

Per `agenda/protocol.md` standing rule: "C:\CHL\backend\server.py: surgical edits to function bodies allowed. No structural refactoring — no add/remove/reorganize functions, no new top-level imports beyond explicit per-stage authorization, no moves across regions."

**AUTHORIZED for this stage only:** Remove the top-level import statement at `server.py:16` (the `LlmChat, UserMessage` import from `emergentintegrations.llm.chat`).

This authorization does NOT extend to:
- Any other line in `server.py`
- The `StripeCheckout` import on the next line (line 17)
- The `EMERGENT_LLM_KEY` constant on line 31
- Reordering, formatting, or whitespace cleanup elsewhere in the import block

If Claude Code finds itself making any other change to `server.py`, STOP.

## Pre-flight checks (read-only)

```powershell
# Check 1: confirm Stage 3b landed cleanly
cd C:\CHL
git log --oneline -5
# Expected: most recent commit is "iter 139.43 stage 3b: ..."
# On mismatch: STOP. 3c gate requires 3b complete.

# Check 2: confirm no remaining LlmChat/UserMessage usage in backend
Select-String -Path C:\CHL\backend\*.py -Pattern "LlmChat|UserMessage" -Recurse
# Expected: ONLY the import at server.py:16 — zero usages anywhere else in backend
# On mismatch (any other usage found): STOP. The 3b gate logically failed even if Stage 3b's commit landed; some module still depends on these symbols. Investigate before removing the import.

# Check 3: confirm server.py:16 still has the expected shape
Get-Content C:\CHL\backend\server.py | Select-Object -Index 15  # 0-indexed line 15 = line 16 in editors
# Expected: line content is `from emergentintegrations.llm.chat import LlmChat, UserMessage`
# On mismatch: STOP. File shape has shifted. Identify the actual line number with:
#   Select-String -Path C:\CHL\backend\server.py -Pattern "from emergentintegrations.llm.chat import"

# Check 4: confirm server.py imports block is otherwise as expected
Get-Content C:\CHL\backend\server.py -TotalCount 35
# Expected: matches the 35-line block documented in this stage's source notes
# On mismatch: note divergence in progress/current_status.md but proceed cautiously — the line-16 target is what matters

# Check 5: working tree clean before edit
git status
# Expected: clean
# On mismatch: STOP. Do not bundle unrelated changes.
```

## Action steps

1. **Remove single import line** — `C:\CHL\backend\server.py:16`
   - OLD: `from emergentintegrations.llm.chat import LlmChat, UserMessage`
   - NEW: (line removed entirely; subsequent lines shift up by one)
   - Constraint: ONLY this line. Do not touch the surrounding import block, the `StripeCheckout` import on the line below (now at line 16 after removal), or any other line. Authorization granted explicitly above.

## Restart and smoke

```powershell
# Restart backend
Stop-Service CHL-Backend
Start-Service CHL-Backend
Get-Service CHL-Backend
# Wait ~3-5 seconds

# Confirm boot
Select-String -Path C:\CHL\logs\nssm\backend.err.log -Pattern "ImportError|ModuleNotFoundError" -Context 0,5 | Select-Object -Last 3
# Expected: no ImportError or ModuleNotFoundError post-restart
# A clean boot here is the primary smoke for this stage — if the import was load-bearing somewhere unexpected, server boot would fail.
```

Auth: `Authorization: Bearer <token>` per usual.

Smoke battery:

- **`GET /api/ops/briefing?force=true`** — Anthropic LLM call path
  - Expected: 200, `source: llm`, briefing content present (~1k+ chars)
  - Why: this is the most reliable LLM-touching endpoint to confirm boot cleanliness
- **`POST /api/assistant/chat`** with a trivial message — OpenAI gpt-4o
  - Expected: 200, response payload includes assistant reply
  - Why: confirms no collateral damage to other LLM-dependent endpoints
- **`POST /api/rfq/inbound`** with body text only — confirms 3b's migration still works
  - Expected: 200, `_RFQExtract` shape
  - Why: regression check on the immediately preceding stage's work
- **Adjacent regression: `GET /api/health` or `GET /docs`** — no auth required
  - Expected: 200
  - Why: cheapest possible "is the server up?" check

## Commit protocol

```powershell
cd C:\CHL
git status
# Expect: only backend/server.py modified, with one line deleted

git diff backend/server.py
# Expect: exactly one line removed, no other changes (no whitespace, no reordering)

git add backend/server.py
git commit -m "iter 139.43 stage 3c: remove unused LlmChat import from server.py"
git push origin main
```

If `git diff` shows more than the single-line removal, STOP. Revert with `git restore backend/server.py` and re-apply the surgical edit.

## Completion doc specification

Write `progress/stage_completion/iter_139_43_stage_3c.md` per `completion_template.md`. Include:

- Per-action-step summary: confirms single-line removal, confirms surrounding import block untouched
- Smoke results per target above
- Post-action grep showing residual state:
  - `Select-String -Path C:\CHL\backend\*.py -Pattern "from emergentintegrations.llm.chat" -Recurse` — expected: zero matches
  - `Select-String -Path C:\CHL\backend\*.py -Pattern "LlmChat|UserMessage" -Recurse` — expected: zero matches
- Confirmation that `EMERGENT_LLM_KEY` constant at server.py (now line 30 after removal) is unchanged:
  - `Select-String -Path C:\CHL\backend\server.py -Pattern "^EMERGENT_LLM_KEY"` — expected: one match
- Caveats: none expected
- Communication protocol applied: state tag at end (`DONE`)

## STOP CONDITIONS specific to this stage

In addition to universal stop conditions:

- Stage 3b commit not present (gate violation)
- `LlmChat` or `UserMessage` usage found anywhere in `backend/*.py` outside the line-16 import (would mean 3b incomplete)
- Server fails to boot after the line removal (would indicate the import was load-bearing somewhere we didn't catch)
- `git diff` shows more than the single-line removal (would indicate scope creep or accidental edit)

## On completion

Update `progress/current_status.md`: mark Stage 3c complete with commit SHA, identify Stage 3d as next per iter agenda, transition to `agenda/stages/stage_3d.md`.
