# Iter 141.3 PF4 — `/api/admin/sms-consent` Endpoint Status

> **STATUS: Path A APPLIED 2026-05-07.** The packet `plivo_white_glove_packet_FINAL.md` no longer references the `/api/admin/sms-consent` endpoint or the JSON sample row. §3 Path C now uses text-only audit-log description; §8 request #3 changed "temporary read-only API token" to "secure file share". @dev-engineer authorization 2026-05-07. This note retained as historical context + Path B reference if the audit-ledger build is later re-prioritized.
>
> **Original framing:** Pre-flight check #4 for the white-glove packet. The packet's §3 Path C originally referenced the endpoint with a sample audit-log JSON row, but the endpoint does not exist in CHL's current backend.
> **Why this mattered:** If Plivo (or TCR via Plivo) requested live verification of the audit ledger, the referenced endpoint had to respond. Promise-vs-actual mismatch could have triggered a 30909-class rejection — exactly the failure mode iter 141.3 is migrating away from.

---

## Current state (as of 2026-05-07)

- `/api/admin/sms-consent` endpoint: **NOT BUILT.**
- Consent ledger collection (`db.consent_records` or similar): **NOT BUILT.**
- iter 141.3 scope: SMS provider migration only (Twilio → Plivo); does NOT include consent-ledger build.
- iter 142.1 scope: carrier network (outreach + assignment + dispatch); `db.carriers.sms_opt_in: bool` flag exists per stage 1d but no full audit-log endpoint.
- Closest existing surface: `db.carriers.sms_opt_in` boolean flag (binary state per recipient, no history). Sufficient for application-layer suppression enforcement, NOT sufficient for TCR audit-ledger verification with timestamps/source-trail.

---

## Two acceptable paths (operator picks ONE before paste)

### Path A — Strip Path C audit-ledger detail from packet

**What:** Remove the JSON sample block from §3 Path C (or replace with high-level prose). Keep the keyword-opt-in flow narrative; just don't promise an audit endpoint that doesn't exist.

**Edit (in `plivo_white_glove_packet_FINAL.md` §3 Path C):**
- Remove or comment out the JSON code block (lines starting with `{ "phone": ... }`).
- Replace with: *"Keyword interactions are logged in CHL's internal consent records. Audit access available on request via secure file share."*

**Pros:** Honest representation of current capabilities. Zero engineering work. Defers the audit-ledger build to when it's actually needed.

**Cons:** Slightly weaker TCR posture (less specific compliance evidence in the packet). Likely still acceptable for low-volume Mixed campaign vetting.

**Recommended for:** v1 pre-revenue; defer building infrastructure that may not be required.

### Path B — Build a minimal stub endpoint before submission

**What:** Implement a read-only stub at `/api/admin/sms-consent` that returns the same JSON structure shown in the packet, populated from `db.carriers` + a new lightweight `db.consent_log` collection populated by the iter 142.1 stage 1d outreach module.

**Estimate:** ~4–6h dev (route + auth check + Mongo aggregation + integration with existing keyword handlers if present, or stub data if not).

**Pros:** Packet content matches reality. TCR audit verification capability if requested. Foundation for future compliance features.

**Cons:** Out-of-scope for iter 141.3. Adds dev hours that competing iters need. Pre-revenue solo cost-of-time concern.

**Recommended for:** Only if Plivo white-glove team explicitly asks for live audit access during their first review pass.

---

## Recommended sequence

1. **Default to Path A.** Strip the JSON sample from packet §3 Path C before paste. Lowest-friction, honest representation.
2. **Watch white-glove team response.** If they ask for live audit verification, escalate to Path B (operator decides whether to authorize ~4–6h dev to build the stub OR push back on the audit requirement at their volume tier).
3. **Re-paste a revised packet** with stub endpoint URL if Path B activates.

---

## What happens if operator does neither

If packet is pasted as-is (with JSON sample intact) and Plivo/TCR requests live verification:
- Best case: white-glove team asks if endpoint is live; operator responds "audit access on request via secure file share" and Plivo accepts the soft answer.
- Worst case: TCR rejects campaign citing CTA-evidence verification failure (the same failure class as Twilio's 30909).
- Probability: low for low-volume Mixed campaign; higher if vetting agent is rigorous.

---

**Source:** Drafted by @pm-lead 2026-05-07 as PF4 follow-up note from `plivo_white_glove_review.md`. Cross-references: `plivo_white_glove_packet_FINAL.md` §3 Path C, `agenda/iter_142_1_full.md` stage 1d (where `db.carriers.sms_opt_in` flag is built).
