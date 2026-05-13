# Emergent Dependency Audit — 2026-05-13 01:55Z

Per [[post_launch_emergent_exit.md]] Step C — sweep audit for emergent-only execution paths.

## Files still importing `emergentintegrations`

```
backend/server.py
backend/tests/test_stripe_residual_cleanup.py
backend/load_completion_workflow.py
backend/rfq_inbound.py
backend/boot_briefing.py
backend/live_wire_diary.py
backend/rfq_concierge.py
```

**7 files.** All are LIVE backend modules (except the test) — they need migration to direct Anthropic SDK before we can safely retire `app.emergent.sh`.

## What this blocks

- `app.emergent.sh` cannot be shut down yet — these imports would fail on Hetzner if the Emergent pod runs them. On Hetzner the modules likely fall back to Python templates (per `boot_briefing.py` pattern) but RFQ AI parsing + load completion workflow may silently degrade.

## What's safe to do BEFORE migration

- Hetzner deployment serves the marketing site + public API just fine; doesn't need Emergent.
- Inbox manager, public_site_watcher, bot_protection, hopper_pricing — all NEW modules built tonight — have ZERO emergent dependencies.
- LLM routing for new code goes through `local_llm_helper.get_router()` which uses Ollama (Llama/Phi-3/Qwen/DeepSeek) — no Emergent dependency.

## Recommended sequence (post-launch first week, 5/14-5/20)

1. Add `ANTHROPIC_API_KEY` to backend `.env` on Hetzner (was already done locally per session memory). Pull from Vault.
2. Build `backend/anthropic_client.py` — direct AsyncAnthropic wrapper matching the `emergentintegrations.llm.chat` call signature.
3. Migrate the 7 files one-by-one, smallest first:
   - `boot_briefing.py` (already has Python-template fallback — lowest risk)
   - `live_wire_diary.py`
   - `rfq_concierge.py`
   - `rfq_inbound.py`
   - `load_completion_workflow.py`
   - `server.py` (largest — last)
4. Per file: confirm pytest passes + smoke test the affected endpoint
5. Once 7th file migrated + 1 week of zero Emergent traffic in logs: shut down Emergent pod, bank ~1200 credits

## Risk of doing it tonight

- Single deploy with all 7 files touched = high blast radius
- Backend startup imports server.py first — if migration introduces an import error, backend crashes
- Watchdog (120s grace) would catch + restart but if it crashes on import every time, watchdog can't fix it
- Better: do the migration during a planned operator-attended window

## What I'm doing instead tonight

Deferring to plan. Focusing on operator-mandated builds that DON'T touch the emergent code paths (already shipped 4 of them tonight + 49 tests passing).
