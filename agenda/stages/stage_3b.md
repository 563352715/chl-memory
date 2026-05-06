# Stage 3b: rfq_inbound.py vision migration to AsyncOpenAI

## Stage objective

Replace the `LlmChat`-based vision call in `rfq_inbound.py:_parse_with_ai` (lines 124-173) with a direct `AsyncOpenAI.chat.completions.create` call using `image_url` content blocks. Image attachments continue to work via base64 data URLs. PDF attachments are explicitly rejected at this layer with `HTTPException(422)` — this is a deliberate, documented regression deferred to a future iter (PDF→image preprocessing requires a new dependency that's out of scope tonight). Function signature, return type, prompt assembly, and `_safe_json_parse` integration all remain unchanged. Success = function returns valid `_RFQExtract` from an image-bearing RFQ in smoke; PDF attachments cleanly 422 with a recoverable error message.

## Pre-flight checks (read-only)

```powershell
# Check 1: confirm we're on the right commit and tree is clean
cd C:\CHL
git status
git log --oneline -3
# Expected: clean working tree, recent commit history visible
# On mismatch: STOP. Do not proceed with uncommitted unrelated changes in tree.

# Check 2: confirm rfq_inbound.py shape matches stage assumptions
Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "^async def _parse_with_ai" -Context 0,2
# Expected: exactly one match, near line 124, signature includes `body_text` and `attachments` keyword args
# On mismatch: STOP. File has shifted since stage authoring.

Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "from emergentintegrations.llm.chat import"
# Expected: one match inside the _parse_with_ai function body (the lazy import)
# On mismatch: STOP. Import location has changed.

# Check 3: confirm OpenAI SDK is installed and version is compatible
C:\CHL\.venv-local\Scripts\python.exe -c "import openai; print(openai.__version__)"
# Expected: 1.99.9 or higher
# On mismatch: STOP. Note version in progress/current_status.md.

C:\CHL\.venv-local\Scripts\python.exe -c "from openai import AsyncOpenAI; c = AsyncOpenAI(api_key='dummy'); print('AsyncOpenAI import ok')"
# Expected: prints "AsyncOpenAI import ok"
# On mismatch: STOP.

# Check 4: confirm OPENAI_API_KEY is in backend/.env
Select-String -Path C:\CHL\backend\.env -Pattern "^OPENAI_API_KEY=" -Quiet
# Expected: True
# On mismatch: STOP. Operator must add OPENAI_API_KEY before this stage proceeds.

# Check 5: locate the @router.post route that calls _parse_with_ai
Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "@router\.post|_parse_with_ai\(" -Context 1,1
# Expected: route handler in the build_router section (~line 200+) calls _parse_with_ai
# Document the actual route path in progress/current_status.md for smoke use.

# Check 6: verify _PROMPT and DEFAULT_PARSER_MODEL exist in the file
Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "^_PROMPT|^DEFAULT_PARSER_MODEL"
# Expected: at least two matches (one assignment each)
# On mismatch: STOP. Stage assumed these as module-level constants.

# Check 7: confirm Stage 0 of this iter is committed (no uncommitted agenda work bleeding in)
cd C:\CHL\..\chl-memory  # or wherever local clone lives
git log --oneline -3
# Expected: 46c01da (Stage 0 install) and the agenda/stages/* commit visible

# Check 8: confirm git remote is correct
cd C:\CHL
git remote -v
# Expected: origin pointing to the CHL private repo (NOT chl-memory)
```

## Action steps

1. **Add top-level OpenAI import** — `C:\CHL\backend\rfq_inbound.py` near existing imports (top of file, after stdlib block)
   - OLD: (no `from openai import AsyncOpenAI` at top of file)
   - NEW: `from openai import AsyncOpenAI` added once, alongside other top-level imports
   - Constraint: rfq_inbound.py is NOT in the monolith protection list, so this top-level import addition is permitted under standard surgical-edit rules. Add the import to a logical position in the existing import block. Do NOT remove any existing imports in this step.

2. **Replace `_parse_with_ai` function body** — `C:\CHL\backend\rfq_inbound.py:124-173`
   - OLD: function body using `LlmChat`, `UserMessage`, `ImageContent`, `FileContentWithMimeType`, lazy import inside the function, tempfile-based PDF handling
   - NEW: function body using `AsyncOpenAI` with `image_url` content blocks, explicit PDF rejection via `HTTPException(422)`. Function signature, parameter names, return type, and use of `_PROMPT` and `_safe_json_parse` all preserved.
   - Replacement body (verbatim, indented inside the existing `async def _parse_with_ai(...)` signature):

```python
    api_key = os.environ.get("OPENAI_API_KEY") or os.environ.get("EMERGENT_LLM_KEY")
    if not api_key:
        raise HTTPException(503, "OPENAI_API_KEY not configured")

    client = AsyncOpenAI(api_key=api_key)

    # Build content blocks: text first, then image_url blocks for image attachments.
    content_blocks: list = [
        {"type": "text", "text": _PROMPT + "\n\n" + (body_text or "(no email body)")}
    ]

    pdf_seen = False
    for att in attachments[:10]:  # safety cap
        mime = (att.get("mime_type") or "").lower()
        b64 = att.get("data_base64") or ""
        if not b64:
            continue
        if mime.startswith("image/"):
            content_blocks.append({
                "type": "image_url",
                "image_url": {"url": f"data:{mime};base64,{b64}"},
            })
        elif mime == "application/pdf":
            pdf_seen = True

    if pdf_seen and len(content_blocks) == 1:
        # Only PDFs were attached, no images and no usable body — refuse cleanly.
        # PDF-to-image preprocessing is deferred (iter 139.44 candidate).
        raise HTTPException(
            422,
            "PDF attachments are not currently supported by RFQ vision parsing. "
            "Please attach the load tender as an image (PNG/JPG) or paste the "
            "content into the email body.",
        )

    # If we have body text but PDFs only, attempt text-only parse as best effort.
    # If we have at least one image, proceed with vision parse.
    resp = await client.chat.completions.create(
        model="gpt-4o",  # vision-capable; matches DEFAULT_PARSER_MODEL intent
        messages=[
            {
                "role": "system",
                "content": "You are a freight RFQ extraction assistant that ALWAYS responds with valid JSON only. No prose, no markdown fences, just the JSON object.",
            },
            {"role": "user", "content": content_blocks},
        ],
        response_format={"type": "json_object"},
        max_tokens=2000,
    )
    raw = resp.choices[0].message.content or ""
    return _safe_json_parse(raw)
```

   - Constraint: function signature preserved. `_PROMPT` and `_safe_json_parse` references unchanged. The behavior change "PDFs raise 422 when no images present" is the documented regression. Body-text-only parses (no attachments at all) continue to work because `content_blocks` will have one element (the text block) and `pdf_seen` is False.
   - Note: `DEFAULT_PARSER_MODEL` tuple is replaced by hardcoded `"gpt-4o"` — this is a deliberate simplification consistent with the EMERGENT_EXIT_PLAN's direction (no provider-tuple indirection layer). If `DEFAULT_PARSER_MODEL` is referenced elsewhere in the file, leave the constant in place; just don't use it inside this function. Grep at pre-flight: `Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "DEFAULT_PARSER_MODEL"` — if it has only one assignment site and no other references, log this in completion doc as "DEFAULT_PARSER_MODEL now unused but not removed (out of scope)."

3. **Remove the lazy `emergentintegrations` import block from inside `_parse_with_ai`** — confirmed gone as part of step 2's body replacement, but verify with grep after.

## Restart and smoke

```powershell
# Restart backend to load the new code
Stop-Service CHL-Backend
Start-Service CHL-Backend
Get-Service CHL-Backend
# Wait for FastAPI to bind: ~3-5 seconds

# Confirm boot didn't crash
Select-String -Path C:\CHL\logs\nssm\backend.err.log -Pattern "ERROR|Traceback" -Context 0,3 | Select-Object -Last 5
# Expected: nothing recent post-restart, OR pre-existing warnings only (compliance_watcher 503 is OK)
```

Auth: cookie is `Secure; SameSite=none`, won't forward over `http://`. Use `Authorization: Bearer <token>` from `/api/auth/login` response.

Smoke battery:

- **`POST /api/rfq/inbound`** (or whatever the actual route is — confirm from pre-flight check 5) — multipart/form-data with one small image attachment + body text describing a load
  - Expected: 200, body matches `_RFQExtract` shape (origin/destination fields, etc.)
  - Method: PowerShell `Invoke-RestMethod` with form data, OR a small Python script using `httpx` + the test image fixture if one exists
  - If no test image fixture available: defer with note "deferred — exercised by next live RFQ"
- **`POST /api/rfq/inbound`** — same, but with a PDF attachment instead of image and no body text
  - Expected: 422, error message mentions "PDF attachments are not currently supported"
  - This validates the deliberate regression
- **`POST /api/rfq/inbound`** — body text only, no attachments
  - Expected: 200, `_RFQExtract` shape (model parses from text alone)
  - This validates the no-attachment fallback path still works
- **Adjacent regression check: `GET /api/ops/briefing?force=true`**
  - Expected: 200, response includes `source: llm` (Anthropic boot_briefing path)
  - This stage doesn't touch boot_briefing, but smoke confirms backend still boots cleanly after rfq_inbound changes

## Commit protocol

```powershell
cd C:\CHL
git status
# Expect: only backend/rfq_inbound.py modified

git add backend/rfq_inbound.py
git commit -m "iter 139.43 stage 3b: rfq_inbound vision migration to AsyncOpenAI; PDFs deferred"
git push origin main
```

If `git status` shows unexpected modifications, STOP. Do not bundle unrelated changes.

## Completion doc specification

Write `progress/stage_completion/iter_139_43_stage_3b.md` (in chl-memory clone) per `progress/stage_completion/completion_template.md`. Include:

- Per-action-step summary: file:line, before/after signature, surgical-edit rules honored Y/N
- Smoke results per target above. Mark PDF-deferred test as `green` (it exercised the new 422 path), not `deferred`.
- Post-action grep showing residual state:
  - `Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "LlmChat|UserMessage|ImageContent|FileContentWithMimeType"` — expected: zero matches
  - `Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "AsyncOpenAI"` — expected: at least one match (the import + usage)
- Caveats:
  - PDF attachments now 422. Known regression. Operator notified at planning time. Iter 139.44 candidate.
  - `DEFAULT_PARSER_MODEL` constant may now be unused — verify with grep, note status.
- Communication protocol applied: state tag at end (`DONE`), verify-yourself line, handoff filename + size

## STOP CONDITIONS specific to this stage

In addition to universal stop conditions in `agenda/protocol.md`:

- `OPENAI_API_KEY` not present in `backend/.env` (pre-flight check 4 fails)
- `_parse_with_ai` signature has changed since this stage was authored (pre-flight check 2 fails)
- OpenAI SDK version below 1.99.9 (pre-flight check 3 fails)
- After action step 2, smoke fails on the body-text-only path (this would indicate the new code broke a previously-working scenario)
- After action step 2, the `compliance_watcher` warning escalates beyond the pre-existing 503 (would indicate import-level damage)
- Adjacent regression: `/api/ops/briefing?force=true` returns 500 or non-LLM source (would indicate broader server damage)

## On completion

Update `progress/current_status.md`: mark Stage 3b complete with commit SHA, identify Stage 3c as next per iter agenda, transition to `agenda/stages/stage_3c.md`. Confirm gate condition (3b green) is met before Claude Code begins 3c.
