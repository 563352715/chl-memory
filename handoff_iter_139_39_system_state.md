# Iter 139.39 — Operator Status-Pull Endpoint

**Date:** 2026-05-05
**Spec:** `GET /api/admin/system-state` returns a complete operator status snapshot as raw Markdown so Jason can copy-paste it into a fresh Claude.ai chat at the start of each session and orient PM Claude without typing anything else.

---

## What was built

Single-purpose endpoint (no scope creep):

- **Route:** `GET /api/admin/system-state` (broker-auth required)
- **Response:** `Content-Type: text/markdown; charset=utf-8` — raw markdown, NOT JSON-wrapped
- **Cache:** 30-second TTL (in-process). `?force=true` bypasses.
- **Cache observability:** `X-System-State-Cache: HIT|MISS` response header
- **Performance:** ~2.0s cold, <50ms warm (TTL hit). Within the <3s spec.
- **Resilience:** Every section is independently wrapped — failure in one section renders `_(unable to compute: <reason>)_` instead of erroring the whole response.

### Sections rendered (in order)

1. **Identity** — MC / DOT / Authority / Mode / days-to-grant / current iter (parsed from SESSION_LOG → PRD → git log fallback chain)
2. **Day-1 Gates** — `{passed}/{total}` table with status emoji + threshold hint (sourced from `ops_state.compose_state()`)
3. **Last 5 Git Commits** — via `git log -5 --pretty=...`
4. **Doctrine Parity** — emoji counts (✅/⚠️/🚧) parsed from `memory/OPERATING_MANUAL.md`
5. **Cron Health** — table of 11 known loops with last-tick timestamp (proxied via `agent_journal`), expected cadence, and status (✅ healthy / ⚠️ stale / 🚨 missed)
6. **Recent CRITICAL Events (24h)** — `agent_journal` where `level=critical`, last 10
7. **Recent dispatch_audit (last 10 actions)** — table from `dispatch_audit` collection
8. **Recent Synthetic Activity (24h)** — spawn count, by-stage breakdown, last spawn
9. **Active Margin Alerts** — non-empty `margin_alerts`, status not paid/cancelled, non-synthetic
10. **Integration Status** — pulled from `ops_state.integrations` (canonical) + FMCSA local cache row count
11. **Open Findings** — parses any `memory/*findings*.md` / `*walkthrough*.md` for unresolved P0/P1/P2 items
12. **Files in memory/ (most recent 15)** — `ls -t` style with size + mtime
13. **Active Schedulers (registry)** — count from `CRON_REGISTRY`
14. **Open Loops Awaiting Action** — `EXTERNAL_BLOCKERS` and `OPERATOR_DECISIONS_PENDING` static lists (manually maintained inline; easy to extend)

---

## Files touched

**New:**
- `backend/system_state_router.py` (~480 LoC)

**Edited:**
- `backend/server.py` — added `include_router` block right after shipper_scorecard mount (~line 9239)
- `memory/PRD.md` — appended Iter 139.39 changelog entry (separately by main agent)
- `memory/SESSION_LOG.md` — appended Iter 139.39 entry (separately by main agent)

---

## Test results

```bash
# Unauth: returns 401 ✅
$ curl -o /dev/null -w "%{http_code}" "$API_URL/api/admin/system-state"
401

# Auth (PIN cookie): returns 200 + markdown ✅
$ time curl -s -w "HTTP=%{http_code} CT=%{content_type} BYTES=%{size_download}" \
       "$API_URL/api/admin/system-state" -b /tmp/cookies.txt -o /tmp/out.md
HTTP=200 CT=text/markdown; charset=utf-8 BYTES=9294
real    0m2.044s

# Cache MISS on first, HIT on second within 30s ✅
$ curl -sI "$API_URL/api/admin/system-state" -b cookies | grep -i x-system
x-system-state-cache: HIT

# Force bypass ✅
$ curl -sI "$API_URL/api/admin/system-state?force=true" -b cookies | grep -i x-system
x-system-state-cache: MISS
```

Sample output validated: every section rendered cleanly with real data (Day-1 4/8 gates, 11 cron rows with mixed health, 10 critical events, 10 dispatch_audit rows including margin_hard_block events from Iter 139.37 testing, last synthetic load from Iter 139.38 walkthrough, integrations table including FMCSA local cache 452,948 rows). No section degraded to `_(unable to compute)_`.

---

## Deviations from spec

**None of substance.** Two implementation notes that match the spec's "graceful" guidance:

1. **No central scheduler registry exists** — the spec acknowledged this with "use whatever heartbeat data is currently available." Implemented a static `CRON_REGISTRY` list inside `system_state_router.py` mapping each known loop to (cadence, action_prefix). Last-tick is queried via the most recent `agent_journal` row matching that prefix. A loop with no journal entry shows as "🚨 missed (no record)". TODO comment added inline pointing at `cron_scheduler.start_all()` for the follow-on heartbeat wiring.
2. **External blockers + operator decisions** are hardcoded lists at module top (per spec: "pulled from a config file or hardcoded list initially"). Easy to extend — top of `system_state_router.py`.

---

## Limitations the next Claude should know

1. **Cron health is heuristic**, not authoritative. A loop that only journals when it does work (e.g. `compliance_watcher` only writes a journal row on a *status change*, not every tick) will show as missed even when running normally. Real heartbeat wiring is the next iteration.
2. **`Open Findings` parser** — looks for `memory/*finding*.md` files matching `[P012]` tags. If Jason adds a findings doc with a different naming convention, won't be picked up. Glob pattern is at the top of `_render_open_findings`.
3. **30s cache is in-process only** — multiple gunicorn workers would each have their own cache. This is a single-process FastAPI app today, so fine. If we ever add workers, swap `_CACHE` for a Redis/`db.system_state_cache` backed entry.
4. **Doctrine parity counter** is naive — it counts every ✅/⚠️/🚧 character in `OPERATING_MANUAL.md`, including ones in narrative text. Fine for trend signal, slightly over-counts vs. table-row counters. The PRD's "Doctrine totals" line remains the authoritative number for reporting.
5. **No frontend** — the endpoint is curl/copy-paste only by design. If Jason wants a "Copy state" button later, wiring it onto `/ops` is one button.

---

## How to use (Jason workflow)

```bash
# At the start of each session:
$ curl -s "$BACKEND/api/admin/system-state" -b cookies.txt | pbcopy
# Paste into a fresh Claude.ai chat. PM Claude is now fully oriented.
```

Or directly in browser when logged in:
```
https://work-remote-search.preview.emergentagent.com/api/admin/system-state
```
(browser will render as plain text; select-all + copy.)
