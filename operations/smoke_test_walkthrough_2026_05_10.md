# Golden-Path Smoke Test — 2026-05-10 EOD

**Purpose:** Step-by-step manual verification of every UI surface shipped today. Catches bugs unit tests miss (like the carrier-contacts JSON-shape bug we caught earlier today: 484k carriers had data in DB but UI read wrong path).

**State at start:**
- Backend: ✅ HTTP 200 at http://127.0.0.1:8001
- Frontend: ✅ HTTP 200 at http://127.0.0.1:3000
- Mongo: ✅ chl_local, 484,700 carriers with contact data, 14 vault blobs, 55 renewals
- Operator: logged in, currently on Dashboard tab

**Resume protocol if session restarts mid-walkthrough:** read `C:\CHL\memory\handoff_session_2026_05_10_eod_close.md` first, then this file, then resume at the next un-checked step below.

---

## Step 1 — Dashboard health

**What it is:** Default landing tab after login. Should be the main `DashboardView` OR the new `Day1Dashboard` (added today, registered as `day1_dashboard` tab next to `dashboard`).

**What to check:**
- [ ] Does the page render (not blank, not spinning forever)?
- [ ] How many tabs in top nav? Quick list.
- [ ] Any red error banners?

**Verifies:** frontend boot, auth cookie working, no JS console crash.

**If broken:** open browser DevTools (F12) → Console tab → screenshot any red errors.

---

## Step 2 — Day 1 Dashboard tab (NEW today)

**What it is:** 4-quadrant operator monitoring dashboard. Built today in Wave 3B + 4. Polls every 15s.

**Click:** find the tab labeled "Dashboard" with a Gauge icon. (If there are two Dashboard tabs — the new Day 1 has the Gauge icon.)

**What to check:**
- [ ] **Top-left quadrant — Platform Health:** Backend dot green, Frontend dot green, Mongo ping <50ms, Disk free 700+ GB (green badge), Memory used ~13 GB (yellow), API health = "healthy"
- [ ] **Top-right — Volume & Activity:** 8 rows (open loads / delivered / quotes / acceptances / payments / emails / SMS) each with "1h" + "24h" columns. Numbers can be 0; structure is what matters.
- [ ] **Bottom-left — Pending Operator Action:** 5 clickable rows (P&L warnings, funding gate, awaiting factor, rate-con failures, critical alerts 24h). Zero counts grey out + disabled; non-zero would show colored badges.
- [ ] **Bottom-right — Recent Alerts:** Empty state shows green check + "No alerts" (expected — no alerts have fired since launch).

**Verifies:** dashboard_router.py + Day1Dashboard.jsx + cache + 25 new Mongo hot-path indexes.

**If broken:** API endpoint is `GET /api/dashboard/day1` (owner-only). Hit in DevTools Network tab.

---

## Step 3 — Operator Queues tab (NEW today)

**What it is:** 5-panel review queue surfacing the new collections Wave 1 created. Built today.

**Click:** Find tab labeled "Operator Queues" (or similar).

**What to check:**
- [ ] 5 sub-panels visible: P&L Warnings / Funding Gate / Pending Factor Assignment / Rate-con Failures / Operator Alerts
- [ ] Each panel has a count badge (zero counts greyed out is expected today)
- [ ] Refresh button works (30s auto-refresh)

**Verifies:** operator_queues_router.py + OperatorQueuesView.jsx. The point of this tab: when Wave 1's P&L guard / funding gate / autofire fire, they queue here for review instead of being silent.

**If broken or missing tab:** check `frontend/src/App.js` for `operator_queues` tab registration + OperatorQueuesView import.

---

## Step 4 — Renewals tab (EXPANDED today)

**What it is:** The renewals UI you flagged this morning as showing only BOC-3. Now expanded with 54-entry catalog including BMC-84 bond + 19 broker regulatory items.

**Click:** Renewals tab.

**What to check:**
- [ ] Page loads
- [ ] If list is empty: click the **"Seed from Catalog"** button (only visible in empty state)
- [ ] After seeding: should show 54 renewal entries grouped by category
- [ ] Verify **BMC-84 Broker Surety Bond ($75K)** is in the list (Insurance category)
- [ ] Verify UCR, IFTA, HVUT 2290, BMC-91, BMC-91X, BMC-32 all present
- [ ] BOC-3 section still works separately (already had data in `db.boc3_designations`)

**Verifies:** `scripts/seed_renewals.py` catalog (54 entries) + `POST /api/_renewals/seed-from-catalog` endpoint + SeedFromCatalogButton component.

**Operator follow-up after smoke test:** edit each renewal's actual policy number + due date to match your real records. The catalog gave you the SHAPE; you fill the data.

---

## Step 5 — Vault tab → Documents subsection (SHIPPED today)

**What it is:** New encrypted document storage feature shipped this morning. Stores files alongside the existing credential vault.

**Click:** Vault tab → "Documents" sub-tab.

**What to check:**
- [ ] Documents tab visible (sibling to "Credentials")
- [ ] One root folder visible: **"Continental Haul Logistics LLC"**
- [ ] Click into it → 5 subfolders: LLC Formation / Insurance / Branding / Correspondence / Login & Recovery
- [ ] Each subfolder has files (insurance PDFs, IRS EIN docs, logos, correspondence, recovery codes)
- [ ] Try downloading **Active_Pending Insurance.pdf** — should download decrypted

**Verifies:** vault.py file endpoints + VaultView.jsx Documents section + Fernet decryption + 14 .enc blobs at `C:\CHL\vault_files\`.

**If broken:** check VAULT_ENCRYPTION_KEY env var is set + the .enc files exist on disk.

---

## Step 6 — Carrier detail (THE BUG WE FIXED today)

**What it is:** This morning we discovered the UI was reading `data?.fields` but the API returned `{ok, dot_number, contact: {fields, ...}}` — so every carrier showed "unfilled" for every field despite 484k rows being populated. Today we (a) fixed the UI bug, (b) backfilled contact_name for all 484k carriers.

**Click:** Navigate to a carrier detail page. Use **DOT 174 (CWT Farms International Inc)** as the test case.

**What to check:**
- [ ] Carrier name: "CWT FARMS INTERNATIONAL INC"
- [ ] **Phone:** `7705323181` — status badge `auto_filled` (green)
- [ ] **City:** `GAINESVILLE` — auto_filled
- [ ] **State:** `GA` — auto_filled
- [ ] **ZIP:** `30501` — auto_filled
- [ ] **Address Line 1:** `1180 AIRPORT PARKWAY` — auto_filled
- [ ] **Contact Name:** `CWT Farms International Inc` — auto_filled (title-cased company name; low-confidence 0.5)
- [ ] **Email / Address Line 2 / Contact Title:** still `unfilled` (Census doesn't expose these — expected)
- [ ] Source column shows `fmcsa_census` for the auto-filled fields

**Verifies:** App.js:7913 fix (read `data?.contact?.fields`) + the contact_name backfill (484,700 rows updated).

**If broken:** if everything shows "unfilled" again — the UI fix didn't deploy. Check git diff on frontend/src/App.js around line 7913.

---

## Step 7 — Rate calculator

**What it is:** Internal quote tool. Tests the P&L guard wire-up from Wave 1 (won't book at a loss).

**Click:** find "Rate Calculator" or similar internal tool.

**What to check:**
- [ ] Form fields: origin city/state, dest city/state, equipment, miles, weight
- [ ] Equipment dropdown shows **9 options** (dry_van, reefer, flatbed, step_deck, tanker, auto_carrier, livestock, intermodal, heavy_haul) — Wave 2 canonical
- [ ] Generate a sample quote: ATL GA → DAL TX, dry_van, ~800 miles
- [ ] Verify it returns a number + the broker premium is 1.12 (default) or whatever's in business_settings

**Verifies:** equipment_types.py canonical 9 + rate_calc_engine + pnl_monitor wire-up.

**If broken or quote = 0:** pnl_monitor may be blocking a loss — check the response payload for a `pnl_review_required` flag.

---

## Step 8 — Load creation

**What it is:** Test that creating a load works end-to-end + validates the equipment + payment_terms enums.

**Click:** Loads tab → "Add Load" or similar.

**What to check:**
- [ ] Form fields populate
- [ ] Equipment dropdown: 9 options
- [ ] Payment terms dropdown: 7 options (quick_pay, net_15, net_30, net_45, net_60, net_90, cod) — Wave 2 canonical
- [ ] Submit a test load
- [ ] Verify it appears in the loads list

**Verifies:** payment_terms.py canonical 7 + load_schema_v2.

---

## Step 9 — Funding gate (only if delivered loads exist)

**What it is:** Wave 1 added GPS + POD requirement before factor funding. Soft gate — operator can override.

**Click:** Settlements / Funding tab.

**What to check:**
- [ ] List of pending funding
- [ ] If any load is "missing GPS" or "missing POD" → a yellow warning badge + "Manual Approve" button
- [ ] If GPS is more than 0.5 miles from delivery → red fraud-block badge (no override)

**Verifies:** payment_rails.check_funding_gate + SettlementsView.jsx Manual Approve UI.

**Skip if no delivered loads exist yet.**

---

## Step 10 — Settings / business_settings UI

**What it is:** The operator-tunable settings panel.

**Click:** Settings tab.

**What to check:**
- [ ] Broker premium default: 1.12
- [ ] Margin floor: 1.08
- [ ] Daily load cap: (whatever you set)
- [ ] Banking auto-sweep %: (whatever)
- [ ] Spend caps section: parser_llm_daily_cap_usd ($5), twilio_alerts ($5 — NEW today)

**Verifies:** db.business_settings has all the keys we shipped today.

---

## Step 11 — Browser console check

**What it is:** Catch JS errors that don't render visibly.

**Action:**
1. Open DevTools (F12 in Chrome/Edge)
2. Console tab
3. Look for red lines
4. Screenshot anything red

**Common things to ignore:**
- `react-hooks/exhaustive-deps` warnings (cosmetic, we have 4 of these — non-blocking)
- React 18 hydration warnings if any
- 401s on routes that need owner auth (expected if you log in as broker)

---

## Step 12 — Browser network tab check

**Action:**
1. DevTools → Network tab
2. Filter to "Fetch/XHR"
3. Refresh the page
4. Look for any **red** (failed) requests
5. Click each red one → check the response

**Common things to ignore:**
- 401 on owner-only routes (you'd need owner login)
- 304 Not Modified (success)

---

## What "PASS" looks like

If all 12 steps check out:
- Platform is verified end-to-end at the UI layer
- The 30+ sub-agents from today + verifier-gates + 469 unit tests are corroborated by manual click-through
- Ready for May 14 launch from a code perspective (operator pending actions still apply: UPS, BIOS XMP, RAM install, FMCSA authority, etc.)

## What "FAIL" looks like + how to report

For each failure:
1. Which step (1-12)
2. Screenshot
3. Console errors (if any)
4. Network tab response (if API call)

Report format makes it easy for me to dispatch a fix sub-agent.

---

## What I'm tracking on my side

| Item | Notes |
|---|---|
| Backend log | `/tmp/chl_backend.log` |
| Frontend log | `/tmp/chl_frontend.log` |
| Last commit | `1df0b9a` (renewals expansion) |
| Last chl-memory commit | `557b2e6` (current_status update) |
| Last D-drive snapshot | (just ran, 3.3s, 14 blobs mirrored) |
| 4 background tasks completed today | backend launch / frontend launch / monitor / sub-agents |

---

**End of walkthrough guide. Stay tight per `feedback_operator_instruction_style.md`: numbered steps, one action per line, no paragraph explanations. Report back step-by-step.**
