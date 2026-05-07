# Iter 141.3 Operator Pre-Flight Checklist (Plivo Migration)

> Compressed from `iter_141_3_agenda_draft.md` (Pre-Flight + Stage 1a operator actions). Complete all 10 items **before** handing off to @dev-engineer for `.env.plivo_migration_prep` build + smoke battery.

**Wall-clock to "ready for dev Stage 1a smoke battery": ~30 min hands-on + up to 24h async** (Plivo solutions-engineer SLA on the white-glove kickoff at step 8). Hands-on items are sequential; the async wait runs in parallel with operator sleep.

1. **Confirm iter 141.2 shipped** (~2 min) — DAT live wiring + auto-bid + voice-only alerts verified green in `current_status.md`. Hard gate; do not proceed otherwise.
2. **Audit external paperwork** (~5 min, OR skip — see fallback) — *Pre-question to ask yourself first:* "Are my existing broker-carrier agreements in one searchable place (single drive, indexed PDFs, etc.)?" If **yes**, search for "+14172193856". Cited = port path (Stage 1c, 7–10 day async wait). Not cited = new-number path. If **no** (paper-only / scattered across email/Dropbox/etc.), **default to NEW NUMBER** — instant Plivo provision, skip the audit entirely. Saves the 5-min-balloons-to-an-hour failure mode.
3. **Plivo signup** (~5 min) — https://console.plivo.com/accounts/register/ . Free $10 credit, no setup fees. Use `dispatch@continentalhaul.com`.
4. **Vault placeholder** (~1 min) — Create entry "Plivo — Auth Credentials" (empty; populate in step 9).
5. **Console login + navigate to A2P 10DLC** (~1 min) — Settings → A2P 10DLC.
6. **Create Profile** (~5 min) — Private LLC, "Continental Haul Logistics LLC", MC-1817555, USDOT 4569843, EIN, registered LLC address, authorized contact = Jason Aaron Meyer.
7. **Register Brand** (~3 min) — Standard brand ($4 one-time, charged to Plivo). Skip optional vetting (volume <6k/day at v1). Submit.
8. **Kickoff white-glove support** (~3 min hands-on, **then up to 24h async wait**) — Console chat: "Migrating from Twilio, need white-glove 10DLC campaign assistance for broker operational SMS. MC-1817555." Solutions engineer responds within 24h. Operator can proceed to steps 9–10 immediately; the async wait gates Stage 1c (campaign submission), not Stage 1a smoke battery.
9. **Populate vault** (~2 min) — Account → API Credentials → copy Auth ID (starts with "MA...") + Auth Token into the vault entry from step 4.
10. **Provision temp number** (~3 min) — Phone Numbers → Buy Numbers → US local, SMS-enabled, ~$0.80/mo. Note number; @dev-engineer will paste into `.env.plivo_migration_prep` during Stage 1a follow-up.

**Handoff trigger:** All 10 boxes checked → ping @dev-engineer with vault entry confirmation + temp number → dev runs Stage 1a smoke battery (6 tests).

**Source:** Drafted by @pm-lead 2026-05-07 from `iter_141_3_agenda_draft.md`.
