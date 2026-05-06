# Stage 6d: Remove dead EMERGENT_LLM_KEY constant from server.py

## Stage objective

Remove the dead EMERGENT_LLM_KEY constant from `server.py:30`. This is a single-line surgical edit to a **monolith-protected file** - special care required per `agenda/protocol.md`. The constant is defined but never used elsewhere in server.py. After this stage, EMERGENT_LLM_KEY will be completely removed from all CHL backend code (except Stripe-related imports which are out-of-scope). Success = constant deleted, server.py still boots clean, smoke passes, EMERGENT_EXIT_PLAN milestone complete.

## Pre-flight checks (read-only)

```powershell
# Check 1: confirm stage 6c complete and tree is clean
cd C:\CHL
git status
git log --oneline -4
# Expected: clean working tree, most recent commit is stage 6c
# On mismatch: STOP. Stage 6c must complete before 6d starts.

# Check 2: locate EMERGENT_LLM_KEY constant in server.py
Select-String -Path C:\CHL\backend\server.py -Pattern "^EMERGENT_LLM_KEY\s*=" -Context 1,1
# Expected: exactly 1 match at line ~30
# On mismatch: STOP. Constant location changed or already removed.

# Check 3: verify constant is truly dead in server.py
Select-String -Path C:\CHL\backend\server.py -Pattern "\bEMERGENT_LLM_KEY\b" | Measure-Object
# Expected: 1 match (just the definition at line 30)
# On mismatch: STOP. If >1, constant is actually used in server.py.

# Check 4: confirm no EMERGENT_LLM_KEY usage in other recently-migrated files
$files = @(
    "C:\CHL\backend\automations.py",
    "C:\CHL\backend\load_completion_workflow.py",
    "C:\CHL\backend\autopilot_phase1.py",
    "C:\CHL\backend\rfq_inbound.py"
)
foreach ($f in $files) {
    $count = (Select-String -Path $f -Pattern "EMERGENT_LLM_KEY" -ErrorAction SilentlyContinue | Measure-Object).Count
    if ($count -gt 0) {
        Write-Host "ERROR: $f still has EMERGENT_LLM_KEY references - stage 6c incomplete"
        exit 1
    }
}
Write-Host "✓ All prior stages complete - no EMERGENT_LLM_KEY in migrated files"
# Expected: pass (0 matches in all files)
# On mismatch: STOP. Prior stage incomplete.

# Check 5: verify backend service is running
Get-Service CHL-Backend
# Expected: Status = Running
# On mismatch: Start-Service CHL-Backend and wait for bind.

# Check 6: confirm server.py is monolith-protected
Write-Host "server.py is MONOLITH-PROTECTED - single-line surgical edit only"
Write-Host "Reference: agenda/protocol.md and iter 139.43 stage 3c completion"
# This is a reminder, not a test. Proceed with extra care.

# Check 7: verify emergentintegrations Stripe imports are still present
Select-String -Path C:\CHL\backend\server.py -Pattern "from emergentintegrations\.payments\.stripe" -Context 0,1
# Expected: 1+ matches (Stripe imports are out-of-scope, must remain)
# On mismatch: document in current_status.md, but not a STOP.

# Check 8: count total lines in server.py before edit
$lineCount = (Get-Content C:\CHL\backend\server.py).Count
Write-Host "server.py total lines before edit: $lineCount"
# Document for post-edit comparison (should be $lineCount - 1 after delete).
```

## Action steps

### Step 1: Remove EMERGENT_LLM_KEY constant from server.py

- File: `C:\CHL\backend\server.py` (**MONOLITH-PROTECTED**)
- Location: Line ~30 (verify exact line with `view` before editing)
- Current line (approximate): `EMERGENT_LLM_KEY = os.environ.get("EMERGENT_LLM_KEY")`
- Action: **Delete this single line completely**
- Constraint: This is the ONLY change permitted in server.py this stage. Use `str_replace` with exact old_str match. No other modifications.

### Step 2: Verify line count decreased by exactly 1

After step 1:

```powershell
$newLineCount = (Get-Content C:\CHL\backend\server.py).Count
$expectedLineCount = $lineCount - 1  # from pre-flight check 8
if ($newLineCount -eq $expectedLineCount) {
    Write-Host "✓ Line count correct: $newLineCount (was $lineCount)"
} else {
    Write-Host "✗ Line count mismatch: $newLineCount (expected $expectedLineCount)"
    exit 1
}
```

### Step 3: Verify no EMERGENT_LLM_KEY references remain in server.py

```powershell
Select-String -Path C:\CHL\backend\server.py -Pattern "EMERGENT_LLM_KEY"
# Expected: 0 matches (constant completely gone)
```

### Step 4: Verify Stripe imports untouched

```powershell
Select-String -Path C:\CHL\backend\server.py -Pattern "emergentintegrations\.payments\.stripe"
# Expected: still present (Stripe out-of-scope, must remain)
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

**Test 1: Verify EMERGENT_LLM_KEY removed from server.py**
- Method:
```powershell
Select-String -Path C:\CHL\backend\server.py -Pattern "EMERGENT_LLM_KEY"
```
- Expected: 0 matches

**Test 2: Verify Stripe imports still present**
- Method:
```powershell
Select-String -Path C:\CHL\backend\server.py -Pattern "emergentintegrations\.payments\.stripe"
```
- Expected: 1+ matches (out-of-scope imports preserved)

**Test 3: Backend boots and serves traffic**
- Method: `GET /api/health` or any simple endpoint
- Expected: HTTP 200, server responding normally

**Test 4: Adjacent regression check - boot_briefing**
- Method: `GET /api/ops/briefing?force=true`
- Expected:
  - HTTP 200
  - `source: llm`
  - Content length > 100 chars

**Test 5: RFQ parse still works (confirms no unexpected breakage)**
- Method: `POST /api/rfq-inbound/parse-text` with body_text
- Expected: HTTP 200, valid extraction

**Test 6: Git diff shows ONLY the one-line deletion**
- Method: `git diff backend/server.py`
- Expected:
  - Exactly 1 line deleted (EMERGENT_LLM_KEY constant)
  - No other changes to server.py
  - Diff is clean and surgical

**Test 7: Verify EMERGENT_LLM_KEY globally removed from all backend files**
- Method:
```powershell
Get-ChildItem C:\CHL\backend\*.py -Recurse | Select-String -Pattern "\bEMERGENT_LLM_KEY\b" | Select-Object -First 10
```
- Expected: 0 matches OR only docstring/comment mentions in live_wire_diary.py, boot_briefing.py, rfq_concierge.py (historical notes, out-of-scope)
- If matches found in active code: STOP. Migration incomplete.

## Commit protocol

```powershell
cd C:\CHL
git status
# Expect: only backend/server.py modified

git diff backend/server.py
# Review: confirm ONLY the EMERGENT_LLM_KEY line deleted, no other changes

git add backend/server.py
git commit -m "iter 139.46 stage 6d: remove dead EMERGENT_LLM_KEY constant from server.py (EMERGENT_EXIT_PLAN complete)"
git push origin main
```

If `git status` shows unexpected modifications, STOP. Do not bundle unrelated changes.

## Completion doc specification

Write `progress/stage_completion/iter_139_46_stage_6d.md` per completion template. Include:

- Per-action-step summary (4 steps)
- Smoke results (7 tests)
- Post-action grep showing:
  - server.py has 0 EMERGENT_LLM_KEY matches
  - Global backend search shows 0 active code matches (only historical docstrings if any)
  - Stripe imports preserved
- Git diff showing surgical one-line deletion
- Line count before/after (should decrease by exactly 1)
- Verify-yourself block with commit SHA
- **EMERGENT_EXIT_PLAN milestone complete** - note this achievement
- Caveats:
  - Stripe emergentintegrations imports remain (out-of-scope)
  - Docstring mentions in historical files remain (out-of-scope)
- Handoff to iter close: iter 139.46 complete, EMERGENT_LLM_KEY fully removed

## STOP CONDITIONS specific to this stage

- Pre-flight check 1 shows stage 6c not complete
- Pre-flight check 2 shows EMERGENT_LLM_KEY not at expected line or already removed
- Pre-flight check 3 shows EMERGENT_LLM_KEY used elsewhere in server.py (not actually dead)
- Pre-flight check 4 shows any prior migrated file still has EMERGENT_LLM_KEY
- After step 1, line count doesn't decrease by exactly 1 (unintended edits)
- After step 1, smoke test 2 shows Stripe imports removed (out-of-scope deletion)
- After step 1, smoke test 3 fails (backend doesn't boot)
- After step 1, smoke test 4 or 5 fails (adjacent regression)
- After step 1, smoke test 7 shows EMERGENT_LLM_KEY in active code (incomplete migration)
- Git diff shows changes beyond the single-line deletion

## On completion

After stage 6d completes:

1. Update `progress/current_status.md`: mark stage 6d complete, state → `ITER COMPLETE`
2. **EMERGENT_EXIT_PLAN milestone achieved** - EMERGENT_LLM_KEY fully removed from CHL backend
3. Operator writes iter close handoff: `C:\CHL\memory\handoff_iter_139_46_close.md`
4. Next iter candidates:
   - Comment cleanup batch (4 stale "Iter 139.42" comments in server.py)
   - R2 cron leak fix (needs investigation iter first)
   - AgentDM bridge re-attempt (operator-gated)

Handoff: progress/stage_completion/iter_139_46_stage_6d.md

DONE
