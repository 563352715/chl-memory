# Stage Completion — Iter 139.45 Stage 5a

**Stage:** 5a — PDF-to-image preprocessing for RFQ vision (pymupdf)
**Status:** ✅ COMPLETE
**Commit:** [`536f9a4`](https://github.com/563352715/Continental-Haul-Logistics/commit/536f9a4) on CHL `main`
**Completed:** 2026-05-06

## Outcome

Restored PDF attachment support in `_parse_with_ai` by installing pymupdf 1.27.2.3 and adding PDF→JPEG conversion (150 DPI, first 3 pages max). The iter 139.43 stage 3b regression (PDFs raising `HTTPException(422)` "not currently supported") is now removed. Smoke 7/7 PASS — single-page PDFs, multi-page PDFs (first 3 converted), images, body-text-only, oversized (11 pages → 422), and corrupt PDFs (→ 422 "could not be processed") all behave per spec. Anthropic-backed `/api/ops/briefing` regression check confirms no adjacent breakage. Surgical edit: 1 file, +74/-28 lines, function signature unchanged.

## Pre-flight (10 checks, all PASS or expected)

1. ✅ Working tree + recent commits — clean for backend, latest commits are iter 139.44 stage 4a + close + agentdm patch
2. ✅ `_parse_with_ai` at line 125, signature matches stage assumption
3. ✅ 422 HTTPException block exists at lines 164-170 (the regression to fix)
4. ✅ `from openai import AsyncOpenAI` at line 35 (one match)
5. ❎ pymupdf not installed pre-stage — expected, step 1 installs it
6. ✅ `OPENAI_API_KEY` present in backend/.env
7. ❎ No PDF fixture in `backend/tests/fixtures/` — expected, smoke generates fixtures via fitz
8. ✅ Backend service Running, port 8001 open
9. ✅ Git remote → CHL private repo
10. ✅ AGPL-3.0 license attestation provided by operator (Path A authorization)

No STOP CONDITIONS triggered.

## Action steps applied

| # | File:line | Change | Constraints honored |
|---|---|---|---|
| 1 | `.venv-local` | `pip install pymupdf` → 1.27.2.3 | venv only, no other deps changed |
| 2 | `backend/rfq_inbound.py:36` | added `import fitz  # pymupdf for PDF-to-image conversion` | single new import line, surgical |
| 3 | `backend/rfq_inbound.py:126-225` | replaced `_parse_with_ai` body (signature preserved); added PDF→JPEG conversion loop with 1≤pages≤10 enforcement; removed iter 139.43 stage 3b's 422 block | signature unchanged, only function body + one import touched |
| 4 | (verification) | `import fitz` at 36 ✓, `AsyncOpenAI` at 35 ✓, "PDF attachments are not currently supported" → 0 matches ✓ | n/a |

## Smoke results

| # | Test | Method | Expected | Actual | Status |
|---|---|---|---|---|---|
| 1 | PDF single-page | POST `/api/rfq-inbound/parse-text` with 1pg PDF | 200, `parser_model="openai/gpt-4o"` | HTTP 200 in 3361ms; parser_model='openai/gpt-4o'; 1 lane | ✅ PASS |
| 2 | Image attachment | POST with JPEG attachment | 200, valid extraction | HTTP 200 in 2693ms | ✅ PASS |
| 3 | Body-text-only | POST with body_text only | 200 | HTTP 200 in 1643ms | ✅ PASS |
| 4 | Corrupt PDF | POST with malformed PDF bytes | 422 with descriptive error | HTTP 422 in 234ms; `"PDF attachment could not be processed: Failed to open stream..."` | ✅ PASS |
| 5 | Oversized PDF (11 pages) | POST with 11pg PDF | 422 with page count | HTTP 422 in 237ms; `"PDF attachment too large (11 pages)..."` | ✅ PASS |
| 6 | Multi-page (5 pages) | POST with 5pg PDF | 200 (first 3 converted) | HTTP 200 in 3490ms | ✅ PASS |
| 7 | Briefing regression | GET `/api/ops/briefing?force=true` | 200, source=llm, len>100 | HTTP 200 in 9019ms; source='llm', len=977 | ✅ PASS |

All 7 PASS. Smoke harness (`backend/.smoke_5a.py`) deleted post-run.

### Reframing of test 4

Stage instruction's original test 4 expected a 0-page PDF. pymupdf refuses to save 0-page documents with `ValueError: cannot save with zero pages`, so the literal "0 pages" fixture is impossible to generate from the stdlib + pymupdf. The 0-page-check code path (`if total_pages == 0: raise HTTPException(422, "PDF attachment is empty (0 pages)")`) remains in place — it's defensive coverage for hypothetical exotic PDFs but cannot be exercised programmatically. Test 4 was reframed as "corrupt PDF (malformed bytes)" which exercises the same 422 fallback path via the catch-all `except Exception` branch and produces a more meaningful error message.

## Post-action grep (residual state)

```
PS> Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "import fitz|AsyncOpenAI"
rfq_inbound.py:35: from openai import AsyncOpenAI
rfq_inbound.py:36: import fitz  # pymupdf for PDF-to-image conversion
rfq_inbound.py:152: client = AsyncOpenAI(api_key=api_key)

PS> Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "PDF attachments are not currently supported"
(no output — block removed as expected)

PS> Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "fitz\." -Context 0,0 | Measure-Object | Select Count
# fitz.open, fitz.Matrix usages inside _parse_with_ai
```

## Verify yourself

```powershell
# Recent commits
git log --oneline -3
# Expected:
#   536f9a4 iter 139.45 stage 5a: restore PDF support via pymupdf preprocessing (3-page cap, 150 DPI JPEG)
#   e496186 doc: correct agentdm support-ticket claim in iter 139.43 close handoff
#   a820dc3 iter 139.44 close: handoff doc

# Confirm fitz import landed
Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "^import fitz"
# Expected: rfq_inbound.py:36:import fitz  # pymupdf for PDF-to-image conversion

# Confirm 422 block gone
Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "PDF attachments are not currently supported"
# Expected: zero matches

# Confirm pymupdf installed
& "C:\CHL\.venv-local\Scripts\python.exe" -c "import fitz; print(fitz.version[0])"
# Expected: 1.27.2.3 (or any >= 1.23.0)

# git show single-file scope
git show --stat 536f9a4
# Expected: backend/rfq_inbound.py | 102 +/-, 74 insertions(+), 28 deletions(-)
```

End-to-end check (logged-in browser at https://continentalhaul.com):
```javascript
// Use a small PDF you have locally; this just checks the route accepts a PDF.
// (For programmatic generation see C:/CHL/backend/.smoke_5a.py history if needed.)
fetch("/api/rfq-inbound/parse-text", {
  method: "POST",
  credentials: "include",
  headers: {"Content-Type":"application/json"},
  body: JSON.stringify({
    body_text: "See attached PDF",
    attachments: [{ filename: "x.pdf", mime_type: "application/pdf", data_base64: "<your_base64>" }]
  })
})
  .then(r => r.json())
  .then(j => console.log("parser_model:", j.extract.parser_model, "lanes:", j.extract.lanes?.length))
// Expected: parser_model: openai/gpt-4o, lanes: <number>
```

## Caveats

1. **pymupdf 1.27.2.3 is AGPL-3.0** — operator attested via Path A authorization that AGPL is acceptable for CHL internal use. If CHL is ever distributed externally as SaaS to other brokerages, library swap to `pypdfium2` (BSD-3-Clause) is recommended; equivalent functionality, ~2-hour migration.
2. **Page cap hardcoded at 3 (convert) / 10 (reject)** — values are inline constants in `_parse_with_ai`. Tunable in future iter if business needs change. 3 pages is sufficient for typical load tenders (cover, terms, equipment specs); 10-page hard ceiling prevents abuse.
3. **150 DPI rendering hardcoded via `fitz.Matrix(2.08, 2.08)`** — yields ~1240×1600 px from US-Letter, well within gpt-4o vision input limits and good for OCR on standard load tenders. Higher DPI would inflate token costs without quality gain.
4. **No OCR fallback for scanned image-only PDFs** — gpt-4o vision handles scanned-text-as-image natively, so explicit OCR (tesseract/etc.) is unnecessary.
5. **0-page PDF test reframed as "corrupt PDF"** — see "Reframing of test 4" above. Both code paths (`total_pages == 0` and `except Exception`) return 422 with descriptive errors; the latter is exercised by smoke, the former is defensive-only.
6. **Each successful smoke test creates a `rfq_inbound` MongoDB row** — `_persist_rfq` is called by the route handler. Acceptable tradeoff for end-to-end smoke validation; rows can be cleaned up via Mongo shell if needed.

## Open items / next stage

This is the FINAL (and only) stage of iter 139.45. Iter is COMPLETE.

Iter 139.46 candidates (per `agenda/stages/stage_5a.md` "On completion"):
- **Comment cleanup in server.py** (3 stale "Iter 139.42 Stage 3a" comments — cosmetic, ~5min stage)
- **EMERGENT_LLM_KEY constant removal** — requires migrating `automations.py`, `autopilot_phase1.py`, `load_completion_workflow.py` to direct OpenAI SDK first. Multi-stage iter (3-4 stages).
- **R2 cron leak fix** from iter 139.41 (sweep found 25 leaks vs 5-leak threshold — on hold pending root-cause diagnosis)
- **AgentDM bridge re-attempt** — only if operator submits the bug feedback AND agentdm resolves it. Currently parked.

PM Claude was previously asked to draft iter 139.46 candidates while operator committed + dispatched 139.45. Operator can now review PM Claude's recommendation and approve the next direction.

## Communication protocol applied

- BOTTOM LINE conveyed throughout
- State tag: DONE
- Verify-yourself block above
- Handoff filename + size below

Handoff: `progress/stage_completion/iter_139_45_stage_5a.md` (~7 KB)

DONE
