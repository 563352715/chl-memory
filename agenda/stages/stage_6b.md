# Stage 6b: Migrate load_completion_workflow.py from EMERGENT_LLM_KEY to AsyncOpenAI

## Stage objective

Migrate `load_completion_workflow.py` LLM call (line ~284) from EMERGENT_LLM_KEY to AsyncOpenAI SDK. The file contains one LLM-calling function that makes HTTP POST to emergentintegrations API with Bearer auth. Replace with AsyncOpenAI `client.chat.completions.create()` following the same pattern as stage 6a. Success = LLM call works via AsyncOpenAI, EMERGENT_LLM_KEY references removed, smoke clean.

## Pre-flight checks (read-only)

```powershell
# Check 1: confirm stage 6a complete and tree is clean
cd C:\CHL
git status
git log --oneline -3
# Expected: clean working tree, most recent commit is stage 6a
# On mismatch: STOP. Stage 6a must complete before 6b starts.

# Check 2: count EMERGENT_LLM_KEY references in load_completion_workflow.py
Select-String -Path C:\CHL\backend\load_completion_workflow.py -Pattern "\bEMERGENT_LLM_KEY\b" | Measure-Object
# Expected: 3 matches (constant + 2 active uses in 1 function)
# On mismatch: STOP. File has changed since consumer audit.

# Check 3: locate the LLM call around line 284
Select-String -Path C:\CHL\backend\load_completion_workflow.py -Pattern "api\.emergentintegrations\.com" -Context 2,2
# Expected: 1 match near line 284
# On mismatch: STOP. LLM call location changed or already migrated.

# Check 4: verify AsyncOpenAI not already imported
Select-String -Path C:\CHL\backend\load_completion_workflow.py -Pattern "^from openai import AsyncOpenAI" -Quiet
# Expected: False (not yet imported)
# On match: STOP. Import already present, migration may be partially done.

# Check 5: verify OPENAI_API_KEY exists in backend/.env
Select-String -Path C:\CHL\backend\.env -Pattern "^OPENAI_API_KEY=" -Quiet
# Expected: True
# On mismatch: STOP. Key required for migration and smoke tests.

# Check 6: verify backend service is running
Get-Service CHL-Backend
# Expected: Status = Running
# On mismatch: Start-Service CHL-Backend and wait for bind.

# Check 7: identify the function containing the LLM call
Select-String -Path C:\CHL\backend\load_completion_workflow.py -Pattern "^async def.*:" -Context 0,15 | Select-String -Pattern "emergentintegrations" -Context 15,0
# Expected: shows function name and context around line 284
# Document function name for smoke test planning.

# Check 8: verify httpx import exists
Select-String -Path C:\CHL\backend\load_completion_workflow.py -Pattern "^import httpx"
# Expected: 1 match (currently used for HTTP call)
# Document for post-migration comparison.
```

## Action steps

### Step 1: Add AsyncOpenAI import

- File: `C:\CHL\backend\load_completion_workflow.py`
- Location: Near existing imports at top of file
- Add: `from openai import AsyncOpenAI`
- Constraint: Single new import line. Not monolith-protected, standard surgical-edit rules apply.

### Step 2: Migrate the LLM call (around line 284)

- File: `C:\CHL\backend\load_completion_workflow.py`
- Location: The function identified in pre-flight check 7
- Current pattern (approximate, verify with `view` before editing):

```python
api_key = os.environ.get("EMERGENT_LLM_KEY")
if not api_key:
    raise SomeException("EMERGENT_LLM_KEY not configured")

async with httpx.AsyncClient() as client:
    response = await client.post(
        "https://api.emergentintegrations.com/v1/chat/completions",
        headers={"Authorization": f"Bearer {api_key}", "Content-Type": "application/json"},
        json={
            "model": "gpt-4o",
            "messages": [...],
            # ... other params ...
        },
    )
    response.raise_for_status()
    data = response.json()
    content = data["choices"][0]["message"]["content"]
```

**Replacement pattern:**

```python
api_key = os.environ.get("OPENAI_API_KEY")
if not api_key:
    raise SomeException("OPENAI_API_KEY not configured")

client = AsyncOpenAI(api_key=api_key)
response = await client.chat.completions.create(
    model="gpt-4o",
    messages=[...],  # same messages list as before
    # ... migrate other params ...
)
content = response.choices[0].message.content or ""
```

**Key changes:**
1. Replace EMERGENT_LLM_KEY with OPENAI_API_KEY
2. Replace httpx AsyncClient POST with AsyncOpenAI SDK
3. Remove Authorization header and URL (handled by SDK)
4. Update response access pattern
5. Preserve all existing messages, model, and parameters
6. Remove httpx context manager

### Step 3: Remove EMERGENT_LLM_KEY constant definition

- File: `C:\CHL\backend\load_completion_workflow.py`
- Location: Near top of file where constants are defined
- Action: Delete the line defining EMERGENT_LLM_KEY
- Verify no other references remain after step 2

### Step 4: Verify httpx usage

After migration, check if httpx is still used elsewhere in the file:

```powershell
Select-String -Path C:\CHL\backend\load_completion_workflow.py -Pattern "\bhttpx\b"
```

- If unused: leave import (cosmetic cleanup deferred)
- If used elsewhere: leave import (still needed)

Document finding in progress/current_status.md.

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

**Test 1: Verify EMERGENT_LLM_KEY fully removed**
- Method:
```powershell
Select-String -Path C:\CHL\backend\load_completion_workflow.py -Pattern "EMERGENT_LLM_KEY"
```
- Expected: 0 matches
- On mismatch: STOP. Residual reference missed.

**Test 2: Call the migrated function**
- Method: API endpoint or venv harness that triggers the load completion workflow
- Expected:
  - Function executes without httpx/emergentintegrations errors
  - Returns valid LLM response
  - Backend logs show OpenAI SDK call
- If endpoint not available, defer with note "deferred - requires load context"

**Test 3: Adjacent regression check - boot_briefing**
- Method: `GET /api/ops/briefing?force=true`
- Expected:
  - HTTP 200
  - `source: llm`
  - Content length > 100 chars

**Test 4: Git diff is surgical**
- Method: `git diff backend/load_completion_workflow.py`
- Expected:
  - AsyncOpenAI import added
  - LLM call function modified
  - EMERGENT_LLM_KEY constant removed
  - No other changes

## Commit protocol

```powershell
cd C:\CHL
git status
# Expect: only backend/load_completion_workflow.py modified

git diff backend/load_completion_workflow.py
# Review: confirm AsyncOpenAI migration, EMERGENT_LLM_KEY removed

git add backend/load_completion_workflow.py
git commit -m "iter 139.46 stage 6b: migrate load_completion_workflow.py from EMERGENT_LLM_KEY to AsyncOpenAI"
git push origin main
```

If `git status` shows unexpected modifications, STOP.

## Completion doc specification

Write `progress/stage_completion/iter_139_46_stage_6b.md` per completion template. Include:

- Per-action-step summary (4 steps)
- Smoke results (4 tests, note any deferred)
- Post-action grep showing EMERGENT_LLM_KEY count = 0
- Git diff summary
- Verify-yourself block with commit SHA
- Caveats (if any tests deferred, if httpx now unused)
- Handoff to stage 6c: cleanup dead constants in autopilot_phase1.py + rfq_inbound.py

## STOP CONDITIONS specific to this stage

- Pre-flight check 1 shows stage 6a not complete
- Pre-flight check 2 shows EMERGENT_LLM_KEY count ≠ 3
- Pre-flight check 3 shows emergentintegrations call missing or moved
- Pre-flight check 4 shows AsyncOpenAI already imported
- After step 2, migrated function crashes on execution
- After step 3, smoke test 1 still shows EMERGENT_LLM_KEY matches
- After step 3, smoke test 3 fails (adjacent regression)
- Git diff shows unintended changes

## On completion

1. Update `progress/current_status.md`: mark 6b complete, active stage → 6c
2. Stage 6c next: remove dead constants from autopilot_phase1.py + rfq_inbound.py
3. Do NOT proceed without operator review

Handoff: progress/stage_completion/iter_139_46_stage_6b.md

DONE
