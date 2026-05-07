# Plivo White-Glove Submission Packet — Completeness Review

> **Reviewed:** `chl-memory/research/emergent_plivo_white_glove_packet_task.md` (the Emergent task spec for generating `plivo_white_glove_packet.md`).
> **Cross-referenced:** `chl-memory/research/plivo_migration_research.md` for Plivo's white-glove process + TCR submission requirements.
> **Reviewer:** @pm-lead, 2026-05-07.
> **Conclusion:** **6 critical gaps, 10 recommended additions, 4 optional polish items.** Spec is solid foundationally — covers business identity, opt-in flow framing, sample messages, volume tiers — but has gaps in the exact areas that historically trigger TCR rejection (CTA verification, suppression-list mechanics, HELP-keyword exact text). Closing the 6 critical items materially improves first-pass acceptance odds with Plivo's white-glove team.

---

## Methodology

Compared the spec section-by-section against:
1. Plivo white-glove process steps (Profile → Brand → Campaign → Numbers per `plivo_migration_research.md`)
2. TCR (The Campaign Registry) standard submission elements
3. Twilio rejection error 30909 (CTA verification failed) — the failure mode being explicitly mitigated
4. CHL's actual data model (`db.carriers.sms_opt_in`, planned `/sms-opt-in` page, planned `db.consent_records` if needed)

Reviewed for: completeness (is each TCR-required element addressed?), specificity (will the white-glove team have to ask follow-up questions?), and risk-mapping (does the spec pre-empt the failure mode that killed Twilio?).

---

## CRITICAL gaps (likely rejection-blockers if unaddressed)

### C1. CTA URL live-state at submission time
**Where:** Section 3 (Recipients & Opt-In Flow), public CTA URL line.
**Gap:** Spec acknowledges `continentalhaul.com/sms-opt-in` is "page to be built" but doesn't address that **TCR/Plivo white-glove typically requires the URL be functional at submission time**. Twilio's 30909 rejection on CHL was specifically a CTA verification failure. White-glove team will check the URL.
**Recommended addition:** Explicit status line — one of:
- (a) "Page LIVE at submission time" (tracked dependency: `emergent_sms_opt_in_page_task.md` ships before Stage 1c kickoff), OR
- (b) "Paper-agreement-only consent at v1; public CTA deferred to post-launch" (frame as primary opt-in, list URL as future).
Operator must pick (a) or (b) before submission. Mixed framing risks the same 30909 outcome.

### C2. Suppression list mechanics + data location
**Where:** Section 3 (Opt-Out) + Section 7 (Compliance).
**Gap:** Spec says "STOP → removed within 24 hours" but doesn't tell white-glove team **where the suppression state lives** or how it's enforced. TCR submissions ask this directly. Per iter 142.1 stage 1d, opt-out flips `db.carriers.sms_opt_in: bool` and the outreach module reads it pre-send.
**Recommended addition:** One sentence: "Opt-out enforced via `db.carriers.sms_opt_in: false` flag; outreach module checks pre-send; manual audit query available for audit response within 24h."

### C3. HELP autoresponder exact text
**Where:** Section 7 (Compliance — marked as Emergent-fill-in).
**Gap:** "Emergent will look up Plivo's standard language" is not enough. TCR rejects for missing/non-standard HELP responses regardless of who fills it in. Standard pattern is required AND specific to CHL identity.
**Recommended addition:** Pre-draft the exact text in the spec rather than deferring:
- HELP: "CHL (MC-1817555) operational SMS for carriers/shippers under signed agreements. Reply STOP to opt out, START to opt in. Support: dispatch@continentalhaul.com or call (XXX) XXX-XXXX."
- STOP confirm: "You have opted out of CHL operational SMS. No further messages will be sent. Reply START to re-enable. MC-1817555."
- START confirm: "You have re-enabled CHL operational SMS. Reply STOP at any time to opt out. MC-1817555."
Operator fills in support phone; everything else fixed.

### C4. Marketing-vs-operational explicit declaration
**Where:** Section 2 (Use Case) + Section 4 (Sample Messages).
**Gap:** Spec implies operational throughout but never says the magic line that TCR vetting flags on. Twilio's 30909 path sometimes triggered when sample messages had any tone interpretable as promotional.
**Recommended addition:** One sentence at top of Section 2: "**No marketing or promotional content. All messages are operational notifications tied to specific load events with carriers/shippers under signed agreements.**" Plus a parallel one-liner at top of Section 4 sample messages.

### C5. Reuse existing TCR brand registration (B6384Q7)
**Where:** Section 8 (Migration-Specific Notes).
**Gap:** Spec mentions brand SID and TCR brand ID but doesn't explicitly **request** that Plivo white-glove team reuse the existing TCR-approved brand rather than re-vet. Re-vetting adds 1-2 weeks + $4 + risk.
**Recommended addition:** Explicit ask line at top of Section 8: "**Request: reuse existing TCR-approved brand registration (TCR ID B6384Q7) — only campaign needs new submission, not the brand.**"

### C6. Operator-fillable fields completeness audit
**Where:** Section 1 (Brand Information).
**Gap:** Spec marks EIN, primary contact phone, business address as operator-fillable. Implicit assumption that operator has these ready. Realistic risk: operator's EIN may be in a different system from MC#/USDOT, business address may be PO box vs registered address. White-glove team rejects on address mismatch with FMCSA registry.
**Recommended addition:** Pre-flight checklist for operator: "Before pasting packet to white-glove chat, verify each operator-fillable field matches CHL's FMCSA registry exactly: EIN (from IRS letter), business address (must match SAFER record for MC-1817555), authorized contact phone (can differ from sender numbers, but must be reachable)." Catch mismatches before submission, not during.

---

## RECOMMENDED additions (improve acceptance odds)

### R1. Per-recipient message frequency
Section 5 covers total monthly volume but TCR asks `msgs/recipient/month`. Add: "Expected per-recipient frequency: 5-20 msgs/month (event-driven; carriers receive on assigned loads only)."

### R2. TCPA quiet-hours compliance posture
Section 7 should declare: "Messages restricted to TCPA-compliant hours (8am-9pm recipient local time) for non-time-critical content; load opportunities and dispatch updates exempt as carrier-business-relationship operational." White-glove team will ask.

### R3. TFV (Toll-Free Verification) is separate from 10DLC
Section 6 mentions toll-free `+18664906433` "ported separately if cited" but doesn't clarify TFV is its own submission path, NOT part of the 10DLC white-glove packet. Add a one-liner: "Note: Toll-free TFV submission is a separate path from this 10DLC packet; operator will handle TFV via Plivo's TFV-specific submission process if/when toll-free port completes."

### R4. Number recycling policy
TCR cares about preventing messages to recycled numbers (e.g., a former carrier's phone reassigned to an unrelated consumer). Add to Section 7: "Carriers inactive >12 months get phone-number-validity check via FMCSA SAFER re-pull before next outreach; suppressed if changed."

### R5. Record retention period
Section 7 marks data retention as Emergent-fill-in. Specify: "Consent records (signed agreements + opt-in/out timestamps) retained ≥4 years per FCRA + TCR best practice."

### R6. Double opt-in declaration for digital CTA
Section 3 doesn't say whether the planned `/sms-opt-in` page implements double opt-in (web form → confirm SMS → reply YES). Specify: "Digital CTA flow at /sms-opt-in implements double opt-in: web submission → confirmation SMS sent → recipient replies YES to confirm enrollment."

### R7. Twilio cancellation/rollback timing
Section 8 should add: "Twilio account remains active for 30 days post-FOC-date (port completion) to enable reverse-port if Plivo migration encounters issues. Cancellation triggered only after 30-day stable Plivo operation confirmed."

### R8. Plivo's template doc URL for Emergent
Section 7 says "look up Plivo docs" — give the exact URL: `https://www.plivo.com/docs/sms/concepts/use-cases/operational-notifications/` (already noted in spec line 99 but only inline; promote to Section 7 header so Emergent uses it explicitly).

### R9. Length target unrealistic
Spec says "600-800 words". With critical + recommended gaps closed, realistic target is **1100-1300 words**. Update target band; or split into a "core packet (700 words)" + "appendix with operator-fillable details" if Plivo chat has length limits.

### R10. Sample message #5 (payment notification) — ACH timing claim
Sample 5 says "ACH should post within 24-48 hrs" — this is a vendor-dependent claim. If factoring company is slower, recipient sees broken promise. Soften to "ACH typically posts within 1-3 business days (timing per your factoring company)."

---

## OPTIONAL polish items

### O1. DUNS number field
Some TCR submissions ask for DUNS. CHL likely doesn't have one at v1. Add to Section 1: "DUNS: not registered (small-business exemption at v1; will register if required for shipper integrations Phase 5+)."

### O2. Cross-state consent variation
Spec doesn't address CA/IL/NY stricter state-level consent. For B2B operational, federal TCPA + signed agreements typically sufficient. Add a one-liner to Section 7 confirming: "All recipients are commercial entities (carriers, shippers) under signed B2B agreements; consumer-protection statutes (CA/IL state-level CPRA, etc.) inapplicable."

### O3. Alphanumeric sender ID
Plivo supports alphanumeric sender IDs in some markets; US 10DLC disallows. Add to Section 6 one-liner: "Alphanumeric sender ID: not applicable (US 10DLC requires numeric sender)."

### O4. Acceptance criteria in spec
Spec's "Acceptance Criteria" lists 5 items. Add a 6th: "Spec passes pre-submission review against `plivo_white_glove_review.md` critical-gap checklist before operator pastes to white-glove chat."

---

## Recommended next steps

1. **Operator decisions** (block submission until resolved):
   - C1: pick path (a) live CTA at submission OR (b) paper-only at v1 with deferred CTA
   - C6: pre-flight verify EIN + business address + authorized contact phone match FMCSA registry
2. **Spec author updates** (revise `emergent_plivo_white_glove_packet_task.md`):
   - Close C2 (suppression mechanics), C3 (HELP exact text), C4 (operational declaration), C5 (TCR brand reuse) directly in spec rather than deferring to Emergent.
   - Update length target to 1100-1300 words (R9).
3. **Emergent execution** (after spec updated):
   - Generate `plivo_white_glove_packet.md` per revised spec.
   - Operator runs final pre-submission audit against this review's critical-gap list.
4. **Re-review** this doc against the generated packet (when it lands) to validate all 6 critical gaps closed before operator pastes to Plivo chat.

---

**Source:** Drafted by @pm-lead 2026-05-07. Reviews `emergent_plivo_white_glove_packet_task.md` against TCR/Plivo white-glove submission requirements. No edits to source spec — gaps logged here for spec author to action.

**Cross-reference:**
- `chl-memory/research/emergent_plivo_white_glove_packet_task.md` — source spec under review
- `chl-memory/research/plivo_migration_research.md` — Plivo white-glove process detail
- `chl-memory/research/iter_141_3_agenda_draft.md` — iter context (stage 1a operator action)
- `chl-memory/research/iter_141_3_kickoff_checklist.md` — operator pre-flight (white-glove kickoff at item 8)
- `chl-memory/research/emergent_sms_opt_in_page_task.md` — CTA URL build (gates C1 path-a)
