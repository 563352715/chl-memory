# 🛡️ FACTS.md — Continental Haul Logistics LLC
## IMMUTABLE FOUNDATION DOCUMENT — READ BEFORE ANY ACTION

> **For all future agents (and forks):** This file is the single source of truth for who Jason is, what Continental Haul is, and what this platform does. If anything you write contradicts this file, **YOU ARE WRONG.** Re-read this file. Do not "fix" or "modernize" the facts here without explicit user confirmation captured in this same file with a dated entry below.
>
> Last verified by user: **2026-04-28 (Iter 134)**
> Welded by: agent under direct user instruction after drift incident.

---

## 🎯 THE POLESTAR — WHAT WE ARE BUILDING (THE MISSION)

> **CHL is building an AUTOMATED FREIGHT BROKERAGE PLATFORM.**
>
> Every architectural decision, every feature, every line of code, every
> document template, every workflow gate must serve this single goal:
> **automate the freight-brokerage business model wherever automation is
> possible, and surface clean human-in-the-loop moments where it is not.**
>
> When in doubt, ask: *"Does this make the brokerage more automated?"*
> If no → don't build it. If yes → build it.
>
> Confirmed by Jason: 2026-04-28.

---

## 🚨 AGENT-START PROTOCOL — RUN AT THE TOP OF EVERY SESSION

**Before you respond to ANY user message in a new session, you MUST:**

1. **🆕 OPEN `/app/memory/AGENT_HANDOFF.md` FIRST.** As of Iter 138.10, this is the master single-entry-point doc that points to every other reference. Reading it is non-negotiable.
2. **Open this file.** Read Sections 1-3 below (Person · Business Entity · FMCSA Authority).
3. **Open `/app/memory/CAPABILITIES.md`** — the complete map of every backend module, frontend view, integration, gate, collection, and endpoint. Mandatory before suggesting ANY new code (so you don't reinvent something that already exists).
4. **Open `/app/memory/SESSION_LOG.md`** — read the LAST 15 ROWS (recent agent work).
5. **Open `/app/memory/DAY1_PLAN.md`.** This is the GO/NO-GO contract — the 8 gates, the agent's operating loop, the escalation triggers. Mandatory.
6. **Open `/app/memory/DUAL_MODE_PLAN.md`** if any work involves manual override, document templates, or SOP cards.
7. **Open `/app/memory/SOP_AUDIT.md` and `/app/memory/DOC_STANDARDS_AUDIT.md`** if work involves documents, workflow steps, or industry-standard formats.
8. **Hit `GET /api/ops/state/public`** to read live brokerage state (mode, authority, bond) without auth. If logged in as broker, hit `GET /api/ops/state` for full state including integration health and Day-1 gate.
9. **Hit `GET /api/agent/journal/session-continuity`** (broker auth) to read the last 10 journal entries + open blockers + recent critical events. This is the previous agent's handoff.
10. **Output a Fact Confirmation Block** as the first thing you write back to Jason. Format exactly like this:

```
🛡️ FACT CONFIRMATION (per FACTS.md, last verified 2026-04-28):
   • Operator: Jason Aaron Meyer · <REDACTED-EMAIL>
   • Entity: Continental Haul Logistics LLC · EIN 42-2003434 · Missouri LLC
   • FMCSA: MC-1817555 · DOT 4569843 · BROKER authority (pending — protest ends 5/13/26)
   • Mission: AUTOMATED freight brokerage. Never owns trucks. Never employs drivers.
   • Mode: TESTING (until 5/13/26 authority grant)
```

9. **Output a Session Continuity Block** (added 2026-04-29, Iter 134.11):

```
📖 SESSION CONTINUITY (per /api/agent/journal/session-continuity):
   • Last 3 actions: <action·iter·summary> · <…> · <…>
   • Open blockers: <count> (or "none")
   • Critical last 14d: <count> (or "none")
   • Day-1 gates: <passed>/<total> · <GO|NO-GO>
```

10. **THEN** respond to Jason's actual question.
11. **As you work, append meaningful actions to the journal** via `POST /api/agent/journal/append`. Required for every: feature added, bug fixed, integration changed, schema changed, decision recorded, blocker opened/closed. Use clear `iter_tag` (e.g. `Iter 134.24`).

If any value above looks wrong, STOP and re-read the file before continuing.
If Jason ever sees a fact in the confirmation block that's wrong, that's the
signal to halt the session and re-baseline.

---

## ⛔ THE TEN COMMANDMENTS (Hard Rules — Never Violate)

1. **Jason Aaron Meyer will NEVER own trucks.** Continental Haul will NEVER own trucks. Forever zero.
2. **Continental Haul will NEVER employ drivers.** Carriers' drivers move freight under broker-carrier agreements.
3. **Continental Haul holds BROKER authority only.** NOT carrier authority. NOT common (dual) authority.
4. **All freight is hauled by contracted external carriers**, not by us.
5. **Any feature, copy, email, or document that frames Jason as a carrier or owner-operator is WRONG** and must be flagged before being shipped.
6. **In every external document** (rate-con, invoice, vendor email, Truckstop questionnaire, A2P submission, factoring application) — position as **BROKER**.
7. **Jason's full legal name is Jason Aaron Meyer.** He doesn't care about middle name in the UI but it IS his middle name.
8. **MC Number is MC-1817555.** No other number. Past mistakes used MC-1788735 and MC-1717565 — both wrong.
9. **DOT Number is 4569843.** No other number.
10. **Broker authority is NOT YET GRANTED as of 2026-04-27.** It is `application_pending` with FMCSA. Protest period ends **2026-05-13**. Until then, the platform is in **TESTING MODE ONLY** — we may not legally execute live broker transactions.

---

## 1. PERSON

| Field | Value |
|---|---|
| Legal Name | **Jason Aaron Meyer** |
| Preferred Display | "Jason Meyer" or "Jason Aaron Meyer" — both fine |
| Title | Director / Owner / Founder of Continental Haul Logistics LLC |
| Personal Cell | +1-417-219-3856 |
| Personal Email | <REDACTED-EMAIL> |
| Mailing Address | 618 W Greenwood St, Springfield, MO 65807 |
| User ID (DB) | f3780a65-56f9-48d7-9737-5f54584fd3db |
| Auth Role | `broker` (with `is_owner: true`) |

---

## 2. BUSINESS ENTITY

| Field | Value | Source |
|---|---|---|
| Legal Name | **Continental Haul Logistics LLC** | DB `business_settings` |
| State of Formation | **Missouri** | DB |
| Formation Date | **2026-04-18** | DB |
| Missouri Charter # | **ORI-04182026-0576** | DB |
| EIN | **42-2003434** | DB (issued by IRS 2026-04-18) |
| DUNS | **145008621** | D&B (issued 2026-05-04, applied 2026-05-04 — fast-track 24h turnaround under SIC 4731) |
| Business Address | 618 West Greenwood St, Springfield MO 65807 | DB |
| Business Phone (dispatch) | (417) 219-3856 | DB |
| Business Toll-Free | (866) 490-6433 | DB |
| Business Email | dispatch@continentalhaul.com | DB |
| Quotes Inbox | quotes@continentalhaul.com (Zoho alias) | Iter 132/133 |
| Website | https://continentalhaul.com | DB |
| Tagline | "Every state. Every mile. One haul." | DB |
| Entity Type | LLC | confirmed |

---

## 3. FMCSA AUTHORITY (CRITICAL — PENDING)

| Field | Value | Status |
|---|---|---|
| MC Number | **MC-1817555** | Issued 2026-04-21 |
| FMCSA Formal Docket | MC01817555 | DB |
| USDOT Number | **4569843** | Active |
| UCR | UCR-2026-TEST | Registered 2026 |
| Authority Type | **Broker only** | Confirmed by user 2026-04-27 |
| Authority Status | **application_pending** | 🚨 NOT YET GRANTED |
| OP-1 Application | Filed | DB |
| BOC-3 (Process Agent) | Filed via 1+49 Process Agents · Order #G5Y41NHAK · e-filed with FMCSA | DB |
| BMC-84 Surety Bond | $75,000 · Bond #540381P · **Westfield Insurance Company** (A+ rated surety) · sold via SuretyBonds.com (Brooklyn Ventures LLC) · agent: Mark Karr Insurance | **eFILED with FMCSA** · effective 2026-04-27 · posted 2026-04-28 · ACTIVE (no cancellation date on FMCSA L&I record) |
| Bond on file with FMCSA | **Yes** (verified via BMC-84 watcher 2026-04-28; insurance_on_file=true) | DB `bond_on_file_with_fmcsa: true` |
| Protest Period Ends | **2026-05-13 13:15 UTC** | After this, authority can grant |
| L&I Public Verified | Yes (2026-04-24) | DB |

🚨 **OPERATIONAL RULE:** Until protest period ends and authority is granted, every legal document (rate-con, invoice, broker-carrier agreement) must carry a **"PENDING — DO NOT EXECUTE"** banner. We are in testing mode, not live operations.

---

## 4. INSURANCE & BONDING (TBD — Section pending tomorrow)

| Field | Value |
|---|---|
| Broker Bond | $75,000 BMC-84 · Bond #540381P · **Westfield Insurance Co** (surety) · sold via SuretyBonds.com · agent: Mark Karr · effective 2026-04-27 · posted 2026-04-28 · **ACTIVE on FMCSA L&I (Insurance: OK · BOC-3: OK · Status: Accepted)** |
| Cargo / Contingent | TBD — to be researched |
| E&O / Professional | TBD — to be researched |
| General Liability | TBD — to be researched |
| Certificate of Insurance on file | TBD |

---

## 5. PLATFORM ROLE MAPPING (Tomorrow's review)

DB roles in code: `owner | admin | broker | employee | carrier | shipper`. Mapping reviewed tomorrow.

Confirmed so far:
- `broker` + `is_owner: true` = **Jason** (single user with this role)
- `carrier` = **external trucking companies** Jason contracts with (self-register)
- `shipper` = **external companies** giving Jason freight to broker (currently no portal)

---

## 6. FINANCIAL INFRASTRUCTURE (Tomorrow's deep-dive)

Confirmed in DB:
- **Mercury (Choice Financial Group)** — operating checking · 997475501079391 · routing 121145433
- **Mercury (Column N.A.)** — savings · 677421028426807 · routing 121145433
- **Owner Pay** account on Mercury · 427461281...
- **Tax sub-bucket** — same as savings, 15% sweep configured (currently disabled)
- **Savings sweep** — 3% enabled
- **Profit split target** (per `PROFIT_SPLIT_RECOMMENDATIONS.md`): 67% Operations / 18% Owner Pay / 15% Tax
- **Factoring**: Jason factors his invoices (carrier name TBD tomorrow)
- **Stripe** — test key only, NOT in production

---

## 7. INTEGRATIONS (Tomorrow's review)

Snapshot as of Iter 133:

| Service | Status | Notes |
|---|---|---|
| Resend Pro (outbound email) | LIVE · `continentalhaul.com` domain VERIFIED in Resend (DKIM/SPF passing 2026-04-28); sends from `dispatch@continentalhaul.com` land directly in Gmail Inbox | |
| Twilio Voice | LIVE | |
| Twilio A2P 10DLC SMS | Brand approved · Campaign in TCR vetting (since 4/27) | 2-3 weeks for full carrier approval |
| Twilio Toll-Free SMS | REJECTED 2026-04-28 (codes 30445/30497/30499/30513) · resubmission packet ready at `/twilio-tollfree` (edit_allowed=true) | Resubmit within 7 days for prioritized queue |
| Zoho Mail (Mail Lite) | LIVE | IMAP poller active as of Iter 133 (`imappro.zoho.com:993`) |
| Mercury Banking | LIVE (read-only) | Treasury Transfers API NOT integrated |
| Stripe | TEST KEY ONLY | not production |
| 123Loadboard | LIVE | only live loadboard |
| DAT | MOCKED | |
| Truckstop | MOCKED | questionnaire pending Adnan Efendic |
| Direct Freight | MOCKED | awaiting FMCSA refresh |
| Emergent LLM Key | LIVE | Claude Sonnet 4.5 + Gemini 2.5 Pro |

---

## 8. INTENDED OUTCOME (Confirmed answers — rest tomorrow)

| Question | Answer |
|---|---|
| **What does CHL do?** | "We broker freight loads." (verbatim from Jason 2026-04-27) |
| Target geography | **Everything in the US.** |
| Equipment types brokered | **Dry van · Reefer · Flatbed · Cattle hauler · Hopper · "and more"** — open multimodal-equipment scope |
| Target shipper customer | TBD tomorrow |
| Target carrier partner | TBD tomorrow |
| Target monthly load volume | TBD tomorrow |
| Target gross revenue | **No fixed target** (Jason doesn't want a number anchored) |
| Average target margin | TBD tomorrow |
| 12-month success vision | TBD tomorrow |
| Platform's #1 differentiator | TBD tomorrow |

---

## 9. PLATFORM PURPOSE (Tomorrow's review)

Snapshot:
- **Single-tenant** — built for Continental Haul only
- **Primary user** — Jason
- **Carriers interact** via magic-link `/offer/:token` (no login required)
- **Shippers** — eventual portal, not built yet
- **Future hires** — `employee` role exists but unused

---

## 10. CHANGE LOG FOR THIS FILE

| Date (CST) | Change | Source |
|---|---|---|
| 2026-04-27 22:10 | File created. Sections 1, 2, 3 (partial), 8 (partial), Ten Commandments verified by user | User-led drift correction during Iter 133 |
| 2026-04-28 (AM) | Added Polestar / Mission section (Automated Freight Brokerage) | Jason confirmed direction |
| 2026-04-28 (AM) | Added AGENT-START PROTOCOL with required Fact Confirmation Block | Drift-prevention build (Mechanism 3) |
| 2026-04-28 (AM) | Added PRE-RESPONSE CHECKLIST below | Drift-prevention build (Mechanism 4) |
| 2026-04-28 (PM) | Added §11 — TWILIO COMPLIANCE TRACK SEPARATION | Drift-prevention after Iter 134.4 confused the 866 toll-free track with the 417 10DLC track when investigating error 30034 |

---

## 🎯 ANTI-DRIFT INSTRUCTIONS FOR FUTURE AGENTS

When you start a session:
1. **Read this file before reading PRD.md.**
2. If user asks for a vendor email, contract, marketing copy, or any external document → **re-read Section 3 and the Ten Commandments first.**
3. If you find yourself writing "owner-operator carrier" or "our trucks" or "our drivers" or "our fleet" → **STOP.** That's drift. We are a broker. Always.
4. **Do not edit this file** without explicit user instruction. Append to the change log; never overwrite the body.
5. Until **2026-05-13**, treat the platform as **testing mode**. No legal binding documents should be sent to a real shipper or carrier without a "PENDING AUTHORITY" disclosure.

---

## 🚨 PRE-RESPONSE CHECKLIST — RUN BEFORE SENDING ANY OUTPUT

**Before you click send on a message that mentions ANY fact below, verify
it against this file. If you cannot verify, ASK before stating.**

### 🔴 ALWAYS-VERIFY FACTS (mistakes here = trust damage)

| Fact Category | Where to verify in this file |
|---|---|
| Jason's full legal name | Section 1 — "Jason Aaron Meyer" |
| MC Number | Section 3 — "MC-1817555" (NOT 1788735, NOT 1717565) |
| DOT Number | Section 3 — "4569843" |
| EIN | Section 2 — "42-2003434" |
| LLC name | Section 2 — "Continental Haul Logistics LLC" |
| Authority status | Section 3 — "application_pending until 2026-05-13" |
| Authority type | Section 3 — "BROKER ONLY" |
| Trucks owned | Ten Commandments #1 — "ZERO. FOREVER ZERO." |
| Drivers employed | Ten Commandments #2 — "ZERO. FOREVER ZERO." |
| Business model | Polestar — "AUTOMATED FREIGHT BROKERAGE" |
| Bond status | Section 3 — verify against current FMCSA L&I check |
| Mercury account #s | Section 6 — operating 9391, savings 6807 |
| Zoho IMAP host | PRD.md — `imappro.zoho.com:993` (NOT `imap.zoho.com`) |

### 🟡 NEVER-ASSUME FACTS (always ask if uncertain)

- Time of day on Jason's end → ASK or simply don't reference time
- Whether Jason has called/signed up with any vendor (HaulPay, Truckstop, etc.) → ASK
- Whether a feature has been tested in production → CHECK code/test reports
- Whether any auth credentials were rotated → CHECK `/app/memory/test_credentials.md`
- Whether any backend service is running → check `tail /var/log/supervisor/*` or curl

### 🟢 ALIGNMENT CHECK BEFORE SHIPPING ANY FEATURE

Before writing code or proposing a feature, ask yourself:

1. **Does this serve the Polestar?** (Automate the brokerage where automatable)
2. **Does this contradict the Ten Commandments?** (If yes, STOP)
3. **Is this in scope for the current Phase?** (Check `/app/memory/DUAL_MODE_PLAN.md`)
4. **Will this output mention Jason as a carrier or owner-operator?** (If yes, REWRITE)
5. **Will this generate a document that goes to a shipper/carrier/factor?** (If yes, check `/app/memory/DOC_STANDARDS_AUDIT.md` for industry-standard format)

### 🛡️ TIME-OF-DAY DRIFT GUARD

**Before stating ANY time-related fact** (current time, business hours,
"recently", "earlier today", time elapsed since X):

**Step 1 — Get canonical time. Choose ONE method:**
  - Run `date -u '+%Y-%m-%d %H:%M:%S UTC'` in bash, OR
  - Hit `GET /api/system/now` (public endpoint, no auth) on the platform

**Step 2 — Use that exact value.** Do NOT estimate. Do NOT extrapolate from
a stale timestamp earlier in the conversation. Do NOT guess.

**Step 3 — When citing a time to Jason**, use his operator timezone
(`America/Chicago`) which `/api/system/now` returns as `operator_human`.

DO NOT tell Jason to "get rest", "sleep", "wrap up for the night", or
reference his time of day UNLESS:
- He explicitly stated the current time within the LAST USER MESSAGE, OR
- He explicitly said he's stopping for the day, OR
- You have just verified canonical time via `date` or `/api/system/now`
  AND it is genuinely past 11 PM CST (and even then — only if relevant)

DO NOT push "tomorrow's agenda" UNLESS Jason asked for it.

🛡️ **Welded. Do not drift.**

---

## 11. TWILIO COMPLIANCE TRACK SEPARATION (READ BEFORE TOUCHING ANY SMS BUG)

**There are TWO independent Twilio compliance tracks.** They share an Account SID and a payment method but nothing else. Do NOT investigate one and assume you know the other.

| | **Toll-Free SMS** | **A2P 10DLC SMS** |
|---|---|---|
| Number | `+18664906433` | `+14172193856` |
| Verification SID | `<REDACTED-TWILIO-TFV-SID>` | Brand `<REDACTED-TWILIO-BRAND-SID>` · Campaign `<REDACTED-TWILIO-CAMPAIGN-SID>` · MS `<REDACTED-TWILIO-MESSAGING-SID>` |
| Status (2026-04-28) | REJECTED — codes 30445/30497/30499/30513 · `edit_allowed=true` | Brand APPROVED · Campaign IN_PROGRESS (TCR vetting since 4/27) |
| What blocks delivery | Twilio rejection email — has to be edited via `POST /Tollfree/Verifications/{SID}` | Twilio error **30034** at message send time |
| Where the fix lives | `/twilio-tollfree` UI · `tollfree_compliance.py` packet | A2P Gate in `server.py:6921` (reads `notification_service.get_cached_10dlc_status`) |
| What "APPROVED" requires | Verification status = `TWILIO_APPROVED` | **Brand APPROVED AND Campaign VERIFIED — both** (see Iter 134.4 fix in `notification_service.fetch_10dlc_status_from_twilio`) |

### The Voice forwarding setup is unrelated.

`+14172193856` (10DLC long code) is configured to forward incoming **voice calls** to `<REDACTED-PERSONAL-PHONE>` (Jason's cell). This is a Twilio Voice TwiML feature. It has **zero impact** on SMS delivery, A2P 10DLC vetting, or any 30034 warning. If you find yourself wondering "could the call-forwarding be causing this SMS error?" the answer is no, every time, forever. SMS and Voice are isolated tracks.

### Why old messages still showed in the Twilio Console after a fix

Twilio's Console shows historical message records. A successful gate-block today does NOT delete yesterday's failures. To verify the gate is working, watch `/var/log/supervisor/backend.*.log` for `[A2P-GATE BLOCKED SMS]` lines OR check `db.outbound_messages` for `status: gate_blocked` rows.

🛡️ **End of Twilio compliance section. Welded 2026-04-28 (Iter 134.4) after error-30034 investigation.**
