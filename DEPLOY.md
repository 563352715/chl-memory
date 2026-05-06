# DEPLOY — installing the agenda/progress structure into chl-memory

## Prerequisites

- `chl-memory` public repo already exists at https://github.com/563352715/chl-memory
- You have a local clone, or you'll create one
- You have push access (you do — you've been pushing to it)

## When to deploy

After iter 139.42 closes cleanly:
- OpenAI key rotated, new key in `.env`
- Stage 3a swap committed and smoke green
- Old key value scrubbed from `handoff_iter_139_42_stage_3a_plain_text_llm_swaps.md`
- Final iter handoff committed and pushed

Until then, leave this bundle on disk unused.

## Install

```powershell
# 1. Clone or pull chl-memory
cd C:\CHL
if (-not (Test-Path "chl-memory")) {
    git clone https://github.com/563352715/chl-memory.git
}
cd chl-memory
git pull origin main

# 2. Copy the bootstrap structure in (assumes you extracted the bundle to C:\CHL\chl-memory-bootstrap)
Copy-Item -Path "C:\CHL\chl-memory-bootstrap\*" -Destination "." -Recurse -Force

# 3. Review the structure
git status
git diff

# 4. Commit
git add README.md DEPLOY.md agenda/ progress/
git commit -m "iter 139.43 stage 0: install agenda/progress coordination structure"
git push origin main

# 5. Verify the raw URLs are accessible (PM Claude will fetch from these)
# Open in browser or curl:
# https://raw.githubusercontent.com/563352715/chl-memory/main/agenda/protocol.md
# https://raw.githubusercontent.com/563352715/chl-memory/main/progress/current_status.md
```

## First-iter checklist (after install)

Before launching Claude Code on the first agenda-driven iter:

1. **Edit `agenda/current_iter.md`** with the actual iter number and stages. The committed version is a template — fill in real content for iter 139.43 (or whatever you're starting).
2. **Edit each `agenda/stages/stage_<id>.md`** with the comprehensive instruction for that stage. Use the template as the structure.
3. **Reset `progress/current_status.md`** to the awaiting-start state (it's already that way in the bootstrap).
4. **Commit and push.**
5. **In Claude Code chat, send one message:**
   > Read `https://raw.githubusercontent.com/563352715/chl-memory/main/agenda/protocol.md`. That is the operating contract for this project. Then read `https://raw.githubusercontent.com/563352715/chl-memory/main/agenda/current_iter.md` for the active iter. Then read the first stage file referenced there. Begin executing per protocol. Write progress to `progress/current_status.md` and per-stage completion docs to `progress/stage_completion/`.
6. **Step away.** Monitor optionally; respond to STOP CONDITIONS if they fire.

## When something goes wrong

If Claude Code gets confused about the protocol, or behaves outside the agenda, the recovery move is:
1. Tell Claude Code to STOP, write current state to `progress/current_status.md`, and HOLD.
2. Come to PM Claude chat with the raw URL of `progress/current_status.md`.
3. PM Claude reads, diagnoses, emits a fix or revised stage instruction.
4. Operator commits the fix to `agenda/`, tells Claude Code to re-read and resume.

## Cleanup between iters

After an iter closes:
1. Move `agenda/current_iter.md` to `agenda/archive/iter_<N>_<title>.md` (create archive/ on first use).
2. Move stage files in `agenda/stages/` to `agenda/archive/stages/iter_<N>/`.
3. Move completion docs in `progress/stage_completion/` to a similar archive structure under `progress/archive/`.
4. Reset `progress/current_status.md` to the awaiting-start state.
5. Commit: `iter <N+1> stage 0: archive iter <N>, prepare iter <N+1>`.
6. Begin next iter planning.
