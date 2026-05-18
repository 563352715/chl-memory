# Architect ↔ Claude-Code Bridge

**Established 2026-05-17.** This folder is the canonical interface between
the **Senior Architect** session (a Claude.ai chat OR a separate Claude
Code instance at `C:\CHL-pm-agent\` — operator's choice) and the
**dev-engineer Claude Code** session at `C:\CHL\` that has actual tool
access (filesystem, MongoDB, terminal, git, services).

## Why this exists

On 2026-05-16/17, the Architect (operating from a Claude.ai chat tab)
made **5 consecutive operational claims that were fabricated** — claiming
to have created files, started syncs, moved data, and observed record
counts that did not exist. The Architect cannot do any of those things
from a browser chat: it has no filesystem, no shell, no Mongo connection.

The operator burned ~2 hours chasing those claims. This bridge formalizes
the boundary so it doesn't happen again.

## The two roles

| Role | Where it runs | Can do | Cannot do |
|---|---|---|---|
| **Architect** | Claude.ai browser tab OR Claude Code @ `C:\CHL-pm-agent\` | Design, propose, review, reason, draft specs, write proposal files (if Claude Code) | Read CHL's live state from a browser tab; "create files" on operator's machine from a browser tab |
| **Dev-engineer Claude** | Claude Code @ `C:\CHL\` (this window) | Run shell, edit files, query Mongo, restart services, commit code, verify ground truth | Operate without operator authorization on destructive ops |

## The Bridge Protocol

1. **Architect writes a proposal** as one Markdown file in this folder:
   ```
   chl-memory/architect_bridge/proposal_<YYYY-MM-DD>_<short-slug>.md
   ```
   Required sections:
   - **Goal** — what outcome we want
   - **Hypothesis / design** — how Architect thinks we get there
   - **Verifiable claims** — any factual assertion ("we have 500K shippers")
     must include "VERIFY: <command or query that grounds this>"
   - **Asked action** — what Architect wants the dev-engineer to actually
     do (build, run, test, commit, etc.)
   - **Stop conditions** — what should make dev-engineer abort / escalate
2. **Architect commits the proposal** to the `chl-memory` repo and pushes.
   - If Architect is Claude.ai chat: it CAN'T commit. Operator copy-pastes
     the proposal into a new file in this folder and commits manually.
   - If Architect is Claude Code @ `C:\CHL-pm-agent\`: it commits directly.
3. **Dev-engineer Claude pulls + reads** the proposal at the start of the
   next operator interaction.
4. **Dev-engineer verifies** every claim in the "Verifiable claims" section
   against ground truth (filesystem, Mongo, git) BEFORE acting on any
   "Asked action".
5. **Dev-engineer writes a response** in the same folder:
   ```
   chl-memory/architect_bridge/response_<YYYY-MM-DD>_<short-slug>.md
   ```
   With:
   - **Verification results** — which claims checked out, which were false
   - **Action taken** — what was actually built / changed / committed
   - **Open questions** — what the Architect needs to clarify

## Rule: Design-board, not Action-reporter

**The Architect MUST NOT use action-verbs in past or progressive tense
about operator's machine.** ❌ Bad: "I created the file." "Sync is
running." "I moved records into the DB."
✅ Good: "We should create a file at..." "A sync cron could be wired
to..." "If we add records to shipper_targets, the count would..."

The dev-engineer Claude is the only entity that operates the platform.
Architect proposes; dev-engineer executes; operator authorizes.

## Why chl-memory (not chl directly)?

`chl-memory` is a SEPARATE git repo from `chl`. PM Claude (at
`C:\CHL-pm-agent\`) already uses it as the Tier-2 communication channel
for filesystem-level state without polluting the CHL code repo with
proposals + responses. The Architect bridge follows the same pattern.

## File naming

```
proposal_2026-05-17_500k-shipper-import.md
response_2026-05-17_500k-shipper-import.md
proposal_2026-05-18_tx-oh-corridor-priority.md
response_2026-05-18_tx-oh-corridor-priority.md
```

Date in ISO. Short slug describes the topic. Same slug ties a proposal
to its response.

## What does NOT belong here

- Operational status reports (those go in `chl-memory/progress/`)
- Live system health (that's `BOOT_BIOS.md` + `cron_heartbeats` Mongo)
- Code changes (those go in `chl` repo)
- Secret values, credentials, or anything that would violate the standing
  "secrets never cross AgentDM / sub-agent prompts / chl-memory commits"
  rule (per `CLAUDE.md`)
