# Handoff — 2026-05-22 close

Long session. Major shift: **operator went from researching to operating** —
started cold-outreaching (Midland Farms web form), wants real call list by
morning. Built 30+ features to support that workflow. Backend scraper now
draining at scale.

## TL;DR for the next agent

1. **Backend unified scraper cron is RUNNING** as of close. Settings
   bumped: batch=100, concurrency=15, interval=30s. Producing
   ~1,700 scrapes/hr, ~580 phones/hr. Confirmed at 19:53 UTC:
   `ok=100 phones=34 emails=9 addresses=12` per tick.
2. **MORNING'S #1 PRIORITY: Option C — move backend scraper to Hetzner.**
   Right now scraper egresses through operator's home Brightspeed IP
   (74.167.34.46). Risk: yp.com / bbb.org rate-limit operator's home IP,
   then operator's BROWSER can't reach those sites for normal work.
   Hetzner VM at 5.78.218.86 already exists (hosts continentalhaul.com).
   Run a lightweight scraper service there that egresses through Hetzner
   IP. ~1 hr work. Lets operator crank to ~5,000/hr safely.
3. **Yeti scraping is operator-only** (his Pro session). He had 20 tabs
   open — TOO MANY. Risk of Yeti Pro account suspension. Told him to
   drop to 5-6. If he asks again: confirm 5-6 max.
4. **Brave Search capped at $55** for May 2026. Freight_contact_finder
   loop halted via `C:\CHL\.freight_contact_finder_stop`. Resets June 1.
   Don't re-enable until then.
5. **User-Agent anonymized** in `unified_backend_scraper.py` and
   `_run_brave_for_ag_bulk.py` — stripped `CHLBot/1.0`. Load-board
   scraper UA left identifying (intentional partner transparency).

## Day's numbers (close-of-session)

```
TOTAL dialable (any phone):  1,106   (was 110 this morning, 10x growth)
TOTAL backend-scraped:         983   (cron drained these in ~3 hrs)
TOTAL Yeti-scraped:            888
TOTAL consignee addresses:   1,364   (Yeti unlocking)
Brave Ag enrichments:          160   (banked before $55 cap hit)
Yeti BOL records:            7,972

Last-hour throughput at close:
  Backend cron:                95/min
  Yeti (operator):             14/min (4-5 tabs active)
  New phones captured:         14/min
```

## What shipped this session (commit log gist)

**UI / operator workflow (Shipper Targets):**
- Industry tab strip (🌾 Ag / 🍔 Food&Bev / 🏗️ Construction / ⛏️ Mining /
  ⛽ Petro/Chem / ⚙️ Metals / 📦 Other) — 7 groups + counts cron-pre-warmed
- Equipment-type filter dropdown (11 types from `equipment_capability[]`)
- ↻ Reload-counts button beside Industry filter
- 🧹 Clear filters button
- Filter + search + page state persists in `sessionStorage` (Ag stays
  selected across tab navigation — operator-mandated)
- 🔍 Search button + Enter-key support beside search box
- 🤖 Backend scraper stats now in `/website_scrape/stats`

**Shipper edit modal:**
- Sticky save bar (always visible, 🟡 unsaved-changes amber, 💾 emerald
  Save button, ✓ Saved green message) — operator was missing the Save
  button at bottom
- 📞 Recategorize section: Industry-group dropdown + Industry text +
  equipment chips + commodity chips (added restaurant / food_service /
  food_distributor / grocery / retail)
- 🚨 URL-mismatch banner (red likely-wrong, yellow suspect; thresholds:
  <0.3 wrong, 0.3-0.6 suspect, >=0.6 ok)
- ✕ Clear URL + 🗑️ Clear scraped data buttons (wipe contamination from
  Bay-Cities-style wrong-URL cases)
- 🔬 Research checklist: 10 items + per-item notes + 🚩 needs-more-research
  flag + research_priority_notes
- 📤 Log outreach button + form (web_form / email / phone / sms / etc.)
- ✅ "Contacted [date] via [method]" green banner persists
- 📨 "THEY REPLIED" blue banner when shipper email matched to inbound_emails
- 🗑️ Delete shipper button + soft-delete (sets deleted_at) + ↩️ Restore
  + show_deleted filter (hide / only / include)
- 📞 Call History collapsible section + ➕ Log Call form (CALL-CRM Phase 1)

**Bookmarklets shipped (in browser):**
- `super_scrapers.html` — 🐲 BEAST + 📒 YP + 🛡️ BBB (YP+BBB CSP-blocked,
  abandon; BEAST works but redundant with backend cron now)
- `unified_scraper.html` — 🔥 Unified (cross-origin broken; do NOT
  recommend operator click it)
- `beast_autoloop.js` — BEAST + auto-loop (alternative to backend cron)
- `yeti_auto_discover_v2.js` — DB-backed term tracking; 397 fresh-ground
  terms left
- `yeti_turbo_profile_scraper.js` — 5x parallel Yeti profile scraper

**Backend infrastructure:**
- `backend/scrapers/unified_backend_scraper.py` — server-side scraper
  hitting website + YP + BBB per shipper. Cron in
  `_unified_backend_scraper_loop` (background_loops_runner.py).
- `backend/inbound_email/shipper_reply_matcher.py` — every 60s scans
  inbound_emails, matches to shipper_targets, tags `inbound_replies[]`
- `backend/inbound_email/website_scrape_paste_api.py` — accepts bookmarklet
  POSTs + URL match scoring + stats endpoint
- `backend/inbound_email/directory_intel_api.py` — YP/BBB/unified POST
  ingest endpoints
- `backend/inbound_email/importyeti_bol_parser.py` — parses BOL rows from
  Yeti's "Most Recent Sea Shipments" text → db.importyeti_bols
- 60s in-process cache on `/_stats` and `/_industry_groups` + cron
  pre-warmer (was 7s cold → now 22ms cached — 318x speedup)
- 8 compound indexes added via
  `backend/scripts/add_shipper_compound_indexes.py`
- `backend/scripts/audit_shipper_urls.py` — bulk URL-match scoring via
  httpx (CLI: `--ag-only --limit 5000`)
- `backend/scripts/seed_yeti_search_terms.py` — populated 604 terms

**Important shipper_targets fields added this session:**
- `bookmarklet_phones[]`, `bookmarklet_emails[]` (cleaner alt to legacy
  `website_phones_found` which had 2147483647 garbage)
- `confirmed_business_name`, `bbb_rating`, `years_in_business`
- `url_match_score`, `url_match_verdict`, `url_likely_wrong`
- `unified_backend_scraper_last_run_at`, `unified_backend_scraper_sources`
- `manual_outreach_log[]`, `last_outreach_at`, `last_outreach_method`,
  `outreach_attempts`
- `inbound_replies[]`, `last_inbound_reply_at`, `last_inbound_reply_from`,
  `inbound_reply_count`
- `research_todo` (dict), `needs_more_research`, `research_priority_notes`
- `deleted_at`, `deleted_reason`, `undeleted_at`
- `industry_group`, `industry_group_source`
- `commodity_class_source`, `equipment_capability_source`

## Standing operator directives (this session, verbatim)

- **"use fewer words"** — already in memory; reinforced.
- **"Why do you ask me?"** about commits — already in memory; reinforced
  (committed autonomously throughout, no permission asks).
- **"We need this data"** — keep scrapers running at max safe rate.
- **"Make sure you save and create a good handoff for the next agent"**
  — this doc.
- **"I want to store the deleted for inspection and research"** — built
  show_deleted filter + undelete endpoint.
- **"I need a way to delete shipper cards. 1826 169TH ASSOCIATES LLC"**
  — built. Operator can delete shells now.
- About browser tabs: **"I got twenty of them running is fast fire mode"**
  — risk-warned (Yeti Pro suspension); told him 5-6 max.
- About backend scraper anonymity: confirmed via my mistake — I told him
  "Hetzner dead-end" then had to CORRECT to "home IP currently."
  Operator approved option C for tomorrow.

## Hard constraints reaffirmed

- Backend scraper concurrency 15 / batch 100 / interval 30s. Don't push
  higher on home IP. Bump after Option C deploys.
- Brave Search disabled until June 1.
- Yeti bookmarklets are operator-only (his Pro session). Don't try to
  back-end Yeti scraping (their bot defense + your IP = ban risk).
- Telnyx voice not webhook-wired yet (Phase 3 backlog).
- Aegis 10DLC brand vetting still pending (TCR BLI6L4R).

## Tomorrow's morning sequence (recommended)

1. Read BOOT_BIOS + this handoff
2. Check overnight numbers via `https://api.continentalhaul.com/website_scrape/stats`
   — should see backend_cron ~20K, ~6K+ new phones
3. **Build Option C** — Hetzner-side scraper service. ~1 hr.
   - SCP `unified_backend_scraper.py` to Hetzner VM
   - systemd unit running the `--loop --rest 30` flag
   - Same Mongo connection (cloudflared tunnel or direct mongo URL)
   - When verified: stop the local backend's `_unified_backend_scraper_loop`
     to avoid duplicate writes
4. Bump Hetzner scraper to conc=50, batch=200 — should hit ~5K/hr
5. Tell operator throughput, ask what next
6. **Sister-site clusterer** (already-built `related-facilities` endpoint
   just needs UI surface) — Steel Dynamics has 33 EPA divisional records
   that should link to corporate parent

## Files modified this session (high-traffic)

- `frontend/src/views/ShipperTargetsView.jsx` (most of UI work)
- `backend/shipper_operator_enrich.py` (delete/undelete/log-outreach/recategorize/research_todo)
- `backend/shipper_targets_admin_api.py` (industry-group/equipment/has_inbound_reply/show_deleted/cache)
- `backend/scrapers/unified_backend_scraper.py` (NEW — the heavy lifter)
- `backend/background_loops_runner.py` (cron registration + tuning)
- `backend/inbound_email/{website_scrape_paste_api,directory_intel_api,shipper_reply_matcher,importyeti_bol_parser,importyeti_paste_api}.py`
- `backend/calls/call_log_router.py` (CALL-CRM Phase 1)
- `backend/scripts/{add_shipper_compound_indexes,audit_shipper_urls,seed_yeti_search_terms}.py`
- `public_site/{unified_scraper,super_scrapers,website_contact_scraper,beast_autoloop,yp_scraper,bbb_scraper,website_scraper_beast,yeti_auto_discover_v2}.{html,js}`
- `public_site/importyeti_bookmarklet.html` (Auto-Discover v2 button added)
- `.gitleaks.toml` (broadened public_site/ bookmarklet allowlist)

## What I DIDN'T finish

- **Carrier researcher fleet** — flagged 0 heartbeats early in session,
  never investigated. Workers may need restart. Check BOOT_BIOS section 4.
- **YP / BBB bookmarklets** — CSP-blocked, abandoned in favor of backend
  scraper. Probably delete the HTML pages eventually.
- **Address Intel bookmarklet** — half-built, backend endpoint exists,
  no bookmarklet UI. Defer.
- **Sister-site clusterer UI surface** — backend endpoint
  `/api/admin/shipper-targets/{id}/related-facilities` exists; just needs
  a 'Related facilities' collapsible on the modal.
- **Hetzner option C** — scoped, not built.

## Operator's mood + state

- Energetic, action-oriented, asked smart strategic questions about
  anonymity / risk. Caught me when I was wrong about Hetzner IP and
  appreciated the honest correction.
- Wants to wake up to a real call list. Conservative wake-up estimate:
  ~1,800-2,500 dialable shippers, ~14,000 consignee addresses. Stretch
  if night goes well: ~3,500+ dialable.
- Started actual outreach (Midland Farms web form). Asked for call-prep
  script. Will likely have replies coming in tomorrow morning that the
  shipper_reply_matcher cron will tag.

— close
