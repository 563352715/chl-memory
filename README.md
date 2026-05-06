# CHL agenda/progress coordination system

This folder structure is an asynchronous coordination channel between PM Claude (claude.ai web/mobile) and Claude Code (VS Code on the CHL Windows host). The operator (Jason) authorizes plans and monitors; Claude Code executes; PM Claude plans and reviews. The folders are the message passing surface.

## Why this exists

Without it, every stage transition required the operator to copy a handoff doc out of Claude Code's chat into PM Claude's chat, then copy the next stage's instruction back. Roughly 2 paste cycles per stage, plus all the formatting friction.

With it, the operator commits a multi-stage agenda once at the start of an iter, then Claude Code chains stages by reading the next stage's instruction file when the current one completes. PM Claude monitors progress by web-fetching files in `progress/` rather than waiting for paste-ins.

## The two channels

**`agenda/`** — Plans authored by PM Claude, installed by the operator, read by Claude Code.

PM Claude has no direct write access (no git push capability), so the install step is: PM Claude generates content in chat, operator commits to `agenda/`. One push per iter, typically at iter start.

**`progress/`** — Status and completion docs authored by Claude Code, read by PM Claude via raw URL.

Claude Code has direct git push access. It writes `current_status.md` as it works, and a per-stage completion doc when each stage finishes. PM Claude fetches these on demand.

## What does NOT change with this system

- No autonomous channel between PM Claude and Claude Code. Both are still stateless across sessions; neither initiates communication. The folders are passive — read on demand, written on action.
- API keys are unrelated to this. The keys in `C:\CHL\backend\.env` are runtime credentials for the FastAPI service. The coordination here uses git over HTTPS.
- The operator is still in the loop at iter boundaries (planning, agenda commit) and at any STOP CONDITION fired during execution. What's removed is the per-stage paste cycle.

## Files

- `README.md` — this file
- `DEPLOY.md` — how to install this structure into your existing chl-memory repo
- `agenda/protocol.md` — operating contract Claude Code reads at start of every session in this project
- `agenda/current_iter.md` — top-of-iter document; replaced/archived per iter
- `agenda/stages/stage_template.md` — template for per-stage instruction files
- `progress/current_status.md` — live status, overwritten by Claude Code as it works
- `progress/stage_completion/completion_template.md` — template for per-stage completion docs

## Workflow at a glance

1. PM Claude and operator plan the iter in chat.
2. PM Claude emits `current_iter.md` content plus per-stage instruction files.
3. Operator commits to `agenda/`. One git push.
4. Operator tells Claude Code: "Read `agenda/protocol.md`, then `agenda/current_iter.md`, then begin."
5. Claude Code executes stages serially, writing to `progress/current_status.md` as it works and `progress/stage_completion/<stage>.md` at each completion.
6. STOP CONDITIONS pause execution; operator handles, Claude Code resumes.
7. When operator returns to PM Claude chat, PM Claude fetches `progress/current_status.md` and any new completion docs to catch up.

See `DEPLOY.md` for installation, `agenda/protocol.md` for the execution contract.
