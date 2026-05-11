# Orphan Module Cleanup Review (M10) — 2026-05-11

_Analysis only. Zero files deleted. Scope: Python orphans flagged by `asset_inventory_orphans.md`. JS/JSX orphans intentionally out of scope (separate frontend audit needed)._

## Scope filter applied

- Inventory at scan time: 62 ORPHAN entries (52 in backlog blurb; the regenerated count is 62)
- Python-module subset: 10 files
- Excluded by rule: `tests/*` (none in the orphan list anyway), `*auth*.py` (none in the orphan list), `.git/*` (untouched)

## Summary

| Category               | Count |
| ---------------------- | ----: |
| SAFE TO DELETE         |   **0** |
| KEEP — has refs        |     4 |
| KEEP — scaffold/future |     0 |
| REVIEW NEEDED          |     6 |
| **Total examined**     |  **10** |

**Headline:** zero high-confidence safe-deletes. The inventory's "orphan" heuristic (not reachable from `server.py` / `App.js`) generates a lot of false positives for one-shot operator scripts (dotfile-prefixed `.send_*.py`, `.smoke_*.py`, `.run_*.py`) and for pytest's auto-discovery (`conftest.py`). Conservative call: nothing in this pile is safe to delete without operator review.

## SAFE TO DELETE (0)

_None. See "Surprises" below for why every candidate fell out._

## KEEP — has refs (4)

| Path | Refs found |
| ---- | ---------- |
| `C:\CHL\backend\conftest.py` | Pytest auto-loads `conftest.py` at collection-time (no explicit import needed). Fixture `chl_env_info` consumed by `C:\CHL\backend\tests\test_env_isolation.py:50-56`. Load-bearing — refuses to boot tests against prod DB. **The inventory's "no imports" rule is a false negative for pytest conftest.** |
| `C:\CHL\backend\.smoke_3_layer_parsing.py` | Documented as the canonical 3-layer smoke harness in `C:\CHL\chl-memory\architecture\parsing_3_layer_stack_shipped_2026_05_10.md` lines 92, 175, 215 — listed as the run command for fixture-replay verification. |
| `C:\CHL\backend\.run_playwright_ui_sweep_with_vault_creds.py` | Cross-references `.run_playwright_with_vault_creds.py` in docstring as security-model mirror. Also imports `test_playwright_ui_sweep.py` via subprocess (per grep). Hidden dotfile = intentional gitignored operator-runnable. |
| `C:\CHL\backend\.run_playwright_with_vault_creds.py` | Referenced by the UI-sweep sibling script above as the security-model template. Both are vault-decrypt-then-subprocess wrappers — paired pair, not independent orphans. |

## KEEP — scaffold/future (0)

_None of the Python orphans contain docstring markers like "scaffold", "Phase 2 stub", "future", "iter N planned". The send/smoke/run scripts are all complete + operationally ready, just one-shot rather than wired into the LIVE chain._

## REVIEW NEEDED (6)

All six are dotfile-prefixed (leading `.`) Python scripts in `backend/`. The dotfile prefix is the gitignore convention for "intentionally-not-tracked operator-runnable one-shots" — they're DEV-TOOL-class, not true orphans. Inventory should re-classify on next scan; until then, leave on disk.

| Path | Ambiguity |
| ---- | --------- |
| `C:\CHL\backend\.smoke_vault_files.py` | One-shot smoke for vault encrypt/decrypt + folder/file CRUD. Useful for re-verification after schema change. Operator may want to keep as a recoverable smoke runner. |
| `C:\CHL\backend\.send_truckstop_api_inquiry.py` | One-shot outbound email script (operator-mandated 2026-05-11). Likely already sent. Question: do we keep send-once scripts as outbound-history records, or move to `scripts/one_shots_archive/`? |
| `C:\CHL\backend\.send_dat_api_inquiry.py` | Same pattern — one-shot outbound to DAT developer support. |
| `C:\CHL\backend\.send_123loadboard_api_request.py` | Same pattern — one-shot outbound to 123Loadboard partner-integrations. |
| `C:\CHL\backend\.send_comfreight_followup.py` | Same pattern — one-shot follow-up email to ComFreight Haul Pay (factoring app stuck PENDING per current backlog). May need to be re-run if no reply by Friday. **Don't delete — operator may want to fire again.** |
| `C:\CHL\backend\.send_nextload_broker_application.py` | Same pattern — one-shot NextLOAD broker application email. |

## Recommended next actions (for operator)

1. **Patch the orphan scanner** to exclude:
   - Files matching `conftest.py` (pytest auto-loaded)
   - Files matching `backend/\..+\.py` (dotfile-prefixed one-shots — these are intentional DEV-TOOL, not orphans)
   - Files whose path is referenced in any `chl-memory/architecture/*.md` (operator-canonical docs are an authority signal)

2. **Decide retention policy for one-shot email scripts.** Options:
   - (a) Keep in place — dotfile-hidden, gitignored, low storage cost, useful as re-run templates.
   - (b) Move to `backend/one_shots_archive/<YYYY_MM_DD>_<name>.py` after the email is confirmed sent. Keeps backend root tidy.
   - Recommendation: (a) for now (under 50 KB total). Revisit at iter 150.

3. **Re-run the inventory** after patching the scanner heuristics — expect Python orphan count to drop from 10 to ~0.

## Surprises

- **`conftest.py` mis-flagged as orphan.** This is a load-bearing pytest auto-loaded module, not just imported-from-nowhere. The orphan-scanner needs a pytest-aware special case.
- **All 6 "REVIEW" Python files are dotfile-prefixed.** They're already filesystem-hidden + gitignored — the orphan-scanner is finding them because it walks `os.listdir`-style. The scanner could probably skip `\..*` automatically.
- **The 3-layer-parsing smoke is referenced in canonical architecture docs.** If chl-memory architecture docs reference a file, that's a strong "do not delete" signal the current heuristic misses.
- **Backlog blurb said 52 orphans; current inventory shows 62.** Drift between when the M10 ticket was filed and now (additional dotfile scripts added 2026-05-10/11). Not concerning, just a count-drift note.
- **Zero true Python orphans.** The cleanup value here is mostly in fixing the scanner, not deleting code.

## Files referenced

- `C:\CHL\chl-memory\research\asset_inventory\asset_inventory_orphans.md` (source)
- `C:\CHL\chl-memory\research\asset_inventory\asset_inventory.json` (source)
- `C:\CHL\chl-memory\architecture\parsing_3_layer_stack_shipped_2026_05_10.md` (authority for smoke_3_layer_parsing)
- `C:\CHL\backend\tests\test_env_isolation.py` (consumer of conftest fixture)

---

_Reviewer: dev-engineer Claude. Conservative classification. No files deleted. Operator review required before any orphan reaches a deletion stage._
