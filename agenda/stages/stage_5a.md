# Stage 5a: PDF-to-image preprocessing for RFQ vision

## Stage objective

Restore PDF attachment support in RFQ vision parsing by installing `pymupdf` and adding PDF→JPEG conversion logic to `_parse_with_ai`. PDFs are converted to JPEG images (150 DPI, first 3 pages max) before being passed to AsyncOpenAI's vision API as base64 data URLs. The 422 HTTPException block from iter 139.43 stage 3b is removed — PDFs now work. Edge cases (corrupt, empty, oversized) return 422 with descriptive errors. Success = smoke with real PDF load tender returns 200 + valid extraction, image attachments still work, no regression on text-only path.

## Pre-flight checks (read-only)

```powershell
# Check 1: confirm we're on the right commit and tree is clean
cd C:\CHL
git status
git log --oneline -5
# Expected: clean working tree, recent commits include iter 139.44 stage 4a (DEFAULT_PARSER_MODEL sync)
# On mismatch: STOP. Do not proceed with uncommitted unrelated changes in tree.

# Check 2: verify _parse_with_ai function location and shape
Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "^async def _parse_with_ai" -Context 0,2
# Expected: exactly one match, signature includes body_text and attachments kwargs
# On mismatch: STOP. Function has moved or signature changed since stage authoring.

# Check 3: confirm 422 HTTPException block exists (the regression we're fixing)
Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "PDF attachments are not currently supported" -Context 2,2
# Expected: one match inside _parse_with_ai, raising HTTPException(422)
# On mismatch: STOP. Block already removed or message changed.

# Check 4: verify AsyncOpenAI import exists from iter 139.43 stage 3b
Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "^from openai import AsyncOpenAI"
# Expected: exactly one match near top of file
# On mismatch: STOP. Import missing or changed.

# Check 5: check if pymupdf is already installed
& "C:\CHL\.venv-local\Scripts\python.exe" -c "import fitz; print(f'pymupdf {fitz.version[0]}')" 2>$null
# Expected: either prints version (already installed) OR fails (not installed yet)
# Document result in progress/current_status.md. If already installed, verify version >= 1.23.0.

# Check 6: verify OPENAI_API_KEY in backend/.env
Select-String -Path C:\CHL\backend\.env -Pattern "^OPENAI_API_KEY=" -Quiet
# Expected: True
# On mismatch: STOP. Key required for smoke tests.

# Check 7: locate test PDF fixture (if exists)
if (Test-Path C:\CHL\backend\tests\fixtures\*.pdf) {
    Get-ChildItem C:\CHL\backend\tests\fixtures\*.pdf | Select-Object -First 1 | ForEach-Object { Write-Host "Found test PDF: $($_.Name)" }
} else {
    Write-Host "No test PDF fixture — will create minimal one for smoke"
}
# Document result in progress/current_status.md.

# Check 8: verify backend service state
Get-Service CHL-Backend
# Expected: Status = Running
# On mismatch: Start-Service CHL-Backend and wait for bind.

# Check 9: git remote check
git remote -v
# Expected: origin pointing to CHL private repo
# On mismatch: STOP.

# Check 10: pymupdf license check
Write-Host "pymupdf license: AGPL-3.0 (acceptable for internal tooling, not distributed software)"
Write-Host "If commercial distribution is planned, verify license compatibility or use pypdfium2 instead."
# Operator approval required if uncertain — check current_iter.md approval checklist.
```

## Action steps

### Step 1: Install pymupdf

```powershell
cd C:\CHL
& .\.venv-local\Scripts\python.exe -m pip install --upgrade pymupdf
# Expected: successful installation, version >= 1.23.0

# Verify installation
& .\.venv-local\Scripts\python.exe -c "import fitz; print(f'pymupdf version: {fitz.version[0]}'); print(f'Import OK')"
# Expected: prints version and "Import OK"
```

If installation fails, STOP and document error in `progress/current_status.md` with state `BLOCKED ON DEPENDENCY INSTALL`.

### Step 2: Add pymupdf import to rfq_inbound.py

- File: `C:\CHL\backend\rfq_inbound.py`
- Location: Near existing imports, after `from openai import AsyncOpenAI` (around line 35 based on stage 3b completion)
- Add: `import fitz  # pymupdf for PDF-to-image conversion`
- Constraint: Single new import line. Standard surgical-edit rules apply (rfq_inbound.py not in monolith protection list).

### Step 3: Replace _parse_with_ai function body with PDF preprocessing logic

- File: `C:\CHL\backend\rfq_inbound.py`
- Function: `async def _parse_with_ai(*, body_text: str, attachments: list[dict]) -> _RFQExtract:`
- Location: Lines 124-184 (approximate, verify with pre-flight check 2)

**Replacement body (complete function body, preserving signature):**

```python
    """
    Extract RFQ data from email body text and/or attachments using OpenAI vision.

    Iter 139.43 Stage 3b: Migrated from emergentintegrations.LlmChat to direct AsyncOpenAI.
    Iter 139.45 Stage 5a: Added PDF-to-image preprocessing (pymupdf) to restore PDF support.

    Args:
        body_text: Email body content (plain text)
        attachments: List of dicts with 'mime_type' and 'data_base64' keys

    Returns:
        _RFQExtract with parsed fields (origin, destination, etc.)

    Raises:
        HTTPException(503): OPENAI_API_KEY not configured
        HTTPException(422): PDF conversion failed or attachment unprocessable
    """
    import base64
    import io

    api_key = os.environ.get("OPENAI_API_KEY") or os.environ.get("EMERGENT_LLM_KEY")
    if not api_key:
        raise HTTPException(503, "OPENAI_API_KEY not configured")

    client = AsyncOpenAI(api_key=api_key)

    # Build content blocks: text first, then image_url blocks for image/PDF attachments
    content_blocks: list = [
        {"type": "text", "text": _PROMPT + "\n\n" + (body_text or "(no email body)")}
    ]

    for att in attachments[:10]:  # safety cap on attachment count
        mime = (att.get("mime_type") or "").lower()
        b64_data = att.get("data_base64") or ""
        if not b64_data:
            continue

        if mime.startswith("image/"):
            # Native image attachment — pass through as-is
            content_blocks.append({
                "type": "image_url",
                "image_url": {"url": f"data:{mime};base64,{b64_data}"},
            })

        elif mime == "application/pdf":
            # Convert PDF pages to JPEG images (first 3 pages max)
            try:
                pdf_bytes = base64.b64decode(b64_data)
                doc = fitz.open(stream=pdf_bytes, filetype="pdf")
                total_pages = doc.page_count

                if total_pages == 0:
                    raise HTTPException(422, "PDF attachment is empty (0 pages)")

                if total_pages > 10:
                    # Hard cap to prevent abuse — log and reject
                    logger.warning(f"RFQ PDF rejected: {total_pages} pages exceeds 10-page limit")
                    raise HTTPException(
                        422,
                        f"PDF attachment too large ({total_pages} pages). "
                        "Please reduce to 10 pages or fewer, or send as images."
                    )

                # Convert first 3 pages to JPEG
                pages_to_convert = min(3, total_pages)
                logger.info(f"Converting {pages_to_convert}/{total_pages} PDF pages to JPEG")

                for page_num in range(pages_to_convert):
                    page = doc.load_page(page_num)
                    # Render at 150 DPI (matrix scale factor ~2.08 for 72->150)
                    pix = page.get_pixmap(matrix=fitz.Matrix(2.08, 2.08))
                    img_bytes = pix.tobytes("jpeg")
                    img_b64 = base64.b64encode(img_bytes).decode("ascii")

                    content_blocks.append({
                        "type": "image_url",
                        "image_url": {"url": f"data:image/jpeg;base64,{img_b64}"},
                    })

                doc.close()

            except HTTPException:
                raise  # Re-raise our own exceptions (empty, oversized)
            except Exception as e:
                logger.warning(f"PDF conversion failed: {e}")
                raise HTTPException(
                    422,
                    f"PDF attachment could not be processed: {str(e)}. "
                    "Please attach as an image (PNG/JPG) or paste content into email body."
                )

    # If we only have the text block and no images, attempt text-only parse
    # (body_text-only RFQs should still work)
    resp = await client.chat.completions.create(
        model="gpt-4o",
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

**Key changes from iter 139.43 stage 3b body:**
1. Added `import fitz` and PDF processing logic
2. Removed the `if pdf_seen and len(content_blocks) == 1:` HTTPException(422) block — PDFs now work
3. Added page count checks (empty → 422, >10 pages → 422)
4. Added PDF→JPEG conversion loop (first 3 pages at 150 DPI)
5. Preserved all other behavior (image attachments, text-only fallback, _safe_json_parse integration)

Surgical-edit rules: Function signature unchanged. Only `_parse_with_ai` body modified. No changes to other functions, constants, or imports outside this step.

### Step 4: Verify imports are clean

```powershell
# Confirm fitz import is present
Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "^import fitz"
# Expected: exactly one match

# Confirm AsyncOpenAI import still present (no accidental removal)
Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "^from openai import AsyncOpenAI"
# Expected: exactly one match

# Confirm 422 block is gone
Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "PDF attachments are not currently supported"
# Expected: zero matches (removed as part of step 3)
```

## Restart and smoke

```powershell
# Restart backend to load new code
Stop-Service CHL-Backend
Start-Service CHL-Backend
Get-Service CHL-Backend
# Wait for FastAPI to bind: ~3-5 seconds

# Confirm boot didn't crash
Select-String -Path C:\CHL\logs\nssm\backend.err.log -Pattern "ERROR|Traceback" -Context 0,3 | Select-Object -Last 5
# Expected: nothing recent post-restart, OR pre-existing warnings only
```

### Smoke battery

**Test 1: PDF attachment (single page) — the regression fix**
- Method: `POST /api/rfq-inbound/parse-text` with PDF attachment
- Fixture: Use test PDF from pre-flight check 7, or create minimal 1-page PDF with load tender text
- Expected:
  - HTTP 200 (not 422)
  - Response includes valid `_RFQExtract` shape
  - Response includes `parser_model: "openai/gpt-4o"` (from iter 139.44)
  - Backend logs show "Converting 1/1 PDF pages to JPEG"

**Test 2: Image attachment regression check**
- Method: `POST /api/rfq-inbound/parse-text` with image (PNG/JPG)
- Expected:
  - HTTP 200
  - Valid extraction
  - No PDF conversion logic triggered (image passes through)

**Test 3: Body text only (no attachments)**
- Method: `POST /api/rfq-inbound/parse-text` with `body_text` only
- Body: `{"body_text": "Load Springfield MO to Dallas TX, 53ft dry van, pickup May 15"}`
- Expected:
  - HTTP 200
  - Valid extraction from text alone
  - No attachment processing

**Test 4: Empty PDF (edge case)**
- Method: `POST /api/rfq-inbound/parse-text` with 0-page PDF
- Expected:
  - HTTP 422
  - Error message includes "empty (0 pages)"

**Test 5: Oversized PDF (edge case)**
- Method: `POST /api/rfq-inbound/parse-text` with 11+ page PDF (if fixture available)
- Expected:
  - HTTP 422
  - Error message includes "too large" and page count
- If no 11-page fixture available, defer with note "deferred — validated via page cap logic review"

**Test 6: Multi-page PDF (3+ pages)**
- Method: `POST /api/rfq-inbound/parse-text` with 5-page PDF
- Expected:
  - HTTP 200
  - Backend logs show "Converting 3/5 PDF pages to JPEG"
  - Valid extraction (from first 3 pages)

**Test 7: Adjacent regression — boot_briefing**
- Method: `GET /api/ops/briefing?force=true`
- Expected:
  - HTTP 200
  - `source: llm`
  - Content length > 100 chars

Smoke harness: Python script at `C:\CHL\backend\.smoke_5a.py` using `httpx` for multipart uploads. Delete post-verification.

## Commit protocol

```powershell
cd C:\CHL
git status
# Expect: backend/rfq_inbound.py modified, possibly .venv-local/ changes (ignore)

git diff backend/rfq_inbound.py
# Review: confirm fitz import added, _parse_with_ai body updated, 422 block removed

git add backend/rfq_inbound.py
git commit -m "iter 139.45 stage 5a: restore PDF support via pymupdf preprocessing (3-page cap, 150 DPI JPEG)"
git push origin main
```

If `git status` shows unexpected modifications outside `backend/rfq_inbound.py`, STOP. Do not bundle unrelated changes.

## Completion doc specification

Write `progress/stage_completion/iter_139_45_stage_5a.md` (in chl-memory clone) per `progress/stage_completion/completion_template.md`. Include:

- Per-action-step summary:
  - Step 1: pymupdf version installed
  - Step 2: fitz import location
  - Step 3: _parse_with_ai body before/after line count, surgical-edit rules honored Y/N
  - Step 4: verification grep results
- Smoke results per test above (all 7 tests). Include actual page counts from logs for PDF tests.
- Post-action grep showing residual state:
  - `Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "import fitz|AsyncOpenAI|pdf_seen"` — show matches
  - Confirm "PDF attachments are not currently supported" has zero matches
- Git diff summary: lines added/removed, function changed
- Verify-yourself block: commit SHA, `git show <SHA> --stat`, smoke reproduction command
- Caveats:
  - Page cap set to 3 (configurable in future if needed)
  - 150 DPI hardcoded (good for load tenders, may need adjustment for other use cases)
  - No OCR fallback for scanned PDFs (vision model handles text in images)
  - pymupdf AGPL license (acceptable for internal tooling)
- Handoff filename + size

## STOP CONDITIONS specific to this stage

In addition to universal stop conditions in `agenda/protocol.md`:

- Pre-flight check 5 shows pymupdf install fails (missing C compiler, Windows incompatibility)
- Pre-flight check 3 shows 422 block already removed (someone else changed the code)
- After step 1, `import fitz` fails in Python REPL (install didn't work)
- After step 3, smoke test 1 still returns 422 for PDF (conversion logic didn't work)
- After step 3, smoke test 1 returns 500 (broke RFQ parsing entirely)
- After step 3, smoke test 2 fails (regressed image attachment handling)
- After step 3, smoke test 3 fails (regressed text-only parsing)
- After step 3, smoke test 7 fails (adjacent regression in boot_briefing)
- Backend logs show `MemoryError` or excessive RAM usage during PDF conversion (>500MB spike)
- Git diff shows changes outside `_parse_with_ai` function (unintended edits)

## On completion

This is a single-stage iter. After completion:

1. Update `progress/current_status.md`: mark iter 139.45 complete with commit SHA, state `ITER COMPLETE`
2. Operator handles close handoff (writing `C:\CHL\memory\handoff_iter_139_45_close.md`)
3. Next iter candidates:
   - Comment cleanup in server.py (cosmetic)
   - EMERGENT_LLM_KEY constant removal (requires migrating consumers in automations.py, autopilot_phase1.py, load_completion_workflow.py)
   - R2 cron leak fix (still on hold from iter 139.41)
   - AgentDM bridge re-attempt (when their support resolves identity bug)

Handoff: progress/stage_completion/iter_139_45_stage_5a.md

DONE
