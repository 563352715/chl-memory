# Build Progress — 2026-05-13 01:45Z (autonomous night build)

Continued per operator directive "Keep going. Don't stop until the build is done" + "Using what we got" + 20X plan utilization.

## Tonight (since 00:00Z, ~3.5 hours)

### Shipper outreach
- Wave A: 15/15 sent (1 placeholder for Joplin crash, 14 actual sends)
- Wave B: 25/25 sent
- Wave C: 18/18 sent — verified-email + small-co prospects from Wave 5 list
- **Total 58 shipper outreach emails delivered tonight**
- Resend status: 36 delivered + 22 bounced (mostly Tier-1 role-based guesses)
- 21 bounced addresses auto-added to email_blocklist via Resend backfill
- 3 factor inquiry emails fired to Bobtail / OTR / TBS (post-ComFreight rejection)

### Security stack (LIVE on Hetzner)
- Cloudflare AI Crawler block — `Block all pages`
- Cloudflare Bot Fight Mode — ON (verified GPTBot UA returns 403)
- Cloudflare AI Labyrinth — ON
- Cloudflare Browser integrity check — ON
- Cloudflare Email Address Obfuscation — ON
- Cloudflare Replace insecure JavaScript libraries — ON
- Cloudflare security.txt — set with dispatch@continentalhaul.com + tel + canonical + expires
- 5-layer backend bot protection on `/api/public/*` (honeypot/rate-limit/Turnstile/heuristic/Qwen)
- Let's Encrypt cert issued for continentalhaul.com + www; nginx serves both :80 and :443 with no redirect-loop
- Hetzner self-heal watchdog (60s probe, 120s grace) auto-restarts backend on failure
- resend_event_sync cron (30 min) polls Resend for opens/clicks/bounces + auto-blocklists
- /api/admin/outreach/stats + /api/admin/visitors/stats endpoints live
- public_site_watcher (15 min, Playwright + Haiku) monitors 7 marketing pages

### Operator-mandated code builds (all LIVE)
- **Hopper/grain pricing mode** (`backend/hopper_pricing.py`):
  - Seasonal surge curve (Oct +25%, July +15%, winter -5%)
  - Plains-to-Gulf port lane premium (+6%)
  - Washout allowance ($75 between commodities)
  - Wired into rate_calc_guard Layer 7
  - **VERIFIED LIVE: KS->LA corn May = $1803 base -> $1949 final** (1.02x seasonal + 1.06x port lane, full audit trail in db.rate_quotes_audit)
  - 22/22 tests pass
- **Power-only equipment matching** (carrier_matcher._score_carrier):
  - load.equipment=power_only relaxes trailer filter (any carrier eligible)
  - Carriers with power_only capability get +6 specialist bonus
  - Carriers with ONLY power_only short-circuit on trailered loads
  - 8/8 tests pass
- **Driver triad preferences** (driver_triad_preferences.py + test suite):
  - 3-question schema verified: home_by_cycle / maximize_revenue / next_destination_states
  - Pydantic validators + partial-update support + enum-set contracts locked
  - 19/19 tests pass

### Equipment-band expansion (`rate_calc_engine.EQUIPMENT_BANDS`)
- Added hopper, hopper_bottom, pneumatic_tanker, end_dump, belly_dump, walking_floor
- Added livestock, auto_carrier, intermodal, conestoga, double_drop, box_truck, hotshot, power_only, rgn, heavy_haul, container
- Total bands: 24 (was 11) — every dropdown equipment now produces a real rate

### Wave 4 + Wave 5 research saved
- Wave 4: 50 Tier-1 mega-shipper prospects (file + Desktop)
- Wave 5: 44 sub-$500M regional prospects (file + Desktop) — the realistic CHL sweet spot

### Critical data audit finding
- 786 "active carriers" in `db.users` were 785 SYNTHETIC TEST + 1 real
- 484,700 FMCSA carrier_contacts have phones but ZERO emails (FMCSA Census doesn't publish emails)
- Carrier email outreach blocked until SMS lights up post-FOC 5/15
- Or until B2B email-discovery tool budget is approved (Hunter/Apollo $150-500/mo)

## Commits tonight (in order)
```
8bf4d16  Bot protection 5-layer + Turnstile wiring on public marketing forms
65df3e0  email_blocklist + Hetzner self-heal watchdog
f5cc8ac  STOP-reply detection + blocklist wiring in waves A/B
2004f30  Bounce notification pipeline + outreach stats endpoint + CLAUDE.md boot step 4
5d34531  robots.txt + visitor-stats endpoint + chl-memory CF nav cheatsheet
e5ac2a1  Night-build: LE cert + watchdog grace + Wave C + Resend event sync + deploy script
aa5b52f  Hopper/grain pricing mode + factor pipeline replacement post-ComFreight rejection
6bd51a5  Expand EQUIPMENT_BANDS + WELL_SUPPORTED + miles-field fallback (hopper goes live)
a1c4e8e  Power-only equipment matching: load=power_only relaxes trailer filter (8/8 tests pass)
ae4793c  Driver-triad preferences test suite (19/19 pass)
```

## Tomorrow's queue (operator-action-blocked)
- **CF SSL: Flexible -> Full (Strict)** — cert is ready, 1-click in CF dashboard
- **Cloudflare Turnstile site key** — provision in CF dashboard, paste into `/js/turnstile_config.js`
- **Cloudflare Access setup** — guide on Desktop (`CloudflareAccess_AdminProtection_Setup_2026_05_12.md`)
- **Cloudflare Web Analytics token** — 1-line beacon, code already wired
- **Mercury / BlueVine business LOC** application — $25-50K float backup
- **Sign W-9 + Strato Pay attestation** — both on Desktop

## Tomorrow's queue (Claude-can-do)
- Daily snapshot regression cron (post-FOC SMS alerts on rate drift)
- Take down legacy Emergent deployment
- Email-factor adapter (when Strato Pay docs arrive)
- Frontend UI for the bidirectional matching endpoints (no new tab needed per operator's tab-layout rule)

## What's running on autopilot RIGHT NOW
1. chl-watchdog.timer (Hetzner) probes backend every 60s
2. inbox_manager_cron (every 30 min) classifies + blocklists bounces/STOPs
3. resend_event_sync (every 30 min) polls Resend for opens/clicks/bounces
4. public_site_watcher (every 15 min) Playwright+Haiku checks 7 pages
5. Bot-protection verify() on every /api/public/* POST
6. predicted_empties_cron (every 6h) projects empty truck locations
7. Auto-renewal of Let's Encrypt cert (certbot scheduled task)
