# Iter 141.3 Operator Pre-Flight Checklist (Plivo Migration)

> Compressed from `iter_141_3_agenda_draft.md` (Pre-Flight + Stage 1a operator actions). Complete all 10 items **before** handing off to @dev-engineer for `.env.plivo_migration_prep` build + smoke battery.

**Total prep time: ~30 min hands-on**

1. **Confirm iter 141.2 shipped** (~2 min) — DAT live wiring + auto-bid + voice-only alerts verified green in `current_status.md`. Hard gate; do not proceed otherwise.
2. **Audit external paperwork** (~5 min) — Search signed broker-carrier agreements for "+14172193856". Yes = port path (Stage 1c, 7–10 day async wait). No = instant new-number path.
3. **Plivo signup** (~5 min) — https://console.plivo.com/accounts/register/ . Free $10 credit, no setup fees. Use `dispatch@continentalhaul.com`.
4. **Vault placeholder** (~1 min) — Create entry "Plivo — Auth Credentials" (empty; populate in step 9).
5. **Console login + navigate to A2P 10DLC** (~1 min) — Settings → A2P 10DLC.
6. **Create Profile** (~5 min) — Private LLC, "Continental Haul Logistics LLC", MC-1817555, USDOT 4569843, EIN, registered LLC address, authorized contact = Jason Aaron Meyer.
7. **Register Brand** (~3 min) — Standard brand ($4 one-time, charged to Plivo). Skip optional vetting (volume <6k/day at v1). Submit.
8. **Kickoff white-glove support** (~3 min) — Console chat: "Migrating from Twilio, need white-glove 10DLC campaign assistance for broker operational SMS. MC-1817555." Solutions engineer responds within 24h.
9. **Populate vault** (~2 min) — Account → API Credentials → copy Auth ID (starts with "MA...") + Auth Token into the vault entry from step 4.
10. **Provision temp number** (~3 min) — Phone Numbers → Buy Numbers → US local, SMS-enabled, ~$0.80/mo. Note number; @dev-engineer will paste into `.env.plivo_migration_prep` during Stage 1a follow-up.

**Handoff trigger:** All 10 boxes checked → ping @dev-engineer with vault entry confirmation + temp number → dev runs Stage 1a smoke battery (6 tests).

**Source:** Drafted by @pm-lead 2026-05-07 from `iter_141_3_agenda_draft.md`.
