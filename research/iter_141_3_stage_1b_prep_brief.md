# Iter 141.3 Stage 1b Prep Brief — Operator Playbook for the White-Glove Wait Period

> **Covers:** the period between "operator pastes white-glove packet to Plivo console chat" (end of stage 1a) and "stage 1d cutover-ready" (port + campaign both green). Operator-facing.
> **Not covered:** stage 1d cutover itself (separate runbook in `iter_141_3_agenda_draft.md`).
> **Timeline expectation:** **7–21 days end-to-end.** Median ~14 days. Wide range driven by port-FOC date (typically 7–10 business days) + 10DLC campaign vetting (1–3 business days for low-volume Mixed if brand reused; up to 14 days if brand re-registration required).

---

## Daily check-in cadence

**Day 0 (packet pasted):** Confirm receipt with Plivo support — they should reply within 24h with a ticket number + assigned solutions engineer name. Save both.

**Days 1–3:** Daily check-in via Plivo console chat. Standard prompt: *"Hi [SE name], any updates on the campaign submission for MC-1817555 / TCR Brand B6384Q7?"* Don't over-ping; once per business day max.

**Days 4–10:** Drop to every-other-day. By day 4, you should have either: (a) campaign in PROCESSING with TCR, OR (b) Plivo asking for additional info — see "Documents to pre-stage" below.

**Days 11–21:** Daily again if either port or campaign still pending. Escalate to Plivo CSM if no movement on either by day 14.

**Escalation triggers** (open Plivo support ticket OR ask SE to escalate):
- No SE assigned by day 2
- Campaign stuck in PROCESSING >7 days at TCR (vs documented 1–3 day target)
- Port FOC date not provided by day 5
- Any rejection (campaign or port) — Plivo white-glove team should re-submit with their fixes; you don't redo it solo

---

## Documents to pre-stage (anticipate Plivo requests)

Have these ready in the vault BEFORE the SE asks. Reduces back-and-forth latency.

1. **W-9** — IRS Form W-9 with EIN matching `<EIN>` in packet §1. Typically requested for billing setup beyond free tier.
2. **Signed LOA (Letter of Authorization) for number port** — Plivo will provide a template; operator signs and returns. Required to authorize Twilio → Plivo number transfer of `+1 (417) 219-3856`.
3. **Most recent Twilio bill** — proves account ownership for the port. Plivo asks for the bill from within the last 30 days.
4. **Proof of business address** — typically a utility bill or LLC formation doc (Articles of Organization) showing the registered address from packet §1. Used to verify the address matches FMCSA SAFER + state SOS records.
5. **Authorized contact ID** — driver's license or passport for Jason Aaron Meyer matching the authorized-contact name in packet §1. Some Plivo flows ask, some don't.

**Pre-stage location:** vault entry "Plivo — White-Glove Submission Docs" with all 5 PDFs scanned/uploaded.

---

## Productive parallel work during the wait

Don't idle. These activities can proceed in parallel with the white-glove wait and will compress total iter 141.3 wall-clock.

- **Stage 1b dev work** (notification_service.py rewrite) is **NOT blocked** by white-glove approval. As soon as stage 1a yields auth credentials + temp number, @dev-engineer can rewrite the SMS module and run smoke battery against the temp number. By the time stage 1c finishes, stage 1b will already be green.
- **`/sms-opt-in` page build** (per `emergent_sms_opt_in_page_task.md`) — stand up the page during the wait. If Plivo asks for a live URL during white-glove review, you can point to it without delay.
- **Operator review of FINAL packet** for any last-minute placeholder fills — confirm `<temp>` was filled per `iter_141_3_pf2_temp_number_capture.md`, FMCSA registry cross-check (PF1) is clean.
- **Twilio decommission planning** (NOT execution — see next section).

---

## Twilio decommission checklist — DO NOT cancel until X

**Hard gate:** Twilio account stays ACTIVE until **all of the following are green**:
1. Plivo campaign APPROVED at TCR ✅
2. Plivo number ported AND linked to approved campaign ✅
3. Stage 1d cutover smoke battery 7/7 PASS ✅ (real load → real SMS → operator phone delivery confirmed)
4. **30-day stable Plivo operation** post-cutover ✅ (no degraded delivery, no carrier complaints)

Why: Twilio has no monthly fee if you don't rent numbers + don't have campaigns active. Once port FOC completes, you can release Twilio numbers (saves ~$1.15/mo per number) but **keep the account itself active**. The 30-day window is your reverse-port insurance — if Plivo deliverability degrades, you reverse-port back to Twilio.

**Decommission steps (only after all 4 gates green):**
1. Confirm Plivo prod traffic has been clean for 30 days (`current_status.md` SMS-path metric).
2. Release Twilio numbers (if not already released post-port).
3. Archive Twilio Auth Token in vault (don't delete — historical reference).
4. Cancel paid Twilio subscriptions (none should exist at $0 number rental, but verify).
5. Final: close Twilio account ONLY if you're sure you won't need it again. Most operators leave it dormant.

---

## Backup plan: 10DLC Brand portability rejected

**Risk:** Plivo white-glove team's request to reuse TCR Brand ID `B6384Q7` is rejected (rare but possible — TCR sometimes flags brands with prior rejected campaigns).

**Detection:** Plivo SE replies in days 1–3 saying brand reuse not approved; we need to re-register the brand at Plivo from scratch.

**Cost of re-registration:** $4 brand fee (one-time) + 7–14 additional days for fresh brand vetting at TCR.

**Actions if triggered:**
1. Authorize Plivo to re-register the brand (don't try to argue the reuse; just accept the +7–14 day delay).
2. Update timeline expectation: 7–21 days → 14–35 days.
3. Stage 1b dev work continues unaffected (smoke against temp number doesn't need brand approval).
4. Stage 1c gates extend by 7–14 days; stage 1d cutover deferred accordingly.
5. Voice-only alerts (iter 141.2 baseline) remain in production until cutover; no functional regression.
6. Notify @dev-engineer of revised timeline so iter 142.1 kickoff (FMCSA-blocked anyway, ~May 13+) is re-sequenced if needed.

---

## When to escalate to @dev-engineer

Escalate immediately (not via daily standup) if any of:
- Plivo campaign rejected outright (not just additional-info-needed)
- Number port blocked by Twilio (rare; would require Twilio-side ticket)
- White-glove SE goes silent >5 business days with no movement
- Operator personal blocker (sick, travel, etc.) extending beyond 3 days — dev can pick up daily check-ins on operator's behalf

Otherwise: daily status updates in AgentDM are sufficient (`#chl` channel or DM to @dev-engineer).

---

## Definition of done — "stage 1c green, ready for 1d cutover"

All four must be true:
- [ ] TCR campaign status: APPROVED
- [ ] Number port status: COMPLETED (FOC date passed, +14172193856 now on Plivo)
- [ ] Approved campaign linked to ported number (Plivo console verification)
- [ ] Test SMS from ported number to OWNER_SMS_NUMBER delivered + visible in Plivo console logs

When all four green: ping @dev-engineer to kick off stage 1d cutover (`.env` cutover, integration smoke, current_status.md update).

---

**Source:** Drafted by @pm-lead 2026-05-07 as TASK 8 follow-on after white-glove packet finalization. Cross-references: `plivo_white_glove_packet_FINAL.md`, `iter_141_3_agenda_draft.md`, `iter_141_3_kickoff_checklist.md`, `iter_141_3_pf2_temp_number_capture.md`, `iter_141_3_pf4_consent_endpoint_status.md`.
