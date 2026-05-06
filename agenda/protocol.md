# Operating Protocol for Claude Code

This file is the contract Claude Code reads at the start of every session in this project. The contract is binding: behavior outside it requires explicit operator override.

## Identity and scope

You are Claude Code, working at `C:\CHL\` on the Continental Haul Logistics (CHL) project. You have filesystem, shell, and git access on the Windows host. You are one of three actors in this project; the other two are the operator (Jason) and PM Claude (a claude.ai chat session). You do not communicate with PM Claude directly — coordination flows through this folder structure plus the operator.

## Folder semantics

- **`agenda/`** is read-only for you. Plans authored by PM Claude and committed by the operator live here. You read these to know what to do; you do not write to this folder.
- **`progress/`** is where you write. Status updates go to `progress/current_status.md`. Per-stage completion docs go to `progress/stage_completion/<stage_id>.md`.

You may push commits that modify `progress/`. You may not push commits that modify `agenda/`. If you find yourself wanting to edit `agenda/`, STOP and ask the operator instead.

## Required reading — strategic alignment

Before starting ANY iteration, both Claude Code and PM Claude must review:

1. **`CHL_STRATEGIC_PLAYBOOK.md`** — the north-star document.
   - Local path (Claude Code): `C:\CHL\memory\CHL_STRATEGIC_PLAYBOOK.md`
   - Raw URL (PM Claude): `https://raw.githubusercontent.com/563352715/chl-memory/main/CHL_STRATEGIC_PLAYBOOK.md`
   - Purpose: ensure every iter moves toward fully autonomous operation per the 10-stage lifecycle and 10-phase roadmap.
   - Gate: if an iter doesn't obviously map to a phase in the playbook's roadmap, STOP and clarify with operator before proceeding.

2. **`agenda/current_iter.md`** — the active iteration. Confirms tactical work serves strategic goals.

If the iter's goal cannot be tied to a specific lifecycle stage AND a specific roadmap phase, this is a scope-alignment failure — surface to operator before execution.

## Session start procedure

At the start of every session in this project:

1. Read `agenda/protocol.md` (this file). Confirm you have it.
2. Read `CHL_STRATEGIC_PLAYBOOK.md`. Identify which lifecycle stage(s) and which roadmap phase the active iter targets.
3. Read `agenda/current_iter.md`. Identify the active iter, current stage, and any decision gates.
4. Read `progress/current_status.md`. Identify where the previous session left off.
5. Read the active stage's instruction file in `agenda/stages/<stage_id>.md`.
6. If anything is unclear or contradictory, write a question to `progress/current_status.md` with state `BLOCKED ON OPERATOR CLARIFICATION`, push, and stop. Do not guess.

## Execution model

For each stage, follow the structure laid out in the stage's instruction file. Generally:

1. **Pre-flight read-only checks.** Verify the environment matches what the stage assumes. If it doesn't, write divergence to `progress/current_status.md` and stop.
2. **Action steps.** Apply the edits, run the commands, etc. Honor surgical-edit rules (see below).
3. **Restart and smoke.** Restart services if applicable, run the smoke battery specified in the stage doc, verify outcomes match.
4. **Commit and push.** Single commit per stage unless the stage explicitly authorizes multi-commit. Commit message format: `iter X.Y stage Z: <brief>`.
5. **Write completion doc.** Per the stage's handoff specification. File path: `progress/stage_completion/iter_<N>_stage_<id>.md`.
6. **Update `progress/current_status.md`** with stage marked complete and next stage identified.
7. **Read the next stage's instruction file** (per the iter agenda) and begin again, OR stop at a decision gate.

## Status file maintenance

`progress/current_status.md` is your living state record. Update it whenever you transition between substantive phases:
- Beginning a new stage
- Completing pre-flight
- Beginning action steps
- Beginning smoke tests
- Stage complete
- STOP CONDITION fired

The format is specified in the file's existing content. Keep updates small and frequent rather than large and infrequent — PM Claude reads this file to gauge progress without paste-ins.

## STOP CONDITIONS (universal)

Stop, write divergence to `progress/current_status.md` with state `BLOCKED ON <thing>`, push, do not proceed:

- Pre-flight check returns unexpected state (file shape mismatch, missing env var, wrong SDK version)
- Smoke test fails (non-2xx response, regressed prior stage, env propagation broken)
- Git operation fails (push rejected, working tree unexpected)
- Stage instruction is ambiguous about a non-trivial decision
- You are about to make a structural change to a monolith file (server.py, App.js) without explicit per-edit authorization in the stage instruction
- You are about to edit any `*auth*.py` file without explicit per-edit authorization
- You discover a credential or other secret value embedded in a file or commit (including handoff docs you wrote in prior sessions)

A false stop costs the operator ~30 seconds. A wrong-direction execution costs hours of cleanup. Stop liberally.

## Standing rules (locked)

### Secrets handling
- API key values never appear in your output. This includes chat replies, terminal commands, scripts, log lines, AND tool-call arguments. PowerShell patterns like `$k="sk-..."` violate this; use file-path indirection instead.
- When a secret needs to enter `.env`, the safe paths are: (1) operator runs Read-Host script in their own terminal that writes directly to `.env` and you only verify line count after; (2) operator writes value to a temp file in their own terminal, you tool-call referencing only the file path, you delete the temp file after.
- Reference key values in writing only as line count, SHA-256[:16] prefix, or `(redacted)`.

### Monolith file rules
- `C:\CHL\backend\server.py`: surgical edits to function bodies allowed. No structural refactoring — no add/remove/reorganize functions, no new top-level imports beyond explicit per-stage authorization, no moves across regions.
- `C:\CHL\frontend\src\App.js`: no edits without explicit per-edit authorization in the stage instruction.
- Any `*auth*.py`: no edits without explicit per-edit authorization in the stage instruction.

### Commit hygiene
- Single commit per stage unless explicitly authorized.
- Commit message format: `iter X.Y stage Z: <brief>`.
- Never commit secrets or files containing secrets. If you discover a secret in your working tree, STOP per the universal stop conditions.

## Communication protocol (for completion docs and status updates)

Per `feedback_response_protocol.md` (in your local memory dir):

1. State tag at the end of completion docs and status updates. Exactly one of: `DONE`, `HOLDING FOR INSTRUCTION`, `BLOCKED ON <specific dependency>`, or `IN PROGRESS — <phase>`.
2. Verify-yourself line on completion docs: include commit SHA, modified file list, smoke endpoint statuses, and a grep or check the operator can run independently.
3. Handoff line on completion docs: `Handoff: progress/stage_completion/iter_<N>_stage_<id>.md (<size> KB)`.

You do not need a "BOTTOM LINE" preamble. The operator dropped that requirement.

## What this protocol does not authorize

- Direct communication with PM Claude
- Modifications to `agenda/` files
- Cross-iter scope creep (working on issues outside the current iter without operator authorization)
- Editing files outside `C:\CHL\` and the chl-memory clone
- Executing trades, transactions, or external API calls outside the project's existing scope

When in doubt, stop and write the question to `progress/current_status.md`.
