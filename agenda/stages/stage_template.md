# Stage <ID>: <one-line goal>

> Template — copy this to `stage_<id>.md` and fill in. Sections marked OPTIONAL may be removed if not applicable.

## Stage objective

One paragraph. What this stage accomplishes, why it's bounded this way, and what success looks like. Reader should be able to determine "did this work" from this paragraph plus the smoke section.

## Pre-flight checks (read-only)

Before applying any edits, verify reality matches assumptions. Each check has an expected outcome and a failure path.

```powershell
# Example check 1
<command>
# Expected: <outcome>
# On mismatch: STOP and write divergence to progress/current_status.md
```

Continue with as many checks as needed. Common categories:
- File shape (line counts, function signatures present)
- Environment (env vars, SDK versions, service status)
- Git state (clean tree, expected branch, no unrelated changes)
- Reference docs read (Stage 2 swap as a model, etc.)

## Action steps

Numbered, surgical. Each step says exactly what to change and where. Use file:line references.

1. **<Description>** — `path/to/file.py:<line range>`
   - OLD: <signature or pattern>
   - NEW: <exact replacement>
   - Constraint: <surgical-edit rules, fallback semantics, etc.>

2. **<Description>** — ...

Cite the surgical-edit rules from `agenda/protocol.md` that apply. If this stage requires an exception (e.g., adding a top-level import to `server.py`), explicitly authorize it here in writing — Claude Code will look for that authorization and refuse to proceed without it.

## Restart and smoke

```powershell
# Restart commands
Stop-Service CHL-Backend
Start-Service CHL-Backend
Get-Service CHL-Backend
# Wait for FastAPI to bind: ~3-5 seconds
```

Auth note: cookie is `Secure; SameSite=none`, won't forward over `http://`. Use `Authorization: Bearer <token>` from `/api/auth/login` response (`server.py:102-104` supports this fallback).

Smoke battery:
- **<Endpoint or function>** — expected status, expected shape, regression check or new-behavior check
- ...

If a smoke target requires populated test data and none is available, defer it explicitly with a note in the completion doc: "deferred — exercised by next live X cycle." Do not skip silently.

## Commit protocol

```powershell
git status
# Expect: only the files listed in §Action steps modified
git add <specific files, not -A>
git commit -m "iter <N> stage <id>: <brief>"
git push origin main
```

If `git status` shows unexpected modifications, STOP. Do not bundle unrelated changes into this commit.

## Completion doc specification

Write `progress/stage_completion/iter_<N>_stage_<id>.md` with:

- Per-action-step summary: file:line, before signature, after signature, constraints honored Y/N
- Smoke results per target: status code, payload shape verification, deferred items with explicit reasons
- Post-action grep or other check showing the expected residual state
- Caveats and known-deferred items
- Communication protocol applied: state tag at end, verify-yourself line, handoff filename + size

## STOP CONDITIONS specific to this stage

In addition to the universal stop conditions in `agenda/protocol.md`:

- <stage-specific divergence #1>
- <stage-specific divergence #2>
- ...

## On completion

Update `progress/current_status.md`: mark this stage complete, identify next stage from the iter agenda, transition to that stage's instruction file. Or, if this is the final stage of the iter, transition to iter-close handling per the iter's `close` stage.
