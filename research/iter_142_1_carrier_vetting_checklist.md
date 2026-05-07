# Carrier Vetting Checklist — Operator Step-by-Step

> **Purpose:** Operator-facing playbook for vetting a single motor carrier from MC number to legal dispatch. Generated 2026-05-07 in support of iter 142.1 load-board-first amendment (operator confirmed Option A — no pre-vetted carriers).
> **Time estimate per carrier:** 30 min - 2 hours depending on carrier responsiveness. Most variability is in steps 5 (initial contact) and 6 (COI receipt).
> **Trigger:** Run this checklist whenever a carrier responds to a CHL load posting (DAT/Truckstop) for the first time, OR when adding a carrier from referral/cold outreach.
> **Legal note:** Steps 5-9 are required before legal dispatch under FMCSA broker regs (49 CFR 371). Skipping any step exposes CHL to liability if the carrier causes a loss.

---

## Pre-vetting prep (one-time, operator does BEFORE first vetting)

Before vetting any carrier, ensure these are ready:

- [ ] **Broker-Carrier Agreement (BCA) template.** Default: industry-standard template (e.g., TIA-published). If you don't have one, search "TIA Broker-Carrier Agreement template" or copy from a reputable freight broker peer. Attorney review optional but not blocking pre-revenue.
- [ ] **W-9 form (blank).** IRS Form W-9. Carrier fills + returns. Free at irs.gov.
- [ ] **CHL company info ready to share:** Continental Haul Logistics LLC, MC-1817555, USDOT 4569843, address (your business address from FMCSA registration), phone, email, EIN.
- [ ] **Bank info / payment terms decided:** What payment terms will you offer? (Net-30, Quick-pay 2-3% fee, factoring net-1, etc.) Decide once and apply consistently. Net-30 is industry default.
- [ ] **Insurance requirements decided:** Standard minimums are $1M auto liability + $100K cargo. Higher for hazmat or specialized loads. Document on a one-page "carrier requirements" sheet to share when carrier asks.
- [ ] **Refusal letter template** (for failed-vetting carriers — see step 11).

**Time:** ~1-2 hours one-time. Skip if all above are already ready.

---

## Vetting Workflow — 10 Steps + Red Flag Decision Points

### Step 1 — Capture initial info (~2 min)

When carrier responds to a load posting (or via cold outreach):

- [ ] Carrier's **MC number** (or DOT number — FMCSA records cross-reference)
- [ ] Carrier's **legal name** as registered (will verify in step 2)
- [ ] **Contact:** dispatcher name, phone, email
- [ ] **Equipment:** Van? Reefer? Flatbed? Step-deck? Specific truck count?
- [ ] **Source:** how did they find CHL? (DAT post, Truckstop, referral) — useful for tracking acquisition channels

**Output:** A small note (text file, spreadsheet row, or CHL UI when iter 142.1 ships) with the above fields.

---

### Step 2 — FMCSA SAFER lookup (~3 min, FREE)

Open **https://safer.fmcsa.dot.gov/CompanySnapshot.aspx** in a browser.

- [ ] Enter MC number → click "Search"
- [ ] **Verify legal name** matches what carrier told you in step 1. (Mismatch = red flag, see RF1)
- [ ] **Operating Status:** must be **"AUTHORIZED FOR Property"** (or "Authorized for Hire"). Anything else = STOP.
- [ ] **Out-of-Service Date:** must be blank/empty. If date present = STOP, OOS order active.
- [ ] **Insurance on File:** scroll to "Insurance Requirements" panel
  - [ ] **BIPD (Bodily Injury & Property Damage):** must be ≥ $750K (federal minimum) — most CHL loads need ≥ $1M (industry standard for general freight)
  - [ ] **Cargo Insurance:** must be ≥ $100K (industry standard) — higher for high-value loads
  - [ ] **Filing Date:** within the last 12 months. Older filings = stale insurance, requires step 6 verification.

**Red Flags:**
| Flag | Action |
|---|---|
| RF1: Legal name mismatch | STOP. Possible identity fraud. Decline politely (step 11). |
| RF2: Authority not active or revoked | STOP. Decline. |
| RF3: OOS order active | STOP. Decline. |
| RF4: Insurance below minimums | STOP. Decline OR ask for current COI showing higher limits — they may have updated since FMCSA filing. |
| RF5: Insurance filing > 12 months old | YELLOW. Continue but require COI in step 6. |

---

### Step 3 — FMCSA SMS scores lookup (~3 min, FREE)

Open **https://ai.fmcsa.dot.gov/SMS/Carrier/Search.aspx**

- [ ] Enter MC or DOT number → search
- [ ] Click into the carrier's profile → view BASIC scores

Review the 7 BASIC categories:
1. Unsafe Driving
2. Crash Indicator
3. HOS (Hours of Service) Compliance
4. Vehicle Maintenance
5. Controlled Substances/Alcohol
6. Hazardous Materials Compliance
7. Driver Fitness

For each category, look for:
- **Percentile:** lower is better (50th = average; >65th = above-average risk)
- **Alert symbol** (yellow triangle): means category exceeds intervention threshold

**Red Flags:**
| Flag | Action |
|---|---|
| RF6: Any category with alert symbol (yellow triangle) | YELLOW. Acceptable for low-value loads, decline for high-value/hazmat. |
| RF7: 2+ categories with alert symbols | RED. Decline unless very compelling fit + low-value load. |
| RF8: Crash Indicator >75th percentile | RED. Decline — accident-prone carriers expose you to liability. |
| RF9: Unsafe Driving >75th percentile | YELLOW for low-risk lanes; RED for hazmat or high-traffic corridors. |

---

### Step 4 — Crash + inspection history detail (~5 min, FREE)

Still on FMCSA SMS profile, scroll to:

- [ ] **Crash data:** how many crashes in last 24 months? Any with fatalities or injuries?
- [ ] **Inspection results:** what's the OOS rate? Compare to national average (~5% for vehicles, ~5% for drivers)
- [ ] **Recent inspections:** any patterns of failed inspections in last 6 months?

**Red Flags:**
| Flag | Action |
|---|---|
| RF10: Fatal crash in last 24 months | STOP. Decline regardless of fault. |
| RF11: Multiple injury crashes (>2 in 24 mo) | RED. Decline. |
| RF12: Vehicle OOS rate >2x national average | RED. Decline. |
| RF13: Driver OOS rate >2x national average | RED. Decline. |

---

### Step 5 — First contact + qualification call (~10-30 min)

Call (or email) the carrier dispatcher using contact info from SAFER (preferred — verified) or step 1 (if SAFER contact differs from claim, use SAFER):

**Confirm:**
- [ ] They actually want this load (or future loads on this lane)
- [ ] They have a truck + driver available for the pickup window
- [ ] Equipment matches what was claimed in step 1
- [ ] They're willing to sign a BCA + provide COI before dispatch

**Discuss:**
- [ ] Rate (your offer; their counter — though for v1 we're fixed-offer per agenda)
- [ ] Pickup/delivery times
- [ ] Any special equipment requirements (lift gate, straps, pallet jack, etc.)
- [ ] Payment terms (your standard from prep prerequisites)

**Red Flags:**
| Flag | Action |
|---|---|
| RF14: Won't share insurance info / COI | STOP. Decline. Required by law. |
| RF15: Won't sign BCA | STOP. Decline. Required by FMCSA regs. |
| RF16: High-pressure tactics, won't talk about rates clearly | YELLOW. Could be inexperienced dispatcher; reassess after BCA signed. |
| RF17: Asks for advance payment, deposit, or unusual money flows | RED. Common scam pattern. Decline. |
| RF18: Phone number from SAFER doesn't reach the company | RED. Possible identity fraud or stale registration. STOP. |

---

### Step 6 — Request Certificate of Insurance (COI) (~minutes - hours, depends on carrier)

After step 5 verbal commitment:

- [ ] Email carrier: "Please have your insurance broker email a current COI naming Continental Haul Logistics LLC, MC-1817555, USDOT 4569843, [your address] as Certificate Holder AND Additional Insured."
- [ ] Specify minimums: "Required: $1M Auto Liability + $100K Cargo (or higher for [specific load type])"
- [ ] **Expected timeline:** 15 min - 24 hours. Carrier broker emails COI directly to you.

When COI arrives:
- [ ] **Verify:** Continental Haul named as Certificate Holder AND Additional Insured (both)
- [ ] **Verify:** Auto liability ≥ $1M
- [ ] **Verify:** Cargo ≥ $100K (or higher per load needs)
- [ ] **Verify:** Effective dates are current (not expired, not future-dated)
- [ ] **Verify:** Insurer is rated A or better by AM Best (find at ambest.com — free) — risk if insurer is unrated or poor-rated

**Red Flags:**
| Flag | Action |
|---|---|
| RF19: COI doesn't name CHL as additional insured | YELLOW. Can be fixed — request endorsement. |
| RF20: COI insurance amounts below requirements | YELLOW. Carrier may need umbrella policy — ask. STOP if they refuse. |
| RF21: COI dates expired or future | RED. Decline. |
| RF22: Insurer not in AM Best, or rated B or lower | YELLOW. Acceptable for low-value loads, RED for high-value. |
| RF23: Carrier resists getting endorsement updated | STOP. Decline — non-cooperation is a future-claim red flag. |

---

### Step 7 — Send + execute Broker-Carrier Agreement (BCA) (~minutes - days)

- [ ] Email BCA template to carrier with CHL company info filled in
- [ ] Carrier signs (electronic signature acceptable — DocuSign, Adobe Sign, or wet ink + scan)
- [ ] Carrier returns signed BCA to you
- [ ] You countersign + send copy back to carrier (both parties have executed copy)

**Standard BCA covers:**
- Brokerage relationship terms
- Liability allocation
- Cargo claims procedure
- Payment terms
- Dispatch + communication expectations
- Termination clause

**Red Flags:**
| Flag | Action |
|---|---|
| RF24: Carrier wants extensive BCA modifications | YELLOW. Standard requests OK; structural changes flagged for legal review. |
| RF25: Carrier won't return signed BCA in 5 business days | YELLOW. Polite reminder; if 10 days, decline (signals operational issues). |
| RF26: BCA signature appears forged or doesn't match dispatcher's claimed name | RED. Decline. |

**Timeline budget:** allow 1-3 business days. If carrier is slow, this delays first dispatch.

---

### Step 8 — Get W-9 + payment terms agreement (~minutes)

- [ ] Email blank W-9 to carrier → they fill + return signed
- [ ] Confirm payment terms in writing (email is fine): Net-30 standard, Quick-pay X% fee, etc.

**Verify W-9:**
- [ ] EIN matches the legal name on FMCSA registration
- [ ] Address matches FMCSA registration
- [ ] Tax classification: usually "S Corp" or "Sole Proprietor" or "LLC" — record whatever they declare

**Red Flag:**
| Flag | Action |
|---|---|
| RF27: W-9 EIN or address differs from FMCSA registration | YELLOW. Could be DBA or new entity. Investigate before booking. |

---

### Step 9 — Add to CHL system (~5 min)

When iter 142.1 stage 1b ships:

- [ ] Use CHL operator UI: enter MC number → system auto-pulls SAFER data → displays
- [ ] Operator confirms vetting outcome (PASS / DECLINED) + uploads:
  - COI PDF
  - Signed BCA PDF
  - W-9 PDF
- [ ] System creates `db.carriers` record with `vetting_status: "complete"`, `bca_signed_at`, `coi_expiry`, etc.

**Pre-iter-142.1-stage-1b alternative:**
- Track in a spreadsheet: `mc_number | legal_name | vetting_status | bca_date | coi_expiry | contact_phone | contact_email | lanes_served | notes`
- Migrate spreadsheet to db.carriers when stage 1b ships

---

### Step 10 — Dispatch the load (~5 min)

Now legal to dispatch this carrier on this load:

- [ ] Send rate confirmation (rate-con)
- [ ] Send Bill of Lading (BOL) info
- [ ] Send pickup details + delivery details
- [ ] Confirm carrier received + acknowledged
- [ ] Track + manage per CHL load workflow (existing `auto_dispatch` infrastructure)

When iter 142.1 stage 1f ships, the dispatch packet (rate-con + BCA confirmation + insurance request) auto-generates from `db.dispatch_packets`.

---

### Step 11 — Decline-politely template (for failed vetting)

When you decline a carrier (any RED flag, or accumulated YELLOW flags):

**Template (email or SMS):**
> "Hi [Dispatcher Name], thank you for responding to our load posting. After our standard carrier-onboarding review, we're unable to move forward at this time. We appreciate your interest and wish you the best. — Continental Haul Logistics LLC."

**Don't:**
- Specify which red flag triggered the decline (avoids legal exposure)
- Promise future business
- Negotiate further if they push back

**Do:**
- Save their MC + reason internally (your private notes / db.carriers `vetting_status: "declined"` + notes field) — prevents re-vetting same carrier later
- Note in CHL global blacklist if reason is fraud-related (RF1, RF17, RF18, RF26)

---

## Time-budget summary

| Phase | Operator time |
|-------|---------------|
| Pre-vetting prep (one-time) | 1-2 hours |
| Steps 1-4 (FMCSA research) | 13 min per carrier |
| Step 5 (qualification call) | 10-30 min per carrier |
| Step 6 (COI handoff) | 15 min - 24 hr wall-clock; ~10 min operator review |
| Step 7 (BCA execution) | 1-3 business days wall-clock; ~10 min operator |
| Step 8 (W-9 + payment) | ~5 min |
| Step 9 (system add) | ~5 min |
| Step 10 (dispatch) | ~5 min |
| **Per-carrier operator time:** | **~50-90 min** (excluding wait windows) |
| **Per-carrier wall-clock:** | **~1 day** (typical) to **~5 days** (slow carriers) |

---

## Red Flag Reference Card (for quick lookup)

**STOP-and-decline (RED) flags:** RF2, RF3, RF7, RF8, RF10, RF11, RF12, RF13, RF14, RF15, RF17, RF18, RF21, RF23, RF26
**Investigate-then-decide (YELLOW) flags:** RF1, RF4, RF5, RF6, RF9, RF16, RF19, RF20, RF22, RF24, RF25, RF27

---

## When iter 142.1 stage 1b ships

The CHL system automates steps 2, 3, 4, and 9 (FMCSA data pull + system entry). Steps 1, 5, 6, 7, 8, 10 remain operator-driven (carrier contact, COI receipt, BCA execution, dispatch). Per-carrier operator time drops from ~50-90 min → ~30-60 min.

When Phase 6 (factoring + carrier-monitoring services like Highway/CarrierAssure) lands, paid services automate steps 4 and 6 — per-carrier operator time drops further to ~15-30 min.

---

## Cross-references

- `chl-memory/agenda/iter_142_1_full.md` — iter 142.1 agenda (this checklist supports the load-board-first amendment, commit `<TBD>`)
- `chl-memory/research/iter_142_1_pre_flight_brief.md` — operator pre-flight brief (commit `d567596`)
- `chl-memory/research/iter_141_2_launch_operator_runbook.md` — iter 141.2 runbook (DAT live wiring; relevant when stage 1d's load-board posting integration ships)
- 49 CFR 371 — FMCSA broker regulations (legal basis for BCA + COI requirements)
- TIA (Transportation Intermediaries Association) — industry-standard BCA template source

---

**Source:** Drafted by @dev-engineer 2026-05-07 in response to operator's "Perform A and put together a vetting checklist" directive. Reviewer: Claude Code Opus 4.7.

**STATE: HOLDING FOR INSTRUCTION** — operator may want to refine red flag thresholds, BCA template choice, or step ordering before treating this as canonical.
