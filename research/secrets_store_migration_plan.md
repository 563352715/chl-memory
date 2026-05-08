# Secrets Store Migration Plan — Iter security/stability Priority-1 #1.2

**Author:** dev (sub-agent DD3)
**Date:** 2026-05-08
**Stream:** DPAPI-encrypted secrets store foundation
**Status:** Module + CLI + tests shipped; migration of live secrets is a follow-up stream (NOT executed in this batch).

---

## What just shipped

- `backend/security/secrets_store.py` — DPAPI-first / Fernet-fallback at-rest encryption.
- `backend/security/secrets_cli.py` — operator CLI (`set`, `get`, `delete`, `list`, `migrate-from-env`, `health`).
- `backend/tests/test_secrets_store.py` — 7 mandated tests + 2 bonus (all passing on `.venv-local`).
- `.gitignore` updated to cover `data/secrets_*` and `data/secrets_store.json{,.tmp}`.

The module is **dormant** — no production code reads from it yet. This doc lays out the wiring plan for subsequent streams.

---

## Why DPAPI first

Windows DPAPI (CryptProtectData / CryptUnprotectData, per-user scope) ties decryption to the Windows user account. An attacker who copies `secrets_store.json` off the C-drive cannot decrypt it without also exfiltrating the user's logon credentials. Fernet fallback exists only for non-Windows test runs (CI / Linux dev container) — production is Windows.

DPAPI is invoked through `ctypes` against `crypt32.dll`; no `pywin32` dependency added.

---

## Migration order — priority-ranked

Migrate highest-blast-radius secrets first. After each migration, restart the service and run the relevant smoke before moving on.

### Tier 1 — Auth / crypto roots (migrate first)

| Env var | Why first | CLI invocation |
|---|---|---|
| `JWT_SECRET` | Forging this lets an attacker mint admin tokens. Highest blast radius. | `python -m backend.security.secrets_cli migrate-from-env JWT_SECRET` |
| `CHL_TOTP_AT_REST_KEY` | Encrypts every operator's TOTP seed at rest. Compromise = MFA bypass on every user. | `python -m backend.security.secrets_cli migrate-from-env CHL_TOTP_AT_REST_KEY` |
| `SESSION_SECRET` (if present) | Session cookie signing. | `python -m backend.security.secrets_cli migrate-from-env SESSION_SECRET` |

### Tier 2 — Inbox + revenue path

| Env var | Why | CLI invocation |
|---|---|---|
| `ZOHO_IMAP_PASSWORD` | Inbox auto-management depends on this; rotation friction was the original motivator for `set_env_var.ps1`. | `python -m backend.security.secrets_cli migrate-from-env ZOHO_IMAP_PASSWORD` |
| `ZOHO_SMTP_PASSWORD` (if distinct) | Outbound replies. | `python -m backend.security.secrets_cli migrate-from-env ZOHO_SMTP_PASSWORD` |
| `STRIPE_SECRET_KEY` | Revenue path. | `python -m backend.security.secrets_cli migrate-from-env STRIPE_SECRET_KEY` |
| `STRIPE_WEBHOOK_SECRET` | Stripe webhook signature verification. | `python -m backend.security.secrets_cli migrate-from-env STRIPE_WEBHOOK_SECRET` |

### Tier 3 — AI + telephony vendors

| Env var | CLI invocation |
|---|---|
| `OPENAI_API_KEY` | `python -m backend.security.secrets_cli migrate-from-env OPENAI_API_KEY` |
| `ANTHROPIC_API_KEY` | `python -m backend.security.secrets_cli migrate-from-env ANTHROPIC_API_KEY` |
| `PLIVO_AUTH_TOKEN` | `python -m backend.security.secrets_cli migrate-from-env PLIVO_AUTH_TOKEN` |
| `PLIVO_AUTH_ID` | `python -m backend.security.secrets_cli migrate-from-env PLIVO_AUTH_ID` |
| `TWILIO_AUTH_TOKEN` (if still present) | `python -m backend.security.secrets_cli migrate-from-env TWILIO_AUTH_TOKEN` |

### Tier 4 — Carrier discovery + niche integrations

| Env var | CLI invocation |
|---|---|
| `FMCSA_WEBKEY` | `python -m backend.security.secrets_cli migrate-from-env FMCSA_WEBKEY` |
| `DAT_API_TOKEN` (when wired) | `python -m backend.security.secrets_cli migrate-from-env DAT_API_TOKEN` |
| `TRUCKSTOP_API_KEY` (when wired) | `python -m backend.security.secrets_cli migrate-from-env TRUCKSTOP_API_KEY` |
| `MONGO_URL` (only if it embeds a password) | `python -m backend.security.secrets_cli migrate-from-env MONGO_URL` |

---

## Per-secret runbook (paste-ready)

For each secret in priority order, run:

1. Source the live env so the migrate command can read it:
   ```powershell
   Get-Content C:\CHL\backend\.env | ForEach-Object {
     if ($_ -match '^\s*([A-Z0-9_]+)=(.*)$') { Set-Item -Path "env:$($Matches[1])" -Value $Matches[2] }
   }
   ```
2. Migrate:
   ```powershell
   C:\CHL\.venv-local\Scripts\python.exe -m backend.security.secrets_cli migrate-from-env <KEY>
   ```
3. Verify:
   ```powershell
   C:\CHL\.venv-local\Scripts\python.exe -m backend.security.secrets_cli list
   ```
4. Scrub the `.env` line. The cleanest way is to overwrite with a placeholder so the loader still sees the variable name (but the secret is no longer at rest in plaintext):
   ```powershell
   C:\CHL\scripts\set_env_var.ps1 -Key <KEY> -Value "__USE_SECRETS_STORE__"
   ```
5. Service-side: update the consumer module to read from `secrets_store.get_secret(<KEY>)` first, fall through to `os.environ.get(<KEY>)` only if the store returns None or the value is the placeholder. Restart the service. Smoke the affected feature.

---

## Code-side wiring pattern (for the follow-up stream)

Recommended consumer pattern for each module that reads a secret:

```python
from backend.security import secrets_store

def _resolve_secret(name: str, default: str | None = None) -> str | None:
    """Prefer the encrypted store; fall through to env (placeholder-aware)."""
    val = secrets_store.get_secret(name)
    if val is not None:
        return val
    env_val = os.environ.get(name, default)
    if env_val == "__USE_SECRETS_STORE__":
        return None  # store should have it, but doesn't — caller logs + alerts
    return env_val
```

Centralize this in a single helper (`backend/security/resolver.py` — future stream) so every consumer goes through the same path and we get one place to instrument cache-hit / store-miss telemetry.

---

## Out-of-scope for this stream

- Wiring any consumer module to the store. Module is dormant.
- Migrating any actual secret. The CLI exists; operator drives it manually post-deploy.
- Cross-host re-encryption. DPAPI ciphertext is per-user-per-host; if we ever move to a second box, the migration runbook needs a re-encrypt step (decrypt on box A, write to a Fernet-encrypted intermediate, set on box B — to be designed when the need arises).
- HSM / cloud-KMS integration. Way over-budget for solo pre-revenue.

---

## Verification before marking iter done

- [ ] `secrets_store.json` exists at `C:\CHL\data\secrets_store.json` with 600+ permissions (Windows ACL — DPAPI scope already restricts decryption, but file ACL is belt-and-suspenders).
- [ ] `git status` shows no `secrets_store.json` (gitignore working).
- [ ] `python -m backend.security.secrets_cli list` shows the migrated keys.
- [ ] Server restart + smoke for each affected lane (auth, inbox, payments, AI calls).
- [ ] `BUILD_STATUS_REPORT.md` updated with the migration completion.

---

## Standing operator directives (carried per boot protocol)

1. **`set_env_var.ps1` helper** — built at `C:\CHL\scripts\set_env_var.ps1`. Operator: *"I need you to operate more autonomously so to speak. I introduce way too many possible failures"*. Used in this plan's runbook for placeholder-scrubbing the `.env`.
2. **3x-daily handover discipline** — every handover doc carries these directives in full detail. Operator: *"Make sure that this idea you have does not get lost. It has to be in each of your documeted handovers three times a day in full detail."*
3. **C-drive HDD monitoring** — warn at 50GB, surface at 20GB, hard-stop at 10GB. Operator: *"You have space you can use on my C drive. Use it, but monitor its use so I dont go over my HDD capacity."* Current state at this stream's ship: 793 GB free (well above warn threshold).
4. **Status-reporting synthesis** — surface contradictions between PM-relayed directives and prior operator directives instead of silently complying.
