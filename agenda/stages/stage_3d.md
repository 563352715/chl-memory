# Stage 3d: live_wire_diary.py TTS migration to OpenAI audio.speech

## Stage objective

Replace the `OpenAITextToSpeech`-based TTS call in `live_wire_diary.py:_synth` (lines 82-93) with a direct `client.audio.speech.create(...)` call on `AsyncOpenAI`. The function returns mp3 bytes on success, `None` on failure or missing key — same external contract. After this stage, `emergentintegrations.llm.openai` is no longer imported by any file in `backend/`. `live_wire_diary` is a non-public-route module — it's called from a scheduled task — so smoke is via direct venv harness.

## Pre-flight checks (read-only)

```powershell
# Check 1: confirm Stage 3c landed cleanly
cd C:\CHL
git log --oneline -5
# Expected: most recent commit is "iter 139.43 stage 3c: ..."
# On mismatch: STOP.

# Check 2: confirm _synth shape matches stage assumptions
Select-String -Path C:\CHL\backend\live_wire_diary.py -Pattern "^async def _synth" -Context 0,12
# Expected: signature `async def _synth(text: str) -> Optional[bytes]:` near line 82,
# body uses `OpenAITextToSpeech` and returns `await tts.generate_speech(...)`
# On mismatch: STOP.

# Check 3: confirm OpenAI SDK is installed (re-check from 3b)
C:\CHL\.venv-local\Scripts\python.exe -c "from openai import AsyncOpenAI; print('ok')"
# Expected: "ok"

# Check 4: confirm OPENAI_API_KEY in backend/.env
Select-String -Path C:\CHL\backend\.env -Pattern "^OPENAI_API_KEY=" -Quiet
# Expected: True

# Check 5: probe how AsyncOpenAI's audio.speech.create response exposes bytes
# This determines whether to use `await resp.aread()` or `resp.content`.
C:\CHL\.venv-local\Scripts\python.exe -c "
import asyncio, os
from openai import AsyncOpenAI
async def probe():
    client = AsyncOpenAI()
    resp = await client.audio.speech.create(model='tts-1', voice='onyx', input='probe', response_format='mp3')
    methods = [m for m in dir(resp) if not m.startswith('_')]
    print('METHODS:', sorted(methods))
    # Try aread first
    try:
        data = await resp.aread()
        print('AREAD_OK len=', len(data))
    except Exception as e:
        print('AREAD_FAIL:', type(e).__name__, str(e)[:80])
        try:
            data = resp.content
            print('CONTENT_OK len=', len(data))
        except Exception as e2:
            print('CONTENT_FAIL:', type(e2).__name__, str(e2)[:80])
asyncio.run(probe())
"
# Expected: one of AREAD_OK or CONTENT_OK prints with a non-zero length.
# Document which path works in progress/current_status.md and use that pattern in step 1.
# On both fail: STOP. SDK behavior diverges from stage assumptions — do not guess.
```

## Action steps

1. **Replace `_synth` function body** — `C:\CHL\backend\live_wire_diary.py:82-93`
   - OLD: function body using `from emergentintegrations.llm.openai import OpenAITextToSpeech` (lazy import), `EMERGENT_LLM_KEY`, and `tts.generate_speech(...)`
   - NEW: function body using `AsyncOpenAI().audio.speech.create(...)` and reading bytes per the path that worked in pre-flight check 5
   - Replacement body (verbatim, indented inside the existing `async def _synth(text: str) -> Optional[bytes]:` signature). Choose the bytes-extraction line based on pre-flight check 5 result:

```python
    if not os.environ.get("OPENAI_API_KEY", "").strip():
        return None
    try:
        from openai import AsyncOpenAI
        client = AsyncOpenAI()
        resp = await client.audio.speech.create(
            model="tts-1",
            voice="onyx",
            input=text[:4096],
            response_format="mp3",
        )
        # Pick ONE based on pre-flight check 5:
        return await resp.aread()    # if AREAD_OK in pre-flight
        # return resp.content        # if CONTENT_OK in pre-flight
    except Exception as e:  # noqa: BLE001
        logger.warning(f"TTS failed: {e}")
        return None
```

   - Constraint: function signature unchanged. Return contract unchanged (mp3 bytes or None). Failure mode unchanged (warning log + None). The only behavior shift: the auth path now requires `OPENAI_API_KEY` rather than `EMERGENT_LLM_KEY` — this matches the EMERGENT_EXIT_PLAN direction.

2. **Verify lazy import is the only `emergentintegrations.llm.openai` reference in `backend/`**:

```powershell
Select-String -Path C:\CHL\backend\*.py -Pattern "emergentintegrations\.llm\.openai" -Recurse
```

   Expected after this stage: zero matches. If any other module imports from `emergentintegrations.llm.openai`, that's an out-of-scope finding — log in completion doc, do not touch.

## Restart and smoke

```powershell
# Restart backend
Stop-Service CHL-Backend
Start-Service CHL-Backend
Get-Service CHL-Backend

# Confirm clean boot
Select-String -Path C:\CHL\logs\nssm\backend.err.log -Pattern "ImportError|ModuleNotFoundError" -Context 0,3 | Select-Object -Last 3
# Expected: nothing recent
```

Smoke battery (no public route, so direct venv call):

- **Direct `_synth` call with OPENAI_API_KEY present**:

```powershell
cd C:\CHL\backend
C:\CHL\.venv-local\Scripts\python.exe -c "
import asyncio, sys
sys.path.insert(0, '.')
from live_wire_diary import _synth
async def go():
    data = await _synth('Stage 3d smoke test. This is a brief test message.')
    print('LEN:', len(data) if data else 'None')
asyncio.run(go())
"
```
  - Expected: `LEN: <number greater than 1000>` (mp3 bytes)
  - Status: green if non-None, non-zero length

- **Direct `_synth` call with OPENAI_API_KEY temporarily absent** (fallback path):

```powershell
$env:OPENAI_API_KEY_BACKUP = $env:OPENAI_API_KEY
$env:OPENAI_API_KEY = ""
C:\CHL\.venv-local\Scripts\python.exe -c "
import asyncio, sys, os
sys.path.insert(0, '.')
from live_wire_diary import _synth
async def go():
    data = await _synth('test')
    print('LEN:', len(data) if data else 'None')
asyncio.run(go())
"
$env:OPENAI_API_KEY = $env:OPENAI_API_KEY_BACKUP
Remove-Item Env:OPENAI_API_KEY_BACKUP
```
  - Expected: `LEN: None` (the fallback path returns None when key absent)
  - Status: green if None
  - Note: env var manipulation is per-shell only; does not affect the running CHL-Backend service.

- **Adjacent regression: `GET /api/ops/briefing?force=true`**
  - Expected: 200, `source: llm`
  - Status: green if 200

## Commit protocol

```powershell
cd C:\CHL
git status
# Expect: only backend/live_wire_diary.py modified

git diff backend/live_wire_diary.py
# Inspect: confirm only the _synth function body changed; surrounding code untouched

git add backend/live_wire_diary.py
git commit -m "iter 139.43 stage 3d: live_wire_diary TTS migration to AsyncOpenAI direct"
git push origin main
```

If `git diff` shows changes outside `_synth`, STOP.

## Completion doc specification

Write `progress/stage_completion/iter_139_43_stage_3d.md` per `completion_template.md`. Include:

- Per-action-step summary, confirming function signature/contract preserved
- Smoke results: both direct calls (with key, without key) and the adjacent regression
- Post-action grep:
  - `Select-String -Path C:\CHL\backend\*.py -Pattern "emergentintegrations\.llm\.openai" -Recurse` — expected: zero
  - `Select-String -Path C:\CHL\backend\live_wire_diary.py -Pattern "AsyncOpenAI"` — expected: at least one
- Note which bytes-extraction path was used (`aread()` vs `.content`) from pre-flight check 5
- Caveats:
  - Auth source changed from `EMERGENT_LLM_KEY` to `OPENAI_API_KEY`. This is the intended direction per EMERGENT_EXIT_PLAN. Operations should confirm `OPENAI_API_KEY` is set in production env.
- Communication protocol applied: state tag at end (`DONE`)

## STOP CONDITIONS specific to this stage

In addition to universal stop conditions:

- Stage 3c commit not present (gate violation)
- Pre-flight check 5 produces both AREAD_FAIL and CONTENT_FAIL (SDK shape unknown)
- Server fails to boot after the change
- Smoke "with key" returns None or zero-byte response (would indicate the new code path doesn't actually generate audio)

## On completion

Update `progress/current_status.md`: mark Stage 3d complete with commit SHA. State `STAGE COMPLETE — awaiting iter close`.

The `close` step for this iter is lightweight — no separate stage instruction file. Operator handles iter-close manually:
- Write `C:\CHL\memory\handoff_iter_139_43_close.md`
- Note any keys exposed in chat during this iter (rotation pass candidate list)
- Update `progress/current_status.md` to `ITER COMPLETE`
