# 🛡️ DUAL-MODE OPERATIONS — Manual Override + Industry-Standard Documents
## Confirmed scope locked in by Jason Aaron Meyer · 2026-04-28

> **Read this before touching anything in this scope. This is a multi-session
> build, not a single-session sprint. Drift on the rules below = wasted work.**

---

## ✋ THE THREE CONFIRMED MANDATES

### Mandate 1 — Industry-Standard SOP Audit
Compare CHL's 14-card SOP against what real freight brokers actually do.
Find gaps, missing steps, or steps in wrong order. Produce a written audit
document (`/app/memory/SOP_AUDIT.md`) BEFORE building anything new.

### Mandate 2 — Manual Override at Every Automated Step
Every step the platform automates today MUST also have a "Do It Manually"
button that:

  a. **The system PROMPTS you** to use when auto-flow fails or stalls.
     (Jason's words: "I want the system to prompt this activity so I can
     get the docs submitted back to the flow into the system.")
  b. Opens a fillable form pre-populated with whatever data we already have.
  c. Submits back into the **same downstream pipeline** the auto-flow uses.
  d. **Auto-resumes from the next step** as if the platform had done it.
  e. Looks IDENTICAL to the auto-generated docs (same template, same legal
     language, same SHA-256 audit stamping, same submission endpoints,
     same downstream effects).
  f. Includes a one-click **Submit** at the bottom that triggers the
     auto-pickup at the next step seamlessly.

### Mandate 3 — Industry-Standard Document Templates
Audit every document CHL generates (rate-con, BOL, invoice, NOA, broker-
carrier agreement, W-9 request, COI request, factoring packet, evidence
packet) against industry-standard formats. Rebuild templates to match what
shippers, carriers, and factors actually expect to see. Use those same
templates as the manual-fill forms in Mandate 2.

---

## 🚨 ASSUMPTION SCOPE (NOT TO BE VIOLATED)

> **"I am assuming that Step 1 in Card 1 will never fail."**
> — Jason, 2026-04-28

This means:
- We do NOT need to build manual override for **Card 1 Step 1**
  (Pick Factor / Verify Poster / Run Credit Lookup).
- That step is treated as the entry gate. If it fails, the whole load
  is skipped — no manual recovery needed.
- All manual-override scope starts at **Card 1 Step 2 onward**.
- This caveat may be revisited later. Until then, hold the line.

---

## 📋 FULL DOCUMENT INVENTORY — 17 Manual-Override Forms

| Card | Doc | Manual Override Required |
|---|---|---|
| 1 | Factor Credit Lookup | ❌ EXEMPT (per Jason's assumption) |
| 2 | Quote Email | ✅ Required |
| 3 | Load creation form (when RFQ parsing fails) | ✅ Required |
| 3 | BOL pre-fill | ✅ Required |
| 4 | Cross-post payload (boards without API) | ✅ Required |
| 5 | Carrier Vetting Packet | ✅ Required |
| 6 | **Rate Confirmation** | ✅ Required (HIGH) |
| 6 | **Broker-Carrier Agreement** | ✅ Required (HIGH) |
| 7 | Dispatch instructions to driver | ✅ Required |
| 8 | Pickup confirmation | ✅ Required |
| 9 | In-transit status update | ✅ Required |
| 10 | POD / Delivery confirmation | ✅ Required |
| 11 | **Broker Invoice + NOA** | ✅ Required (HIGH) |
| 11 | **Factoring Packet** (slim) | ✅ Required (HIGH) |
| 11 | **Evidence Packet** (fat) | ✅ Required |
| 12 | Carrier Payment ACH instructions | ✅ Required |
| 13 | Audit trail entry / compliance note | ✅ Required |

**Total: 16 forms in scope** (Card 1 Step 1 excluded per Jason).

---

## 🛣️ FOUR-PHASE BUILD PLAN

| Phase | Estimated Effort | Output |
|---|---|---|
| **Phase 1 — The Audits** | ~3-4 hours | `SOP_AUDIT.md` + `DOC_STANDARDS_AUDIT.md` |
| **Phase 2 — Core Override Framework + 3 Critical Docs** | ~6-8 hours | Universal ManualOverrideForm component + Rate-Con, Broker Invoice, Carrier Vetting Packet manual modes |
| **Phase 3 — Industry Re-Templating** | ~6-10 hours | All 16 docs rebuilt to industry-standard formats |
| **Phase 4 — Remaining Override Forms** | ~6-8 hours | Manual override on remaining 13 docs |
| **TOTAL** | **~20-30 hours** | Full dual-mode platform |

---

## 🧠 SIMULATION REQUIREMENT

> **"We will also need to simulate how manual mode operates, but always
>   assuming that Step 1 Card 1 is functioning properly."**
> — Jason, 2026-04-28

This means in addition to the build, we must produce:

- A **simulation walkthrough** of every card's manual override flow
- For each card: show what the operator sees, what they fill in, what the
  Submit button does, and where the system picks up the auto-flow next
- Format: probably a series of side-by-side screenshots (Auto Mode → Manual
  Mode → Submit → Auto Mode resumes) per card

This goes into a doc: `/app/memory/MANUAL_MODE_SIMULATION.md`.

---

## 🛡️ WELDED RULES — DO NOT DRIFT

1. Manual mode and auto mode produce **IDENTICAL output documents**
   — same template, same legal language, same SHA-256 stamp.
2. Manual submission **resumes auto-pipeline** at the next step.
3. The system **PROMPTS** the operator when manual mode is needed
   (does not require the operator to seek it out).
4. Card 1 Step 1 is **EXEMPT** from manual override scope.
5. Every doc must match **industry-standard format** before being shipped
   to a shipper/carrier/factor.
6. Phased build — DO NOT try to ship all 16 forms in a single session.
   Quality over speed.

---

Last verified by Jason: 2026-04-28 evening.
