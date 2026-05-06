# AgentDM Bug Report — Identity Binding Failure

**Filed:** 2026-05-06 (initial submission)
**Account email:** `dispatch@continentalhaul.com`
**Reporter:** Jason Aaron Meyer / Continental Haul Logistics LLC
**Severity:** High (agent-to-agent messaging unusable on this account)

---

## Subject

API key rotations for second agent all authenticate as the first agent (server-side identity binding bug).

## Summary

I have two agents on my account — `@pm-lead` and `@dev-engineer`. Any API key generated for `@dev-engineer` authenticates server-side as `@pm-lead` instead. Rotating the `@dev-engineer` key on the dashboard does not change this — every newly-issued key also auths as `@pm-lead`.

## Evidence

1. **4 distinct rotations of the `@dev-engineer` key, all bind to the wrong identity.** SHA256[:16] prefixes of the rotated keys (none currently compromised; all behaviorally bound to wrong identity):
   - `B10C3A3EBF0E5353`
   - `F64417461735DE0B`
   - `C6B78BC5D835E387`
   - `2C3197C799CD6D7E`

2. **Every one of those 4 keys**, when presented in `Authorization: Bearer ...` header against the AgentDM MCP endpoint, returns an identity of `@pm-lead`.

3. **The `@pm-lead` key** (separate, never rotated) correctly auths as `@pm-lead` — so the identity lookup *can* return the right value when it has the right binding.

4. **Round-trip failure mode:** when `@pm-lead` (PM Claude on claude.ai) attempts to send a message to `@dev-engineer`, the AgentDM service returns a `self_message` error — confirming both sides resolve to the same identity at the server. This rules out client misconfiguration; the client is sending the right key, but the server registers both keys as the same agent.

5. **Theories ruled out:**
   - **Click-on-wrong-row at rotation time** — ruled out by rotating the same row repeatedly with the same outcome
   - **Dashboard row-label swap** — verified that the labels in the dashboard match the expected agents
   - **Mis-pasting at the client** — each rotation was tested independently with a fresh paste using the operator's chat-paste protocol

## Expected behavior

Each agent's API key should authenticate as the agent it was issued for. The `@dev-engineer` agent should be a distinct identity from `@pm-lead` in the AgentDM identity lookup.

## Hypothesis (server-side)

The `@dev-engineer` agent on this account appears to either:
- (a) not be registered server-side as a distinct identity, OR
- (b) be shadowed by `@pm-lead` in the identity lookup (e.g., a stale binding row or a foreign-key collision)

## Impact

Agent-to-agent messaging is unusable on this account. The operator is working around this via a non-AgentDM file-based bridge (chl-memory git repo + raw URL fetches), which works but adds friction and is not the architecture AgentDM is designed to deliver.

## Reproduction (server-side)

Anyone with access to the AgentDM identity-binding tables for account `dispatch@continentalhaul.com` should be able to:

1. Look up the agent records for `@pm-lead` and `@dev-engineer`. If `@dev-engineer` is missing or has a `pm-lead` foreign key in its identity row, the bug is server-side as hypothesized.
2. Look up the most recent API key issued for `@dev-engineer`. The key's `agent_id` field should point at `@dev-engineer`'s record. If it points at `@pm-lead`'s record instead, that's the binding bug.
3. Verify by curl: presenting any of the 4 rotated keys above should return an identity object identifying the agent. If all 4 return `@pm-lead`'s identity, that's the symptom.

## Reproduction (client-side, if you want me to demonstrate)

Available on request. Operator can rotate the `@dev-engineer` key one more time during a screenshare, then run a test call to whichever endpoint reflects the resolved identity, and show that the new key auths as `@pm-lead` even though the dashboard shows it as `@dev-engineer`'s.

## Account / contact

- **Account email:** `dispatch@continentalhaul.com`
- **Operator:** Jason Aaron Meyer
- **Company:** Continental Haul Logistics LLC (MC-1817555 — pending FMCSA authority finalization ~2026-05-13)
- **Preferred contact:** email reply or scheduled 1:1 (appointment booked with Alex 2026-05-06)

## Workaround in place (for AgentDM team's information)

While this bug persists, all PM Claude ↔ Claude Code coordination flows through the operator's `chl-memory` public GitHub repository (`https://github.com/563352715/chl-memory`) using:
- `agenda/current_iter.md` — operator-committed iter plan
- `agenda/stages/stage_*.md` — per-stage instructions
- `progress/current_status.md` — Claude Code-written live state
- Raw GitHub URLs for cross-agent fetching

This works (5 iters / 14 stages / 70-of-72 smoke tests / 0 stop-conditions fired) but adds operator-side paste round-trips and per-fetch failure modes. The AgentDM-resolved bridge would replace this with direct MCP messaging.

---

**Document maintained at:**
- Private repo: `C:\CHL\memory\AGENTDM_BUG_REPORT.md`
- Public mirror: `https://raw.githubusercontent.com/563352715/chl-memory/main/AGENTDM_BUG_REPORT.md`

If AgentDM team prefers a different format, please reply with what you'd like to see and I'll restructure.
