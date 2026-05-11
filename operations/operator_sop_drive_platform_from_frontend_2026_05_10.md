# Operator SOP — How to Drive the Platform from the Front End

**Version:** 1.0 — 2026-05-10
**Purpose:** Click-by-click operator reference for every manual workflow on the CHL platform. If you can read this, you can drive every lifecycle stage without touching the backend or API.

**Standard format:**
- Steps numbered, one action per line
- Bold = button/field name as it appears on screen
- `Monospace` = exact text to type
- ✅ = expected result after the action
- ⚠️ = watch out for / common mistake
- ↩ = where this returns you

---

## Quick reference card — what tab to open for each task

| Task | Tab | SOP section |
|---|---|---|
| Look up every document for a BOL number | **BOL Search** | §1 |
| Run any manual workflow (the operator console) | **Ops Console v2** | §2-§9 |
| Test pricing without committing a quote | **Ops Console v2** → Dry-Run Quote card | §3 |
| Manually fire a delivered load's invoice + NOA | **Ops Console v2** → Fire Invoice + NOA card | §6 |
| Manually generate a rate confirmation | **Ops Console v2** → Fire Rate-Con card | §5 |
| Create test data for staging | **Ops Console v2** → Create Synthetic Load card | §2 |
| Look at your dashboards | **Dashboard** + **Day-1 Monitor** | §10 |
| See pending operator approvals | **Queues** | §11 |
| Check renewal due dates | **Renewals** | §12 |

---

## §1 — BOL Document Search

**When to use:** carrier calls asking about a specific load, factor calls asking for the rate-con + invoice, audit request, dispute, anytime you need EVERY document linked to one BOL.

1. Click the **"BOL Search"** tab (top nav)
2. ✅ You see a search bar + empty results panel
3. Type the BOL number into the search box (example: `CHL-001` or whatever your shipper assigned)
4. Click **"Find Documents"**
5. ✅ Within 1-2 seconds, you see:
   - **Load summary card** at top (origin, dest, carrier, status, dates)
   - **Document timeline** chronologically below — every doc grouped by type
   - **Totals bar** at the bottom (e.g., "1 rate_con · 1 BOL · 3 POD photos · 1 invoice · 1 NOA")
6. To view a specific document: click the **"View"** button on its row
7. ✅ Document opens in a new tab (PDF download or inline preview)

⚠️ **If you see "No load found":**
- Double-check the BOL spelling
- BOL may live under a different field name (try the load's `reference_number` or `master_bol_ref` if you have it)
- The load may not have been created yet — check the Loads tab first

↩ Stays on BOL Search tab; click any other tab to leave.

---

## §2 — Create a Synthetic Test Load (for testing, demo, training)

**When to use:** you want to test the platform end-to-end without using a real load. Synthetic loads are tagged `synthetic: true` and DO NOT pollute production reports.

1. Click **"Ops Console v2"** tab (top nav, near Reviews / Alerts area)
2. ✅ You see the BOL Quick Search at top + a 2-column grid of 7 action cards
3. Find the card titled **"Create Synthetic Load"** (top-left)
4. Fill in the fields:
   - **BOL number:** type `CHL-TEST-` + your unique suffix (example: `CHL-TEST-DEMO-001`)
   - **Origin city:** e.g., `Atlanta`
   - **Origin state:** 2-letter, e.g., `GA`
   - **Destination city:** e.g., `Dallas`
   - **Destination state:** 2-letter, e.g., `TX`
   - **Equipment type:** select from dropdown (van / reefer / flatbed / etc.)
   - **Miles:** integer, e.g., `780`
   - **Weight (lbs):** integer, e.g., `42000`
5. Click **"Run"** at the bottom of the card
6. ✅ Result panel appears below the form with:
   - Green status badge
   - **load_id** field (with Copy button — save this!)
   - **bol_number** field
   - audit_id

⚠️ **The BOL number must start with `CHL-TEST-` or `SYNTH-`** so the cleanup card can find it later.

⚠️ **Save the load_id** — you'll paste it into the next 4 cards.

↩ Stays on Ops Console v2.

---

## §3 — Dry-Run Quote (test pricing without committing)

**When to use:** customer asks "what would this load cost?" and you want to see the pricing engine output without creating a load record.

1. On **Ops Console v2**, find the card titled **"Dry-Run Quote"**
2. Fill in: **Origin city / state** + **Destination city / state** + **Equipment type** + **Miles**
3. Click **"Run"**
4. ✅ Result panel shows:
   - **broker_premium:** e.g., 1.12
   - **market_rate:** the calculated quote (dollars)
   - **suggested_carrier_rate:** what you'd pay the carrier
   - **pnl_warning_status:** GREEN (profitable) / RED (would lose money) / YELLOW (margin tight)
5. ⚠️ If `pnl_warning_status` is RED — **DO NOT** book at this rate. The platform's never-book-at-loss rule will block it anyway.

**No load is created. No quote is committed. Pure preview.**

↩ Stays on Ops Console v2.

---

## §4 — Synthetic Carrier Accept (simulate a carrier accepting your offer)

**When to use:** testing the carrier-acceptance → dispatch flow without waiting for a real carrier to click the magic link.

1. On **Ops Console v2**, find the card titled **"Synthetic Carrier Accept"**
2. Fill in:
   - **load_id:** paste the load_id from §2
   - **mc_number:** any test MC (suggested: `MC-9999001` — that's a pre-seeded synthetic carrier)
3. Click **"Run"**
4. ✅ Result panel shows acceptance state + audit_id
5. ✅ The load.status transitions to `booked` and `assigned_carrier_id` is set

↩ Stays on Ops Console v2.

---

## §5 — Fire Rate Confirmation (manual rate-con generation)

**When to use:** the auto-fire cron didn't fire OR you need to regenerate a rate-con for a load.

1. On **Ops Console v2**, find the card titled **"Fire Rate-Con"**
2. Fill in: **load_id** (from §2 or §4)
3. Click **"Run"**
4. ✅ Result panel shows:
   - rate_con_id (the new doc's UUID)
   - status: `generated`
   - **download URL** — click to preview the PDF
5. If a rate-con already exists for this load: the card returns `already_fired: true` (idempotent — no duplicates)

↩ Stays on Ops Console v2.

---

## §6 — Mark Load Delivered

**When to use:** load is physically delivered, you have POD proof, but the carrier-side flow didn't fire the delivered status transition (rare — usually carrier app does this).

1. On **Ops Console v2**, find the card titled **"Mark Delivered"**
2. Fill in: **load_id**
3. (Optional) Upload a POD photo via the file input
4. Click **"Run"**
5. ✅ Result panel shows:
   - status: `delivered`
   - delivered_at: timestamp
   - If POD uploaded: synth_pod_id (a fake POD doc tagged synthetic so it shows up in BOL Search)
6. ✅ This may auto-fire the invoice + NOA cron IF feature flag `delivered_autofire_enabled=true`. Check by looking at §7's status.

↩ Stays on Ops Console v2.

---

## §7 — Fire Invoice + NOA (Notice of Assignment)

**When to use:** load is delivered, factor is selected, but the auto-fire didn't run (or you have `delivered_autofire_enabled=false` by default).

**Prerequisites:**
- Load must be in `delivered` status
- Load must have a `factor_id` set (selected via Factor Picker tab)

1. On **Ops Console v2**, find the card titled **"Fire Invoice + NOA"**
2. Fill in: **load_id**
3. Click **"Run"**
4. ✅ Result panel shows:
   - invoice_id (new broker_invoice doc)
   - invoice_number (human-readable, e.g., `CHL-INV-2026-1042`)
   - noa_id (new notice_of_assignment doc)
   - factor_name (which factor was selected)
5. If already fired for this load: returns `already_fired: true` + the existing IDs

⚠️ **If no factor_id is set on the load:** the card returns `deferred: true, reason: no_factor`. Use **Factor Picker** tab first to assign a factor, then re-fire.

↩ Stays on Ops Console v2.

---

## §8 — BOL Quick Search (same as §1 but inside Ops Console)

**When to use:** you're already in Ops Console v2 and want to verify documents were created without leaving.

1. At the top of **Ops Console v2**, find the **"BOL Quick Search"** panel
2. Type the BOL number
3. Click **"Find Documents"**
4. ✅ Results render in-line below the search bar (same format as §1)

This is a convenience proxy to the full BOL Search tab — same data, same shape.

↩ Stays on Ops Console v2.

---

## §9 — Synthetic Data Cleanup

**When to use:** you're done testing and want to remove ALL synthetic loads + their docs.

1. On **Ops Console v2**, find the card titled **"Synth Cleanup"** (bottom-right, red destructive button)
2. Fill in: **prefix** field — must start with `CHL-TEST-` or `SYNTH-`
3. Click **"Run"**
4. ✅ Confirmation modal appears: **"Delete all synthetic data with prefix X? This cannot be undone."**
5. Click **"Confirm Delete"** to proceed, or **"Cancel"** to abort
6. ✅ Result panel shows counts per collection: loads / carrier_interests / pod_uploads / broker_invoices / rate_confirmations / notices_of_assignment / quotes — and total_deleted

⚠️ **Prefix MUST start with `CHL-TEST-` or `SYNTH-`** — otherwise the card rejects with a 400 error. This prevents accidentally deleting real loads.

⚠️ This is destructive — the loads disappear from the Loads tab + reports forever. Make sure you no longer need the test data.

↩ Stays on Ops Console v2.

---

## §10 — Daily Dashboards (where to look on launch day)

### Dashboard tab
- High-level KPIs: open loads, delivered loads, revenue potential, bookings today
- Driver status panel (available / on load / off duty)
- Load status panel (available / booked / in transit / delivered)
- Top match suggestions

### Day-1 Monitor tab (NEW — 2026-05-10)
- **Top-left (Platform Health):** Backend / Frontend / Mongo ping / Disk free / Memory / API status / Cron failures last hour
- **Top-right (Volume & Activity):** open loads, delivered, quotes, acceptances, payments, emails, SMS — each with last 1h + last 24h
- **Bottom-left (Pending Operator Action):** 5 clickable rows — click to jump to Queues tab pre-filtered
- **Bottom-right (Recent Alerts):** last 10 alerts from `db.operator_alerts`, color-coded by severity

**Refresh:** Day-1 Monitor auto-polls every 15s. Dashboard polls every 30s.

---

## §11 — Operator Queues (5 panels, one tab)

**When to use:** at least 2-3x per day on launch days. Anything that needs your action lives here.

1. Click **"Queues"** tab
2. You see 5 sub-panels with badge counts:
   - **P&L Warnings** — loads that would lose money (your never-book-at-loss decisions)
   - **Funding Gate** — delivered loads missing GPS or POD; click Manual Approve to override
   - **Pending Factor Assignment** — delivered loads without a factor selected; pick one + retry invoice/NOA
   - **Rate-Con Failures** — rate-con generation broke; retry from here
   - **Operator Alerts (24h CRIT)** — critical alerts in the last 24 hours
3. Each panel has its own action button:
   - P&L: resolve (strategic / book_correction / dismiss)
   - Funding Gate: manual approve
   - Pending Factor: pick factor + retry
   - Rate-Con: retry
   - Alerts: acknowledge

**Refresh:** auto-polls every 30s.

---

## §12 — Renewals (so things don't lapse)

**When to use:** monthly at minimum. Whenever you renew a policy or pay a fee.

1. Click **"Renewals"** tab
2. If list is empty: click **"Seed from Catalog"** — populates the 54-entry standard broker renewals
3. Verify **BMC-84 Surety Bond** is listed (it should be — required for broker authority)
4. For each renewal:
   - Edit the **due date** to match your actual policy
   - Edit the **policy/reference number** to your actual one
   - Edit the **vendor** if known
   - Add **notes** for renewal contact info
5. The platform alerts you 30/14/7/3/1 day before each due date

**Critical renewals to verify FIRST:**
- BMC-84 broker surety bond (annual — REQUIRED for MC authority)
- BMC-91 / BMC-91X / BMC-32 (cargo insurance filings — annual)
- UCR Filed (annual by Dec 31)
- HVUT Form 2290 (annual by Aug 31)
- SAM.gov registration (annual)
- Workers Comp (annual)
- Commercial Auto policy (annual)

---

## §13 — Vault (where your docs live)

**When to use:** anytime you need a stored credential or company document.

1. Click **"Vault"** tab (owner-only — no broker access)
2. Two sub-tabs:
   - **Credentials** — login passwords for banking / FMCSA / SaaS / email / general
   - **Documents** — encrypted file storage (PDFs, scans, certs)
3. **To find a credential:** scan the table; click the eye icon (👁) to reveal, pencil (✏️) to edit
4. **To find a document:** click into the relevant folder (e.g., "Continental Haul Logistics LLC" → "Insurance")
5. **To add a credential:** click **"+ Add Credential"** top-right
6. **To upload a document:** open a folder → click **"Upload"** → drag file OR click to browse

**Every reveal is audit-logged** to `db.vault_audit`.

---

## §14 — Full Load Lifecycle Walkthrough (the master flow)

This is what an end-to-end production load looks like, click by click:

### Stage 1: Quote
1. Customer requests a quote (via email or phone)
2. Open **Ops Console v2** → **Dry-Run Quote** card
3. Enter origin / dest / equipment / miles
4. Click **Run**
5. Read the **market_rate** result
6. If `pnl_warning_status = GREEN`: send the quote to customer
7. If `pnl_warning_status = RED`: requote at a higher rate OR decline

### Stage 2: Book the load (if customer accepts)
1. Go to the **Loads** tab → **"+ Add Load"**
2. Fill the full load form (shipper, consignee, pickup date, delivery date, BOL, etc.)
3. Submit
4. Load now appears in the loads list with status = `available`

### Stage 3: Find a carrier
1. Go to the **Find Loads** tab OR **Carriers DB** tab
2. Run a search by lane / equipment
3. Click on a carrier → review safety scores + SMS BASIC + vetting status
4. Click **"Send Cold Outreach"** to send a magic-link SMS

### Stage 4: Carrier accepts
- Carrier clicks the SMS link
- They see the load + accept on their phone
- Backend auto-fires the rate-con generation
- Load.status transitions to `booked`
- Operator sees notification in **Queues** if anything fails

### Stage 5: Dispatch
- Carrier picks up the load
- Carrier app fires GPS pings to `db.gps_pings`
- Load.status transitions to `in_transit`
- You can watch progress in the **Live Map** tab

### Stage 6: Delivery
- Carrier physically delivers the load
- Carrier app fires POD upload + status transition to `delivered`
- Auto-fire (if enabled) generates broker invoice + NOA
- Load.status = `delivered`

### Stage 7: Verify documents
1. Go to **BOL Search** tab
2. Type the load's BOL number
3. ✅ You see: rate_con, POD photo(s), invoice, NOA — all chronologically linked

### Stage 8: Factor funding
1. Factor receives the invoice + NOA (via email or factor portal)
2. Factor advances payment
3. Mercury bank account receives the funds
4. You see the deposit in the **Accounting** tab

### Stage 9: Carrier payout
1. Operator reviews delivered load in **Settlements** or **Accounting** tab
2. Click **"Pay Carrier"** (or use the auto-pay if configured — currently disabled per factoring decision)
3. Mercury fires the ACH payout

### Stage 10: Close the load
- Load lifecycle complete
- All documents permanently linked to BOL
- Historical record visible in **BOL Search** indefinitely

---

## §15 — When Things Go Wrong

### Symptom: load shows in Queues > Funding Gate
**Cause:** delivered load missing GPS proximity to delivery OR missing POD
**Action:** Click into Queues → Funding Gate panel → review the load → click **"Manual Approve"** if you've verified delivery via other means

### Symptom: load shows in Queues > Rate-Con Failures
**Cause:** PDF generation failed (printer service down, font issue, etc.)
**Action:** Click **"Retry"** from the queue panel OR go to **Ops Console v2** → **Fire Rate-Con** card → enter load_id

### Symptom: load shows in Queues > Pending Factor
**Cause:** load delivered but no factor was selected
**Action:** Go to **Factor Picker** tab → pick factor for the load → return to Queues → click **"Retry Invoice+NOA"**

### Symptom: P&L Warning fires on a quote
**Cause:** the platform detected the quote would lose money
**Action:** Go to **Queues** → **P&L Warnings** panel → click into the warning. Three options:
- **Strategic** = "I know it's a loss, book it anyway for relationship reasons"
- **Book Correction** = "I'm adjusting the carrier rate, recalculate"
- **Dismiss** = "False positive, ignore"

### Symptom: critical alert in Recent Alerts feed
**Cause:** something serious — cron failure, vendor outage, throttle trip
**Action:** click into the alert → review the source → fix the underlying issue → acknowledge

### Symptom: platform won't load (whole page blank)
**Cause:** likely a backend or frontend service died
**Action:** restart services via NSSM (if VS Code admin-elevated) OR call up Claude Code session to relaunch

---

## §16 — Tabs you'll use daily vs rarely

### Daily (every launch day)
- **Dashboard** + **Day-1 Monitor** — situational awareness
- **Queues** — operator action items
- **Loads** — see what's open / booked / delivered
- **Inbox** — email triage
- **Ops Console v2** — manual interventions

### 2-3x per week
- **Carriers DB** — vet a new carrier
- **Renewals** — check upcoming deadlines
- **Vault** — store/retrieve credentials or docs
- **Accounting** — invoice / payment status

### Monthly
- **Factors** — factor performance / chargeback review
- **Pipeline** — sales conversion review
- **Shipper Scorecard** — customer health
- **Compliance** — annual audit prep

### Rare / advanced
- **Self-Heal** + **Patch Proposals** + **Outcomes** + **Reviews** — autonomous-platform diagnostics (only when the self-heal layer is active; default OFF)
- **Æther** — system entropy + network aperture (operator-only Project Æther governor)
- **Failure Console** — historical failure mode review

---

## §17 — Keyboard shortcuts (currently)

- `/` — focus the global search bar (top of screen)
- `Ctrl+K` — open command palette (where supported)
- `Esc` — close any open modal

---

## §18 — Where to find help

- **Code documentation:** `chl-memory/operations/` — operational SOPs
- **Architecture:** `chl-memory/architecture/SYSTEM_MATRIX.md` — agent execution blueprint
- **Build state:** `C:\CHL\BUILD_STATUS_REPORT.md` — current platform state
- **Smoke test:** `chl-memory/operations/smoke_test_walkthrough_2026_05_10.md` — manual UI verification guide
- **This document:** lives at `chl-memory/operations/operator_sop_drive_platform_from_frontend_2026_05_10.md` and on your Desktop

---

## Document maintenance

- This SOP reflects the platform state as of 2026-05-10 (post Wave 4 + Ops Console + BOL Search + renewals expansion + Stripe cleanup)
- Re-read after any major feature ship to verify still accurate
- Update on every UI change (new button, renamed field, new tab)
- Version this document in chl-memory git so prior versions stay accessible

**End of SOP.**
