# Stage 6c: Remove dead EMERGENT_LLM_KEY constants from autopilot_phase1.py and rfq_inbound.py

## Stage objective

Remove dead EMERGENT_LLM_KEY references from two files: (1) `autopilot_phase1.py:28` - unused constant definition, (2) `rfq_inbound.py:147` - `or EMERGENT_LLM_KEY` fallback in the `_parse_with_ai` function that is now redundant since OPENAI_API_KEY is always set. Both are single-line surgical edits. Neither file is monolith-protected. Success = both files have zero EMERGENT_LLM_KEY references, smoke clean, no regressions.

## Pre-flight checks (read-only)

```powershell
# Check 1: confirm stage 6b complete and tree is clean
cd C:\CHL
git status
git log --oneline -3
# Expected: clean working tree, most recent commit is stage 6b
# On mismatch: STOP. Stage 6b must complete before 6c starts.

# Check 2: locate EMERGENT_LLM_KEY in autopilot_phase1.py
Select-String -Path C:\CHL\backend\autopilot_phase1.py -Pattern "EMERGENT_LLM_KEY" -Context 1,1
# Expected: exactly 1 match around line 28 (constant definition, never used elsewhere in file)
# On mismatch: STOP. File changed since audit.

# Check 3: verify constant is truly dead in autopilot_phase1.py
Select-String -Path C:\CHL\backend\autopilot_phase1.py -Pattern "EMERGENT_LLM_KEY" | Measure-Object
# Expected: 1 match (just the definition)
# On mismatch: STOP. If >1, constant is actually used.

# Check 4: locate EMERGENT_LLM_KEY fallback in rfq_inbound.py
Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "or.*EMERGENT_LLM_KEY" -Context 2,2
# Expected: exactly 1 match around line 147 in _parse_with_ai function
# On mismatch: STOP. Line moved or already removed.

# Check 5: verify _parse_with_ai function context
Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "api_key = os\.environ\.get" -Context 0,1
# Expected: shows the line with OPENAI_API_KEY or EMERGENT_LLM_KEY fallback
# Confirm this is inside _parse_with_ai function.

# Check 6: verify OPENAI_API_KEY is always set in .env
Select-String -Path C:\CHL\backend\.env -Pattern "^OPENAI_API_KEY=" -Quiet
# Expected: True (always present, making fallback unnecessary)
# On mismatch: STOP. If key missing, fallback may still be needed.

# Check 7: verify backend service is running
Get-Service CHL-Backend
# Expected: Status = Running
# On mismatch: Start-Service CHL-Backend and wait for bind.

# Check 8: count total EMERGENT_LLM_KEY refs across both files
$count = 0
$count += (Select-String -Path C:\CHL\backend\autopilot_phase1.py -Pattern "EMERGENT_LLM_KEY" | Measure-Object).Count
$count += (Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "EMERGENT_LLM_KEY" | Measure-Object).Count
Write-Host "Total EMERGENT_LLM_KEY refs to remove: $count"
# Expected: 2 (1 from each file)
# Document for post-action comparison.
```

## Action steps

### Step 1: Remove dead constant from autopilot_phase1.py

- File: `C:\CHL\backend\autopilot_phase1.py`
- Location: Line ~28 (verify exact line with `view` before editing)
- Action: Delete the entire line defining EMERGENT_LLM_KEY constant
- Likely looks like: `EMERGENT_LLM_KEY = os.environ.get("EMERGENT_LLM_KEY")`
- Constraint: Delete only this line. No other changes to autopilot_phase1.py.

### Step 2: Remove fallback from rfq_inbound.py

- File: `C:\CHL\backend\rfq_inbound.py`
- Location: Inside `_parse_with_ai` function, line ~147
- Current line (approximate): `api_key = os.environ.get("OPENAI_API_KEY") or os.environ.get("EMERGENT_LLM_KEY")`
- NEW line: `api_key = os.environ.get("OPENAI_API_KEY")`
- Action: Remove the `or os.environ.get("EMERGENT_LLM_KEY")` portion
- Constraint: Only modify this line. Preserve the rest of _parse_with_ai function.

### Step 3: Verify complete removal

After steps 1-2, confirm zero EMERGENT_LLM_KEY references in both files:

```powershell
Select-String -Path C:\CHL\backend\autopilot_phase1.py -Pattern "EMERGENT_LLM_KEY"
Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "EMERGENT_LLM_KEY"
# Expected: both return zero matches
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
# Expected: nothing recent post-restart
```

### Smoke battery

**Test 1: Verify EMERGENT_LLM_KEY removed from autopilot_phase1.py**
- Method:
```powershell
Select-String -Path C:\CHL\backend\autopilot_phase1.py -Pattern "EMERGENT_LLM_KEY"
```
- Expected: 0 matches

**Test 2: Verify EMERGENT_LLM_KEY removed from rfq_inbound.py**
- Method:
```powershell
Select-String -Path C:\CHL\backend\rfq_inbound.py -Pattern "EMERGENT_LLM_KEY"
```
- Expected: 0 matches

**Test 3: RFQ parse still works (exercises rfq_inbound.py)**
- Method: `POST /api/rfq-inbound/parse-text` with body_text
- Body: `{"body_text": "Load Springfield MO to Dallas TX, 53ft dry van, pickup May 15"}`
- Expected:
  - HTTP 200
  - Valid `_RFQExtract` response
  - No 503 errors (OPENAI_API_KEY fallback working)

**Test 4: Adjacent regression check - boot_briefing**
- Method: `GET /api/ops/briefing?force=true`
- Expected:
  - HTTP 200
  - `source: llm`
  - Content length > 100 chars

**Test 5: Git diff shows only the two single-line edits**
- Method: `git diff`
- Expected:
  - `backend/autopilot_phase1.py`: 1 line deleted (EMERGENT_LLM_KEY constant)
  - `backend/rfq_inbound.py`: 1 line modified (removed fallback)
  - No other changes

## Commit protocol

```powershell
cd C:\CHL
git status
# Expect: backend/autopilot_phase1.py and backend/rfq_inbound.py modified

git diff backend/autopilot_phase1.py backend/rfq_inbound.py
# Review: confirm only the two single-line edits

git add backend/autopilot_phase1.py backend/rfq_inbound.py
git commit -m "iter 139.46 stage 6c: remove dead EMERGENT_LLM_KEY constants from autopilot_phase1 + rfq_inbound"
git push origin main
```

If `git status` shows unexpected modifications, STOP.

## Completion doc specification

Write `progress/stage_completion/iter_139_46_stage_6c.md` per completion template. Include:

- Per-action-step summary (3 steps)
- Smoke results (5 tests)
- Post-action grep showing both files have 0 EMERGENT_LLM_KEY matches
- Git diff showing surgical edits (1 delete, 1 modify)
- Verify-yourself block with commit SHA
- Handoff to stage 6d: remove dead constant from server.py (monolith edit)

## STOP CONDITIONS specific to this stage

- Pre-flight check 1 shows stage 6b not complete
- Pre-flight check 2-3 show autopilot_phase1.py constant missing or used elsewhere
- Pre-flight check 4-5 show rfq_inbound.py fallback missing or moved
- Pre-flight check 6 shows OPENAI_API_KEY not set (fallback may still be needed)
- After step 1, autopilot_phase1.py crashes on import (broke module)
- After step 2, smoke test 3 returns 503 (broke RFQ parsing)
- After step 2, smoke test 4 fails (adjacent regression)
- After step 3, either file still shows EMERGENT_LLM_KEY matches
- Git diff shows changes beyond the two expected single-line edits

## On completion

1. Update `progress/current_status.md`: mark 6c complete, active stage → 6d
2. Stage 6d next: remove dead constant from server.py (final EMERGENT_LLM_KEY removal)
3. Do NOT proceed without operator review

Handoff: progress/stage_completion/iter_139_46_stage_6c.md

DONE
