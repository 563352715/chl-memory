# EMERGENT_EXIT_PLAN.md
**Purpose:** Migrate Continental Haul Logistics off `app.emergent.sh` as an active dependency. Bank remaining ~1,200 credits as runway. Move Emergent to "available if needed" status.
**Drafted:** 2026-05-05 (T-8 days to FMCSA grant)
**Timing:** Post-launch first week (5/13–5/20).
**Owner:** PM Claude + Claude Code. No Emergent required for any migration step.

## Why exit

- Emergent is paid per credit; ~1,200 remaining; finite resource.
- Claude Code is free under Max plan; covers all of Emergent's daily-work surface.
- Three Emergent-specific paths exist in the codebase that must be replaced before Emergent can be safely retired.

## Three steps

### A. Migrate LLM integrations off `emergentintegrations`
- The package only exists on Emergent's pod. Locally, `boot_briefing.py` falls back to Python templates.
- `boot_briefing.py` already targets `claude-sonnet-4-5`. Swap the import from `emergentintegrations.llm.chat` to direct Anthropic API.
- Add `ANTHROPIC_API_KEY` to vault. Adapt call signature (LlmChat → Anthropic client).
- Apply the same pattern to RFQ AI parsing (`rfq_imap_poller.py` + helpers).
- Verify LLM rendering returns `"source": "llm"` not `"fallback"` on local backend.
- Effort: ~2 Claude Code sessions.
- Post-launch because boot_briefing has a working fallback; not launch-blocking.

### B. Establish Claude Code as test-runner of record
- Convention: every code-touching iteration ends with `pytest -x` (or scoped equivalent) and results block in the response.
- Codify in `feedback_response_protocol.md` (append).
- Removes reliance on `testing_agent_v3_fork`.
- Effort: ~30 minutes.

### C. Sweep audit for Emergent-only execution paths
- Search backend for `from emergentintegrations` imports.
- Search for hardcoded references to Emergent filesystem paths.
- Search for assumptions about Emergent's compute environment (memory, egress, system tools).
- Catalog and replace.
- Effort: ~1 Claude Code session.

## Total
~3-4 sessions across post-launch first week. All Claude Code work — no Emergent credits consumed by the migration itself.

## End state
- Emergent moves from active dependency to "available if needed."
- ~1,200 credits banked as future emergency runway.
- No daily Emergent reliance; CHL operates fully on local stack + Claude Code + direct Anthropic API.

## Post-migration verification
- `boot_briefing.py` returns `"source": "llm"` on local backend.
- `pytest` runs end-to-end on every iteration via Claude Code.
- Zero `emergentintegrations` imports remain in backend.
- `app.emergent.sh` not visited for routine work.
