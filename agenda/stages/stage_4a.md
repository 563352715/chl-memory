# Stage 4a: DEFAULT_PARSER_MODEL constant sync

## Stage objective

Update the `DEFAULT_PARSER_MODEL` constant in `rfq_inbound.py` from `("gemini", "gemini-2.5-pro")` to `("openai", "gpt-4o")` to match the actual model used since iter 139.43 stage 3b. This fixes a data quality bug where the `parser_model` field in `_RFQExtract` responses misreports the model. The constant is consumed by `_safe_json_parse` (line 211) and at least one other reference (line 434 per stage 3b completion doc). Success = smoke test shows `parser_model: "openai/gpt-4o"` in response, all consumers use updated value, single clean commit.

## Pre-flight checks (read-only)

```powershell
# Check 1: Stage 3b/d (iter 139.43) commits landed cleanly
cd C:\CHL
git log --oneline -5
# Expected: recent history shows iter 139.43 stages 3b/3c/3d
# On mismatch: STOP. iter 139.43 must be closed before iter 139.44 starts.

# Check 2: Locate DEFAULT_PARSER_MODEL line in rfq_inbound.py
Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "^DEFAULT_PARSER_MODEL"
# Expected: exactly one match near line 40
# On mismatch: STOP. File shape has shifted since stage was authored.

# Check 3: Confirm current value matches expected old value
Get-Content C:\CHL\backend\rfq_inbound.py | Select-String -Pattern '^DEFAULT_PARSER_MODEL = \("gemini", "gemini-2.5-pro"\)'
# Expected: one match
# On mismatch: STOP. Value may already have been changed, OR shape differs (extra whitespace, different quote style, etc.).

# Check 4: List all references to DEFAULT_PARSER_MODEL (should be 3+ — assignment + at least 2 consumers)
Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "DEFAULT_PARSER_MODEL"
# Expected: at least 3 lines (assignment at ~40, consumer at ~211, consumer at ~434)
# Document the exact line numbers in progress/current_status.md before action steps.

# Check 5: Verify _safe_json_parse still uses DEFAULT_PARSER_MODEL (consumer hasn't been refactored)
Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "_safe_json_parse|parser_model" -Context 1,1
# Expected: _safe_json_parse function references DEFAULT_PARSER_MODEL (joined with "/")
# On mismatch: STOP. If _safe_json_parse no longer uses the constant, the change won't propagate as expected.

# Check 6: Working tree clean before edit (other than long-standing untracked files)
git status --short
# Expected: no modifications to backend/*.py files; long-standing untracked files (chl-memory/, etc.) acceptable but not bundled
# On modifications to backend files: STOP. Do not bundle unrelated changes.
```

## Action steps

### Step 1: Update DEFAULT_PARSER_MODEL constant value

- File: `C:\CHL\backend\rfq_inbound.py` (line number from pre-flight check 2)
- OLD: `DEFAULT_PARSER_MODEL = ("gemini", "gemini-2.5-pro")`
- NEW: `DEFAULT_PARSER_MODEL = ("openai", "gpt-4o")`
- Constraint: This is the ONLY change in this stage. Do not modify any other line. Use exact `str_replace` with the OLD value verbatim from pre-flight check 3.
- Surgical-edit rules honored: ✓ (single constant value update, no function signatures changed, no imports added/removed)

### Step 2: Verify no other code changes needed

After step 1, verify that all consumers of `DEFAULT_PARSER_MODEL` will automatically use the new value (the constant is referenced, not the value copied):

```powershell
# Re-grep to confirm the assignment changed and consumers reference the constant by name
Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "DEFAULT_PARSER_MODEL"
# Expected: same number of lines as pre-flight check 4, but the assignment line now shows ("openai", "gpt-4o")
# Consumer lines should be unchanged (they reference the constant, not the literal value)
```

If any consumer line shows literal `"gemini"` or `"gemini-2.5-pro"` (instead of referencing `DEFAULT_PARSER_MODEL`), that's an out-of-scope finding — log in completion doc, do not touch.

## Restart and smoke

```powershell
# Restart backend to load the new value
Stop-Service CHL-Backend
Start-Sleep -Seconds 2
Start-Service CHL-Backend
Start-Sleep -Seconds 5
Get-Service CHL-Backend | Select-Object Name, Status

# Confirm clean boot
Select-String -Path C:\CHL\logs\nssm\backend.err.log -Pattern "ImportError|ModuleNotFoundError|Traceback" -ErrorAction SilentlyContinue | Select-Object -Last 3
# Expected: no recent errors
```

Auth: `Authorization: Bearer <access_token>` from `/api/auth/login`.

Smoke battery:

**Test 1: RFQ parse with body text (exercises `_safe_json_parse`)**
- Method: `POST /api/rfq-inbound/parse-text` with simple body_text
- Body: `{"body_text": "Load Springfield MO to Dallas TX, 53ft dry van, pickup May 10"}`
- Expected:
  - HTTP 200
  - Response includes `parser_model` field with value `"openai/gpt-4o"` (not `"gemini/gemini-2.5-pro"`)
  - Response includes valid `_RFQExtract` shape (origin/destination/equipment)

**Test 2: Adjacent regression check (boot_briefing still works)**
- Method: `GET /api/ops/briefing?force=true`
- Expected: HTTP 200, `source: llm`, content length > 100 chars
- This confirms backend still boots cleanly after `rfq_inbound` change

**Test 3: Verify git diff is surgical**
- Method: `git diff backend/rfq_inbound.py`
- Expected: exactly 1 line changed (the `DEFAULT_PARSER_MODEL` assignment), no other modifications

## Commit protocol

```powershell
cd C:\CHL
git status
# Expect: only backend/rfq_inbound.py modified (long-standing untracked files acceptable)

git diff backend/rfq_inbound.py
# Inspect: exactly 1 line changed — the DEFAULT_PARSER_MODEL value

git add backend/rfq_inbound.py
git commit -m "iter 139.44 stage 4a: sync DEFAULT_PARSER_MODEL to openai/gpt-4o"
git push origin main
```

If `git status` shows unexpected modifications, STOP. Do not bundle unrelated changes.

## Completion doc specification

Write `progress/stage_completion/iter_139_44_stage_4a.md` (in chl-memory clone) per `progress/stage_completion/completion_template.md`. Include:

- Per-action-step summary: file:line, before/after value, surgical-edit rules honored Y/N
- Smoke results per test above. Include the actual `parser_model` value returned in test 1.
- Post-action grep showing residual state:
  - `Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "DEFAULT_PARSER_MODEL"` — show all matches with context
  - Count should match pre-flight check 4
- Git diff excerpt showing the single-line change
- Verify-yourself line: commit SHA, `git show <SHA> --stat`, grep command to confirm value
- Handoff filename + size

## STOP CONDITIONS specific to this stage

In addition to universal stop conditions in `agenda/protocol.md`:

- Pre-flight check 2 finds `DEFAULT_PARSER_MODEL` in unexpected location or shape
- Pre-flight check 3 shows value is not `("gemini", "gemini-2.5-pro")` (already changed by someone else)
- Pre-flight check 5 shows `_safe_json_parse` no longer uses `DEFAULT_PARSER_MODEL` (code changed)
- After action step 1, smoke test 1 still returns `parser_model: "gemini/gemini-2.5-pro"` (change didn't take effect)
- After action step 1, smoke test 1 returns 500 or non-2xx (broke RFQ parsing)
- After action step 1, smoke test 2 fails (adjacent regression in boot_briefing)
- Git diff shows more than 1 line changed (unintended edits)

## On completion

This is a single-stage iter. After completion:

1. Update `progress/current_status.md`: mark iter 139.44 complete with commit SHA, state `ITER COMPLETE`
2. Operator handles close handoff (writing `C:\CHL\memory\handoff_iter_139_44_close.md`)
3. Next iter candidate: 139.45 (PDF→image preprocessing for RFQ vision)

Handoff: progress/stage_completion/iter_139_44_stage_4a.md

DONE
