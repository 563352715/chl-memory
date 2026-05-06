# Stage 6a: Migrate automations.py from EMERGENT_LLM_KEY to AsyncOpenAI

## Stage objective

Migrate `automations.py` from emergentintegrations Bearer-auth HTTP calls to direct AsyncOpenAI SDK. The file contains two LLM-calling functions (`d_voice_agent` at ~line 406 and `voice_transcription_ready_check` at ~line 456) that currently make HTTP POST requests to `https://api.emergentintegrations.com/v1/chat/completions` with `Authorization: Bearer {EMERGENT_LLM_KEY}`. Replace these with AsyncOpenAI `client.chat.completions.create()` calls following the pattern from iter 139.43 stage 3b. Additionally, update the `d_voice_agent_ready` flag (line ~86) to check for OPENAI_API_KEY instead of EMERGENT_LLM_KEY. Success = both functions work via AsyncOpenAI, readiness flag correct, EMERGENT_LLM_KEY constant removed, smoke clean.

## Pre-flight checks (read-only)

```powershell
# Check 1: confirm we're on the right commit and tree is clean
cd C:\CHL
git status
git log --oneline -5
# Expected: clean working tree, recent commits include iter 139.45 stage 5a (536f9a4) and close (604762a)
# On mismatch: STOP. Do not proceed with uncommitted unrelated changes in tree.

# Check 2: count EMERGENT_LLM_KEY references in automations.py
Select-String -Path C:\CHL\backend\automations.py -Pattern "\bEMERGENT_LLM_KEY\b" | Measure-Object
# Expected: 7 matches (constant definition + 6 active uses)
# On mismatch: STOP. File has changed since consumer audit.

# Check 3: locate the two functions to migrate
Select-String -Path C:\CHL\backend\automations.py -Pattern "^async def (d_voice_agent|voice_transcription_ready_check)" -Context 0,2
# Expected: exactly 2 matches, d_voice_agent around line 406, voice_transcription_ready_check around line 456
# On mismatch: STOP. Functions moved or renamed.

# Check 4: verify both functions make emergentintegrations HTTP calls
Select-String -Path C:\CHL\backend\automations.py -Pattern "api\.emergentintegrations\.com/v1/chat/completions" -Context 1,1
# Expected: 2 matches (one per function)
# On mismatch: STOP. HTTP call pattern changed.

# Check 5: locate d_voice_agent_ready flag
Select-String -Path C:\CHL\backend\automations.py -Pattern "^d_voice_agent_ready\s*=" -Context 0,2
# Expected: exactly 1 match around line 86, checking for EMERGENT_LLM_KEY
# On mismatch: STOP. Flag location or logic changed.

# Check 6: verify AsyncOpenAI not already imported
Select-String -Path C:\CHL\backend\automations.py -Pattern "^from openai import AsyncOpenAI" -Quiet
# Expected: False (not yet imported)
# On match: STOP. Import already present, migration may be partially done.

# Check 7: verify httpx is imported (currently used for HTTP calls)
Select-String -Path C:\CHL\backend\automations.py -Pattern "^import httpx"
# Expected: exactly 1 match near top of file
# Document location for later comparison (httpx may become unused after migration).

# Check 8: verify OPENAI_API_KEY exists in backend/.env
Select-String -Path C:\CHL\backend\.env -Pattern "^OPENAI_API_KEY=" -Quiet
# Expected: True
# On mismatch: STOP. Key required for migration and smoke tests.

# Check 9: verify backend service is running
Get-Service CHL-Backend
# Expected: Status = Running
# On mismatch: Start-Service CHL-Backend and wait for bind.

# Check 10: check for any usage of EMERGENT_LLM_KEY outside the 2 functions + constant + readiness flag
Select-String -Path C:\CHL\backend\automations.py -Pattern "EMERGENT_LLM_KEY" -Context 1,1
# Expected: 7 total matches - review each to confirm audit is complete
# Document all 7 locations in progress/current_status.md for comparison post-migration.
```

## Action steps

### Step 1: Add AsyncOpenAI import

- File: `C:\CHL\backend\automations.py`
- Location: Near existing imports at top of file, after `import httpx` or similar
- Add: `from openai import AsyncOpenAI`
- Constraint: Single new import line. Standard surgical-edit rules apply (not monolith-protected).

### Step 2: Migrate d_voice_agent function (first LLM caller)

- File: `C:\CHL\backend\automations.py`
- Function: `async def d_voice_agent(...)` (~line 406-450)
- Current pattern (approximate, verify with `view` before editing):

```python
async def d_voice_agent(...):
    # ... setup code ...
    
    api_key = os.environ.get("EMERGENT_LLM_KEY")
    if not api_key:
        raise HTTPException(503, "EMERGENT_LLM_KEY not configured")
    
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
    
    # ... process content ...
```

**Replacement pattern (AsyncOpenAI):**

```python
async def d_voice_agent(...):
    # ... setup code (unchanged) ...
    
    api_key = os.environ.get("OPENAI_API_KEY")
    if not api_key:
        raise HTTPException(503, "OPENAI_API_KEY not configured")
    
    client = AsyncOpenAI(api_key=api_key)
    response = await client.chat.completions.create(
        model="gpt-4o",
        messages=[...],  # same messages list as before
        # ... migrate other params (temperature, max_tokens, etc.) ...
    )
    content = response.choices[0].message.content or ""
    
    # ... process content (unchanged) ...
```

**Key changes:**
1. Replace `os.environ.get("EMERGENT_LLM_KEY")` with `os.environ.get("OPENAI_API_KEY")`
2. Replace httpx AsyncClient POST with `AsyncOpenAI(api_key=api_key)`
3. Replace `client.post(...)` with `client.chat.completions.create(...)`
4. Remove Authorization header and URL (handled by SDK)
5. Replace `data["choices"][0]["message"]["content"]` with `response.choices[0].message.content`
6. Preserve all existing messages, model, and parameters
7. Remove httpx context manager (`async with httpx.AsyncClient()`)

### Step 3: Migrate voice_transcription_ready_check function (second LLM caller)

- File: `C:\CHL\backend\automations.py`
- Function: `async def voice_transcription_ready_check(...)` (~line 456-471)
- Apply the same transformation as step 2:
  - EMERGENT_LLM_KEY → OPENAI_API_KEY
  - httpx POST → AsyncOpenAI SDK
  - Preserve all existing logic, messages, and parameters

### Step 4: Update d_voice_agent_ready flag

- File: `C:\CHL\backend\automations.py`
- Location: ~line 86
- OLD: `d_voice_agent_ready = bool(os.environ.get("EMERGENT_LLM_KEY"))`
- NEW: `d_voice_agent_ready = bool(os.environ.get("OPENAI_API_KEY"))`
- Constraint: Single-line change, no other modifications to flag logic

### Step 5: Remove EMERGENT_LLM_KEY constant definition

- File: `C:\CHL\backend\automations.py`
- Location: Likely near top of file where constants are defined
- Action: Delete the line `EMERGENT_LLM_KEY = os.environ.get("EMERGENT_LLM_KEY")` or similar
- Verify no other references remain in the file after steps 2-4

### Step 6: Verify httpx usage

After migration, check if httpx is still used elsewhere in the file:

```powershell
Select-String -Path C:\CHL\backend\automations.py -Pattern "\bhttpx\b" -Context 0,0
```

- If httpx is ONLY used by the two migrated functions and now unused: leave the import (removing unused imports is cosmetic, separate cleanup)
- If httpx is used elsewhere in the file: leave the import (still needed)

Document finding in progress/current_status.md for future cleanup iter.

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

**Test 1: Verify d_voice_agent_ready flag (simple check)**
- Method: Python REPL or inline script
- Code:
```python
import os
os.environ["OPENAI_API_KEY"] = "test"  # mock value
exec(open("C:/CHL/backend/automations.py").read())
print(f"d_voice_agent_ready = {d_voice_agent_ready}")
# Expected: True (flag now checks OPENAI_API_KEY)
```
- If flag is False, migration of step 4 failed

**Test 2: Call d_voice_agent function (exercises first migrated function)**
- Method: Direct venv harness or API endpoint that triggers d_voice_agent
- Expected:
  - Function executes without httpx/emergentintegrations errors
  - Returns valid LLM response
  - Backend logs show OpenAI SDK call, NOT emergentintegrations.com POST
- If test fixture/endpoint not available, defer to operator with note "deferred - function called by agent routing, requires live agent context"

**Test 3: Call voice_transcription_ready_check function (exercises second migrated function)**
- Method: Direct venv harness or API endpoint that triggers the function
- Expected:
  - Function executes without httpx/emergentintegrations errors
  - Returns valid readiness check result
  - Backend logs show OpenAI SDK call
- If test fixture/endpoint not available, defer with same note as test 2

**Test 4: Verify EMERGENT_LLM_KEY fully removed from automations.py**
- Method:
```powershell
Select-String -Path C:\CHL\backend\automations.py -Pattern "EMERGENT_LLM_KEY"
```
- Expected: 0 matches
- On mismatch: STOP. Residual reference missed in migration.

**Test 5: Adjacent regression check - boot_briefing**
- Method: `GET /api/ops/briefing?force=true`
- Expected:
  - HTTP 200
  - `source: llm`
  - Content length > 100 chars
- This confirms Anthropic path (boot_briefing uses AsyncAnthropic, different key) still works after automations.py changes

**Test 6: Git diff is surgical**
- Method: `git diff backend/automations.py`
- Expected:
  - AsyncOpenAI import added
  - Two functions modified (d_voice_agent, voice_transcription_ready_check)
  - d_voice_agent_ready flag updated
  - EMERGENT_LLM_KEY constant removed
  - No other changes
- On unexpected diffs: STOP and review.

## Commit protocol

```powershell
cd C:\CHL
git status
# Expect: only backend/automations.py modified

git diff backend/automations.py
# Review: confirm AsyncOpenAI migration, EMERGENT_LLM_KEY removed

git add backend/automations.py
git commit -m "iter 139.46 stage 6a: migrate automations.py from EMERGENT_LLM_KEY to AsyncOpenAI (2 functions + readiness flag)"
git push origin main
```

If `git status` shows unexpected modifications, STOP. Do not bundle unrelated changes.

## Completion doc specification

Write `progress/stage_completion/iter_139_46_stage_6a.md` (in chl-memory clone) per `progress/stage_completion/completion_template.md`. Include:

- Per-action-step summary:
  - Step 1: AsyncOpenAI import location
  - Step 2: d_voice_agent migration - before/after pattern, lines changed
  - Step 3: voice_transcription_ready_check migration - before/after pattern, lines changed
  - Step 4: d_voice_agent_ready flag update - old/new value
  - Step 5: EMERGENT_LLM_KEY constant removal - confirm 0 residual matches
  - Step 6: httpx usage finding (still needed or now unused)
- Smoke results per test above (6 tests). Note any deferred tests with justification.
- Post-action grep showing residual state:
  - `Select-String -Path C:\CHL\backend\automations.py -Pattern "EMERGENT_LLM_KEY|AsyncOpenAI|httpx"` - show all matches
  - Confirm EMERGENT_LLM_KEY count is 0, AsyncOpenAI import present
- Git diff summary: functions modified, lines added/removed
- Verify-yourself block: commit SHA, `git show <SHA> --stat`, grep command to confirm EMERGENT_LLM_KEY gone
- Caveats:
  - If tests 2-3 deferred: note "live agent context required, function tested in production use"
  - If httpx import now unused: note "httpx import retained (cosmetic cleanup deferred)"
- Handoff to stage 6b: load_completion_workflow.py migration next

## STOP CONDITIONS specific to this stage

In addition to universal stop conditions in `agenda/protocol.md`:

- Pre-flight check 2 shows EMERGENT_LLM_KEY count ≠ 7 (file changed since audit)
- Pre-flight check 3 shows functions missing or moved (d_voice_agent or voice_transcription_ready_check not found)
- Pre-flight check 4 shows emergentintegrations HTTP calls missing (already migrated or pattern changed)
- Pre-flight check 6 shows AsyncOpenAI already imported (partial migration state)
- After action step 2, d_voice_agent crashes on execution (broke function logic)
- After action step 3, voice_transcription_ready_check crashes on execution (broke function logic)
- After action step 4, d_voice_agent_ready flag is False when OPENAI_API_KEY is set (flag logic broken)
- After action step 5, smoke test 4 still shows EMERGENT_LLM_KEY matches (incomplete removal)
- After action step 5, smoke test 5 fails (adjacent regression in boot_briefing)
- Git diff shows changes outside the 2 functions + flag + constant + import (unintended edits)

## On completion

After stage 6a completes:

1. Update `progress/current_status.md`: mark stage 6a complete, update to "Active stage: 6b"
2. Stage 6b is next: migrate `load_completion_workflow.py` using same AsyncOpenAI pattern
3. Do NOT proceed to stage 6b without operator review of stage 6a completion doc

Handoff: progress/stage_completion/iter_139_46_stage_6a.md

DONE
