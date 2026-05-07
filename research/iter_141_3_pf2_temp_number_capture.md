# Iter 141.3 PF2 — Plivo Temp Number Capture Procedure

> **Pre-flight check #2** for the white-glove packet (`plivo_white_glove_packet_FINAL.md` §6 row 3). Operator must capture the Plivo-provisioned temporary phone number during stage 1a step 10 and paste it into the packet **before** sending the packet to the white-glove team.
> **Why this matters:** The temp number is what stage 1b smoke tests send from. White-glove team needs it cited in §6 so the campaign can be linked to it during the smoke window (before the ported `+1 (417) 219-3856` arrives in stage 1c). Forgetting to fill `<temp>` won't reject the packet but will require a follow-up message and slows the white-glove cycle.

---

## When this happens

Iter 141.3 stage 1a, step 10 (per `iter_141_3_kickoff_checklist.md`):
> **Provision temp number** (~3 min) — Phone Numbers → Buy Numbers → US local, SMS-enabled, ~$0.80/mo. Note number; @dev-engineer will paste into `.env.plivo_migration_prep` during Stage 1a follow-up.

The kickoff checklist focused on the dev `.env` paste. PF2 covers the parallel paste into the white-glove packet.

---

## Procedure (operator, ~1 min after step 10)

1. After completing checklist step 10 (number provisioned in Plivo console), copy the new number in **E.164 format** (e.g., `+14175551234`).
2. Open `chl-memory/research/plivo_white_glove_packet_FINAL.md` (or your local working copy if you've redacted SIDs further).
3. In §6 "Phone Numbers to Link", find the row containing `` `<temp>` `` in column 1 and replace `<temp>` with the E.164 number from step 1.
   - Before: `| `<temp>` | Plivo provisioning | Temporary number assigned during stage 1a for stage 1b smoke testing. **Release after cutover.** |`
   - After: `| **+14175551234** | Plivo provisioning | Temporary number assigned during stage 1a for stage 1b smoke testing. **Release after cutover.** |`
4. Tick the bottom-of-doc checklist row: `- [x] §6 — Plivo temporary number (after Brand registration)`.

---

## Verification before paste

- Number is in E.164 (leading `+`, country code, no separators).
- Number matches the one operator captured in the vault entry from checklist step 9 (Auth ID + Token + new number row).
- Number is the Plivo number, NOT one of the Twilio numbers (`+14172193856` or `+18664906433`) — those go in the existing §6 rows above.

---

## What happens if operator forgets

White-glove team will reply asking for the temp number to link to the smoke campaign. Adds ~24h to the white-glove SLA cycle. Not a rejection, but a stall. PF2 exists specifically to prevent this stall.

---

**Source:** Drafted by @pm-lead 2026-05-07 as PF2 follow-up note from `plivo_white_glove_review.md`. Cross-references: `plivo_white_glove_packet_FINAL.md` §6, `iter_141_3_kickoff_checklist.md` step 10.
