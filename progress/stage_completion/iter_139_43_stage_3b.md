# Stage Completion — Iter 139.43 Stage 3b

**Stage:** 3b — rfq_inbound.py vision migration to AsyncOpenAI
**Status:** ✅ COMPLETE
**Commit:** [`6ed4188`](https://github.com/563352715/Continental-Haul-Logistics/commit/6ed4188) on CHL `main`
**Completed:** 2026-05-06

## Per-action-step summary

### Step 1: Top-level `from openai import AsyncOpenAI` added

- File: `C:\CHL\backend\rfq_inbound.py:35`
- Inserted between `fastapi` and `pydantic` imports in the third-party import block
- `rfq_inbound.py` is NOT in the monolith protection list — this top-level addition is permitted under standard surgical-edit rules
- Surgical-edit rules honored: ✓ (only one new line, no other top-level changes)

### Step 2: `_parse_with_ai` function body replaced

- File: `C:\CHL\backend\rfq_inbound.py:124-184` (function expanded by ~14 lines)
- Signature preserved: `async def _parse_with_ai(*, body_text: str, attachments: list[dict]) -> _RFQExtract:` ✓
- `_PROMPT` reference preserved ✓
- `_safe_json_parse(raw)` integration preserved ✓
- Auth source: `OPENAI_API_KEY` with `EMERGENT_LLM_KEY` as legacy fallback (per stage doc — operator can drop fallback in a future iter)
- Vision client: `AsyncOpenAI(api_key=...).chat.completions.create(model="gpt-4o", ...)` with `image_url` content blocks
- PDF attachments: explicit `HTTPException(422)` when no images and no usable body
- Surgical-edit rules honored: ✓ (only `_parse_with_ai` body changed, no other functions touched)

### Step 3: Lazy `emergentintegrations` import removed

- Verified by post-action grep (zero matches for `LlmChat|UserMessage|ImageContent|FileContentWithMimeType` in `rfq_inbound.py`)
- Docstring rephrased to avoid tripping the grep with historical references
- Surgical-edit rules honored: ✓

## Smoke results

| Test | Endpoint | Expected | Actual | Status |
|---|---|---|---|---|
| 1 | `POST /api/rfq-inbound/parse-text` (body text only) | 200, valid `_RFQExtract` | HTTP 200 in 4.6s, `shipper_company="Acme Lumber"`, 1 lane (Springfield → Dallas) — real LLM extraction | ✅ PASS |
| 2 | `POST /api/rfq-inbound/parse-text` (PDF attachment only) | 422 with PDF rejection message | HTTP 422 in 237ms, message includes "PDF attachments are not currently supported" | ✅ PASS |
| 3 | `GET /api/ops/briefing?force=true` (Anthropic regression) | 200, `source: llm` | HTTP 200 in 13.2s, `source='llm'`, markdown_len=961 | ✅ PASS |
| (deferred) | Image attachment + body — exercised by next live RFQ | n/a | no test fixture available | ⏸ deferred per stage doc |

All three executed tests passed. Backend boot was clean (no ImportError/ModuleNotFoundError post-restart).

Smoke harness: lived at `C:\CHL\backend\.smoke_3b.py` during run, deleted post-verification.

## Post-action grep (residual state)

```
$ grep -n "LlmChat\|UserMessage\|ImageContent\|FileContentWithMimeType" backend/rfq_inbound.py
(zero matches — clean)

$ grep -n "AsyncOpenAI" backend/rfq_inbound.py
35:from openai import AsyncOpenAI
141:    client = AsyncOpenAI(api_key=api_key)
```

## Caveats

1. **PDF attachments now return 422.** Documented regression — operator approved at planning time. Iter 139.44 candidate (PDF→image preprocessing requires new dependency, e.g., `pypdfium2` or `pdf2image`).
2. **`DEFAULT_PARSER_MODEL` constant is still consumed** by `_safe_json_parse` (line 211) and another reference at line 434, so it's NOT unused. However its value `("gemini", "gemini-2.5-pro")` no longer matches the actual model used (now `gpt-4o`). The `parser_model` field in `_RFQExtract` will report `gemini/gemini-2.5-pro` despite the actual call going to OpenAI. **This is a data-quality issue.** Recommend a follow-up to either (a) update DEFAULT_PARSER_MODEL to `("openai", "gpt-4o")` or (b) replace the constant with a parameter passed by `_parse_with_ai`. Out of scope for stage 3b.
3. **`EMERGENT_LLM_KEY` still works as fallback** in `_parse_with_ai`. The function tries `OPENAI_API_KEY` first and falls back to `EMERGENT_LLM_KEY` only if OpenAI key is missing. Once Stage 3d lands and 3c removes the top-level import, the fallback could be cleaned up too.
4. **Pre-flight #1 found pre-existing untracked files** in working tree (`chl-memory-bootstrap/`, `chl-memory/`, `backend/tests/synthetic_load_walkthrough.py`, etc.) — none affect rfq_inbound.py. Stage commit was scoped to `git add backend/rfq_inbound.py` so no bundling occurred.

## Verify yourself

```powershell
git log --oneline -3
# Expected: 6ed4188 iter 139.43 stage 3b: rfq_inbound vision migration to AsyncOpenAI; PDFs deferred

# Confirm post-stage grep clean
Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "LlmChat|UserMessage|ImageContent|FileContentWithMimeType"
# Expected: no output

# Re-run a quick smoke (body-text-only path)
$body = @{email="<email>"; password="<password>"} | ConvertTo-Json
# Then POST /api/rfq-inbound/parse-text with simple body_text via Bearer
```

End-to-end (logged-in browser at https://continentalhaul.com or local http://localhost:3000):
```js
fetch("/api/rfq-inbound/parse-text", {method: "POST", credentials: "include",
  headers: {"Content-Type": "application/json"},
  body: JSON.stringify({body_text: "Load Springfield MO to Dallas TX, 53 dry van"})})
  .then(r => r.json()).then(j => console.log(j.extract));
```

## Communication protocol applied

- BOTTOM LINE conveyed throughout execution
- State tag: DONE
- Verify-yourself block above
- Handoff filename + size below

## Next stage

Per `agenda/current_iter.md`: **Stage 3c** — single-line removal of unused `LlmChat` import at `server.py:16`. Gate (3b green: smoke clean, single commit landed, residual grep confirms no LlmChat usage outside server.py:16) is **MET**.

Status updated in `progress/current_status.md`. Stage 3c instruction file: `agenda/stages/stage_3c.md`.

Handoff: progress/stage_completion/iter_139_43_stage_3b.md (~5 KB)

DONE
