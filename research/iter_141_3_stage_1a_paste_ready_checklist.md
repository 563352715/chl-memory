# Iter 141.3 Stage 1a — Paste-Ready Consolidated Checklist

> **Single-page reference for the moment Plivo sales unblocks signup.** Replaces the read-5-docs cognitive load (`plivo_white_glove_packet_FINAL.md` + `iter_141_3_kickoff_checklist.md` + `iter_141_3_pf2_temp_number_capture.md` + `iter_141_3_pf4_consent_endpoint_status.md` + `iter_141_3_stage_1a_signup_troubleshooting.md`) with one ordered checklist.
> **Assumes:** Plivo sales has just granted manual signup access. You're about to log into Plivo console for the first time.
> **Estimated total time:** ~30 min hands-on (sections 0–1) + async wait for white-glove SE (~24h SLA).

---

## Section 0 — Pre-paste pre-flight (~5 min)

Run these BEFORE touching the Plivo console. Catches mismatches that would force a re-paste.

- [ ] **PF1 — FMCSA registry cross-check.** Verify each of these matches CHL's FMCSA SAFER record for MC-1817555 EXACTLY (case + spacing matter):
  - Legal name: "Continental Haul Logistics LLC"
  - EIN (from W-9 / IRS letter)
  - Registered business address (must match SOS filing AND SAFER — not a PO box if SAFER shows street)
  - Authorized contact: Jason Aaron Meyer
- [ ] **§1 packet values pre-staged.** Open `plivo_white_glove_packet_FINAL.md` in a text editor. Have these values ready to paste into placeholders:
  - `<EIN>` (from vault: "Continental Haul — Tax Documents")
  - `<state>` (state of LLC formation)
  - `<year>` (year LLC formed)
  - `<contact phone>` (operator's primary contact phone, can differ from SMS sender)
  - `<street, city, state, zip>` (registered address per FMCSA + SOS)
- [ ] **Vault entries pre-staged.** Empty placeholder entries created (will populate during section 1):
  - "Plivo — Console Login" (URL + username + password)
  - "Plivo — Auth Credentials" (Auth ID + Auth Token)
- [ ] **Operator-fillable checklist at packet bottom — re-read.** Mental note of the 5 boxes you'll tick during section 1.

---

## Section 1 — Plivo console signup + packet paste (~25 min hands-on)

Sequential. Each step gates the next.

- [ ] **1.1 — Plivo console login** (~1 min). Use the email Plivo's sales team provisioned for you. Set password; store in vault entry "Plivo — Console Login".
- [ ] **1.2 — Navigate to A2P 10DLC** (~1 min). Settings → A2P 10DLC.
- [ ] **1.3 — Create Profile** (~5 min). Fill from §0 PF1 values:
  - Business type: Private (LLC)
  - Legal name: Continental Haul Logistics LLC
  - MC-1817555 + USDOT 4569843
  - Authorized contact: Jason Aaron Meyer + email + phone
  - EIN, business address (per PF1 cross-check)
- [ ] **1.4 — Register Brand** (~3 min). Profile created → "Register Brand". Standard brand $4 one-time. **Skip optional vetting** (volume <6k/day at v1). Submit.
- [ ] **1.5 — Provision temp number** (~3 min). Phone Numbers → Buy Numbers → US local, SMS-enabled, ~$0.80/mo. **Capture E.164 format** (e.g., `+14175551234`).
- [ ] **1.6 — PF2: Fill temp number into packet §6** (~1 min). Open `plivo_white_glove_packet_FINAL.md`. Find the row containing `` `<temp>` `` in §6. Replace with the E.164 number from step 1.5. Tick `- [ ] §6 — Plivo temporary number (after Brand registration)` at the bottom-of-doc operator-fillable checklist.
- [ ] **1.7 — Populate vault credentials** (~2 min). Account → API Credentials → copy Auth ID (starts with `MA...`) + Auth Token. Paste both into vault entry "Plivo — Auth Credentials".
- [ ] **1.8 — Fill remaining packet §1 placeholders** (~3 min). Replace `<EIN>`, `<state>`, `<year>`, `<contact phone>`, `<street, city, state, zip>` with values pre-staged in §0. Tick all 4 remaining boxes at the bottom-of-doc operator-fillable checklist.
- [ ] **1.9 — Final packet sanity check** (~2 min). Search the packet for any remaining `<placeholder>` syntax (use Ctrl+F for `<`). Should find ZERO unfilled placeholders. If any remain, fill before paste.
- [ ] **1.10 — Open Plivo console chat + paste** (~3 min). Settings → Support → Chat. Opening message: *"Hi — migrating from Twilio (4+ weeks of 30909 CTA-rejection friction). Need white-glove 10DLC campaign assistance for broker operational SMS. MC-1817555. Pasting submission packet below."* Then paste the entire FINAL packet contents.
- [ ] **1.11 — Confirm receipt** (~1 min). White-glove team should reply within ~24h with a ticket number + assigned SE name. Save both in vault entry "Plivo — White-Glove Submission Docs".

---

## Section 2 — Post-paste async follow-ups

After section 1, the wait begins. Refer to `iter_141_3_stage_1b_prep_brief.md` for the full 7–21 day timeline + daily cadence + escalation triggers.

- [ ] **Pre-stage 5 documents in vault** (per `iter_141_3_stage_1b_prep_brief.md` §"Documents to pre-stage"): W-9, signed LOA template (Plivo provides), most recent Twilio bill, proof of business address (utility bill or LLC formation doc), authorized contact ID (driver's license).
- [ ] **Daily check-in cadence** (Plivo console chat, days 1–3 daily / 4–10 every-other-day / 11–21 daily again). Standard prompt: *"Hi [SE name], any updates on the campaign submission for MC-1817555 / TCR Brand B6384Q7?"*
- [ ] **Track port + campaign status.** Phone Numbers → Port-In for FOC date; A2P 10DLC → Campaigns for vetting status.
- [ ] **Watch for escalation triggers** (per stage 1b prep brief): no SE assigned by day 2; campaign stuck >7 days at TCR; port FOC not provided by day 5; rejection (campaign or port).

---

## Section 3 — Branch points / decision gates

Pre-resolved decisions on the packet — no operator action needed unless circumstances change.

| ID | Decision | Status | Operator action |
|---|---|---|---|
| M2 | Port `+14172193856` from Twilio vs new Plivo number | Default: port path (cited in signed agreements) | Confirm with paperwork audit per `iter_141_3_kickoff_checklist.md` item 2; flip to new-number-path requires §6 + §8 packet hand-edit before paste |
| C1 | `/sms-opt-in` page live at submission vs paper-only | Path: graceful punt (packet §3 Path B notes "in active build"; SE flags if live URL needed) | Build during white-glove wait per `iter_141_3_stage_1b_prep_brief.md` parallel work |
| C5 | Reuse TCR Brand `B6384Q7` vs re-register | Default: reuse (saves 1–2 weeks + $4) | Packet §8 explicitly requests reuse; SE confirms |
| PF1 | FMCSA registry cross-check on EIN + address | **GATES PASTE** | Section 0 pre-flight |
| PF2 | Temp number capture | **GATES PASTE** | Section 1.6 |
| PF4 | `/api/admin/sms-consent` endpoint live | Path A applied: JSON stripped from packet §3 Path C; "secure file share" fallback per `iter_141_3_pf4_consent_endpoint_status.md` | None — already in FINAL packet |
| M1 | Sample message length / GSM-7 encoding | Resolved: all 5 packet messages 126–140 chars + GSM-7-safe per `plivo_white_glove_review.md` M1 | None |

---

## Section 4 — If it goes sideways

| Symptom | First-pass response |
|---|---|
| Brand portability rejected (TCR Brand B6384Q7 reuse denied) | Authorize re-registration. +$4 + 7–14 day delay. See `iter_141_3_stage_1b_prep_brief.md` §"Backup plan: Brand portability rejected". |
| Campaign rejected at TCR | Plivo white-glove team re-submits with their fixes; you don't redo it solo. Provide any additional info SE requests. |
| Number port blocked by Twilio | Provide Twilio account SID + authorized signature to Plivo SE; they escalate. If permanently blocked, switch to new-number path (provision new Plivo number, accept that broker-carrier paperwork won't auto-route to it). |
| White-glove SE silent >5 business days | Escalate via Plivo console support ticket OR re-engage sales@plivo.com. Notify @dev-engineer in AgentDM. |
| Operator gets sick / travels >3 days during the wait | @dev-engineer can pick up daily check-ins on operator's behalf — coordinate via AgentDM. |
| Plivo email validator re-blocks at any console action | See `iter_141_3_stage_1a_signup_troubleshooting.md` §3 4-outcome decision tree (refresh / incognito / MX / vendor-side block). |

---

## Section 5 — Definition of "stage 1a complete, ready for stage 1b/1c handoff"

All four must be true:

- [ ] Packet pasted to Plivo console chat with all placeholders filled (§1 + §6 verified at section 1.9 + 1.10)
- [ ] Vault entries populated: "Plivo — Console Login", "Plivo — Auth Credentials", "Plivo — White-Glove Submission Docs" (with SE name + ticket number)
- [ ] White-glove team has acknowledged receipt within 24h (any "got it, working on it" reply counts)
- [ ] @dev-engineer pinged in AgentDM with: *"Stage 1a complete: Plivo signup green, Brand registered, packet pasted, SE [name] acknowledged. Stage 1b dev work can start in parallel — Auth ID + Auth Token + temp number `+1XXXXXXXXXX` in vault. Async wait for campaign + port now begins."*

When all four green: stage 1a is closed. Stage 1b dev rewrite (`backend/plivo_client.py` cutover) can run in parallel with stage 1c port/campaign approval (~7–21 day async wait).

---

**Source:** Drafted by @pm-lead 2026-05-07 during iter 141.3 stage 1a Plivo-blocker quiet-time, autonomous-continue mode. Consolidates: `plivo_white_glove_packet_FINAL.md`, `iter_141_3_kickoff_checklist.md`, `iter_141_3_pf2_temp_number_capture.md`, `iter_141_3_pf4_consent_endpoint_status.md`, `iter_141_3_stage_1b_prep_brief.md` (post-paste section), `iter_141_3_stage_1a_signup_troubleshooting.md` (sideways section). Use this checklist as the canonical paste-day reference; the source docs remain authoritative for deep context.
