# Security incident — admin credentials committed to git history

**Date discovered:** 2026-05-08 EOD-14 (autonomous-continue mode)
**Discovered by:** dev-engineer Claude during Priority-1 living-TODO item "Audit git history for accidentally-committed secrets"
**Severity:** HIGH (live admin creds in private-repo git history); LOW after rotation
**Status:** AWAITING OPERATOR — rotation pending; history-scrub decision pending

## What was found

`git log -p --all` on the **chl-memory** repo surfaces the operator's admin password and PIN in plaintext in multiple commits dated 2026-05-03 (iter 135.3 handoff docs / changelog rows documenting the login-loop fix). Specific strings:

- `ADMIN_PASSWORD=Thomas1342#`
- `PIN 352715`

Context of leak: prior agent (dev iter 135.3) discovered `seed_admin()` was overwriting DB password with the env var on every boot. The fix involved setting the env var. The fix-summary documentation included the password VERBATIM in the commit message / changelog, propagating into chl-memory git history.

## What was NOT found

- No real Stripe / Anthropic / OpenAI / AWS API keys in plaintext.
- No Twilio / Plivo / Mercury credentials in plaintext.
- No Zoho IMAP password in history (operator provisioned this via `set_env_var.ps1` in EOD-2 EOD-6 — never wrote it to a tracked file).
- One synthetic test string `sk_test_encryption_verify_123` (clearly a test fixture; safe).

## Risk assessment

**Pre-rotation (current state):**
- Anyone with read access to GitHub `chl-memory` repo can see the admin password + PIN.
- Repo is private; only operator's GitHub account has access.
- Risk = a compromise of the operator's GitHub account, OR the repo accidentally going public, OR a future enterprise audit flagging this.

**Post-rotation:**
- Old creds in history become inert (no longer match production).
- Risk = effectively zero.

## Required actions (operator-handed)

1. **Rotate `ADMIN_PASSWORD`** via:
   ```
   .\scripts\set_env_var.ps1 -Key ADMIN_PASSWORD -Value "<NEW>" -Restart
   ```
2. **Rotate operator PIN** via the platform's Change Password UI in Business Settings.
3. **Decide:** leave history (Option A — recommended; rotation makes leak inert) OR force-rewrite history with `git filter-repo` (Option B — ~30 min work; force-push; invalidates local clones).

## Future prevention (dev-shipped autonomously)

- Pre-commit hook with `gitleaks` (or equivalent) scanning for secret patterns. Blocks commits containing API-key / password / token shapes.
- Phase-2 CI pipeline (GitHub Actions) runs the same scan on every push as a backstop.
- Update SUB_AGENT_BOILERPLATE.md to forbid pasting credentials into commit messages or doc files; require `<REDACTED>` placeholder + a separate operator-handed env-var step.

## Why this incident exists

Iter 135.3 (~2026-05-03; pre-current-session) logged the password in good-faith documentation of the fix. No malicious intent; just absent secret-hygiene discipline at the time. The autonomous-continue mode + the security-stability living-TODO are exactly what's designed to catch this kind of historical leak.

The capture-every-feature persistence rule applies: this incident is now durably documented + the rotation + history-scrub decision will be ticked off in the EOD-13 living-TODO checklist when complete.
