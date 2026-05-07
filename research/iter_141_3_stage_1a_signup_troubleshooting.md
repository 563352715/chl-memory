# Iter 141.3 Stage 1a — Plivo Signup Troubleshooting Playbook

> **Captured live during stage 1a, 2026-05-07.** Operator hit two distinct validator gates during Plivo console signup. This doc preserves the workarounds, diagnostic patterns, and escalation paths for the current iter and any future Plivo-adjacent onboarding (or any SaaS with similar email-validator behavior).
> **Audience:** operator (today) + future @pm-lead / @dev-engineer (next time anyone touches this signup flow).

---

## 1. Plivo email-validator behavior (observed)

Two rejection events during today's signup:

**Rejection A — `dispatch@continentalhaul.com` (role-based filter):**
- Plivo's signup form rejected this address with a generic "use a personal email" message.
- Cause: standard role-based-email frontend filter (`dispatch@`, `info@`, `admin@`, `noreply@`, `support@`, `sales@`, etc. all blocked at the acquisition layer).
- **Important:** This is a Plivo signup-form filter, NOT a TCR-level requirement. The packet's §1 authorized-contact field can still use `dispatch@` (TCR's role-based policy is more lenient — see white-glove packet §1).

**Rejection B — `jason.meyer@continentalhaul.com` (initial submit; resolved via Zoho alias creation + re-submit):**
- After Rejection A, operator tried `jason.meyer@continentalhaul.com` — also rejected on first submit.
- Working theory: rejection was caused by the alias not yet existing on the Zoho mailbox (Plivo's MX/deliverability check failed) rather than the address pattern itself. After creating the alias in Zoho admin (see §2) and re-submitting, the address was accepted.
- Less likely but worth ruling out: domain-age penalty (continentalhaul.com newly registered) or vendor-reputation flag (DMARC/SPF/DKIM gaps). If still rejected after alias is confirmed live, those become the next diagnostic fork.

---

## 2. Zoho alias workaround procedure

Use this when operator needs a non-role personal-format address on the existing custom domain without provisioning a separate user mailbox.

1. **Log into Zoho Mail Admin Console:** https://mailadmin.zoho.com/cpanel/index.do (operator's Zoho Workspace admin login required).
2. **Navigate:** Users → select the target user (operator's primary user account, owner of the mailbox).
3. **Open Mailbox Settings:** click the user → "Mailbox Settings" tab.
4. **Find "Email Alias":** scroll to the Email Alias section; click "Add" (or "Add Alias").
5. **Try first-name only:** e.g., `jason` → rejected as reserved/conflicted (Zoho reserves common first names + admin-overlap names at the system level).
6. **Try first.last (lowercase):** e.g., `jason.meyer` → accepted. The full alias becomes `jason.meyer@continentalhaul.com`.
7. **Confirm propagation:** wait ~30–60 seconds for MX propagation. Send a test email from a personal account (Gmail, etc.) to the new alias and verify it lands in the primary mailbox's inbox.
8. **Return to Plivo signup** with the new alias as the email; submit. Should now pass the validator.

**Key constraints learned:**
- Lowercase only (Zoho rejects mixed-case alias entries).
- Reserved names blocked: `admin`, `root`, `webmaster`, `postmaster`, common first names alone (`jason`, `john`, etc.), generic role names (`info`, `support`, `sales`).
- First.last format is the most reliable (also acceptable: first-last, firstinitial-last).
- One alias addition does NOT count as a new user license under Zoho Workspace — free under existing user.

---

## 3. Diagnosing stale-display vs real validator rejection

If the validator rejects an address you believe should pass, run this 3-step diagnostic before assuming the validator is correct:

1. **Refresh + re-submit** — close the browser tab, open a fresh tab, navigate back to signup form, re-enter the email, submit. Some validators cache rejections per-session; a fresh tab resets the cache.
2. **Incognito-window test** — open the signup URL in an incognito/private window. Bypasses session cookies + cached form state. If incognito accepts where regular browser rejected, the rejection was UI-state, not real.
3. **MX-record sanity check** — `dig MX continentalhaul.com` (or use https://mxtoolbox.com); confirms the domain's mail records are actually live + correctly pointing to Zoho. If MX is broken, no email validator anywhere will accept addresses on that domain.

**Decision tree:**
- Refresh → accepted: stale-display rejection. Proceed.
- Incognito → accepted: session/cookie issue. Proceed in incognito or clear cache + retry.
- All three reject + MX is healthy: real validator gate. Escalate per §4.

---

## 4. Escalation path — domain-level block

If diagnostic in §3 confirms the rejection is real and not session-state, the cause is likely domain-age, deliverability reputation, or a Plivo-specific blocklist.

**Contact:** sales@plivo.com (NOT the regular support queue — sales handles signup-blocker resolution).

**Subject line:** "Signup blocked — verifying business credentials for MC-1817555 / Continental Haul Logistics"

**Body content (provide all):**
- Business legal name: Continental Haul Logistics LLC
- MC Number: 1817555
- USDOT Number: 4569843
- Authorized contact: Jason Aaron Meyer, jason.meyer@continentalhaul.com
- Domain: continentalhaul.com
- Plivo signup attempt date/time: (today, with timezone)
- Specific rejection message Plivo's validator returned: (paste exact text)
- Use case summary: "10DLC operational SMS for freight brokerage carrier dispatch"

**Expected turnaround:** ~1 business day. Plivo sales team manually allowlists the address/domain after verifying business legitimacy via FMCSA SAFER lookup.

---

## 5. Alternative-email fallback options

If the Plivo signup-form gate is blocking critical-path progress and §4 escalation is too slow, these alternatives unblock today:

- **Google Workspace alias:** if operator has a Google Workspace tenant on continentalhaul.com, add `jason.meyer@` alias via admin.google.com → Users → user → Add alternate email. Same propagation behavior as Zoho.
- **Microsoft 365 alias:** Microsoft 365 admin center → Users → Active users → user → Manage username and email → Add alias.
- **Personal Gmail signup, then custom-domain swap:** sign up at Plivo with `jason.aaron.meyer@gmail.com` (or operator's existing personal Gmail), complete onboarding + Brand registration, THEN add `jason.meyer@continentalhaul.com` as a secondary contact email in Plivo console after activation. Less clean but unblocks if domain is fully blocked.
- **Last resort — fresh business email at a different domain:** operator owns `continentalhaul.com`; if domain is permanently flagged, register a backup domain (e.g., `chl-dispatch.com`, ~$12/yr) for vendor-signup-only use.

---

## 6. Password handling

Plivo signup creates a console password on the account. Treat as production credential.

**Storage location (recommended, OUTSIDE the git repo):**
- Primary: `C:\Users\meyer\Documents\CHL_secrets\plivo_console.txt` (or operator's preferred password manager — 1Password / Bitwarden / etc.)
- Backup: `D:\CHL_backups\rolling\secrets\plivo_console.txt`

**Rotation cadence:** rotate every 90 days, immediately after any suspected exposure, or whenever a team member with access leaves the operator's circle.

**Never commit:**
- The git repo `chl-memory/.gitignore` already covers common credential filename patterns (`*credentials*`, `*secrets*`, `*.env*`, `*plivo*` if added). If adding any new credential file, double-check `.gitignore` covers it BEFORE first save.
- If accidentally committed: rotate the credential immediately + use `git filter-branch` or BFG Repo-Cleaner to scrub history + force-push (with operator authorization — destructive operation).

**Vault entry naming (operator):** "Plivo — Console Login" (login URL + username + password) and "Plivo — Auth Credentials" (Auth ID + Auth Token, populated at stage 1a step 9 per kickoff checklist).

---

## 7. Cross-references

- White-glove packet (paste target): `chl-memory/research/plivo_white_glove_packet_FINAL.md`
- Iter 141.3 agenda (full 4-stage spec): `chl-memory/research/iter_141_3_agenda_draft.md`
- Operator pre-flight checklist (10 items, ~30 min hands-on): `chl-memory/research/iter_141_3_kickoff_checklist.md`
- Stage 1b prep brief (white-glove wait playbook, 7–21 day window): `chl-memory/research/iter_141_3_stage_1b_prep_brief.md`
- PF2 (temp number capture procedure): `chl-memory/research/iter_141_3_pf2_temp_number_capture.md`
- PF4 (consent endpoint status): `chl-memory/research/iter_141_3_pf4_consent_endpoint_status.md`
- Plivo migration research (process detail + cost analysis): `chl-memory/research/plivo_migration_research.md`

---

## 8. Future-iter applicability

This troubleshooting doc is reusable beyond iter 141.3. Specifically:
- **Future SaaS signups with role-based-email filters:** Stripe, Linear, GitHub Enterprise, AWS Activate, Twilio (re-onboarding), DocuSign, etc. all use similar acquisition-layer email validators. The §3 diagnostic + §5 fallback patterns apply.
- **Onboarding additional businesses onto Plivo:** if CHL ever expands to multi-broker SaaS scale (Phase 9+), each new broker tenant likely hits the same `dispatch@` rejection. Reuse §2 alias procedure as the standard onboarding step.
- **Domain reputation rebuilds:** if continentalhaul.com hits a deliverability degradation event (spam-flag, DKIM rotation, etc.), the §4 escalation pattern (FMCSA SAFER as legitimacy proof) applies to any vendor that requires manual allowlist.

---

**Source:** Drafted by @pm-lead 2026-05-07 during iter 141.3 stage 1a in-flight, capturing live operator learnings while fresh. Cross-references: see §7.
