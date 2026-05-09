# Secrets Vault Migration -- Phase 1 (EOD-16 KK2)

**Date:** 2026-05-08 EOD-16
**Stream:** KK2 -- backend code swap to read secrets from DPAPI/Fernet vault
**Status:** Phase 1 shipped (vault-first reads with env fallback). Phase 2 (env retire) tracked below.
**Origin:** EOD-16 DD3 shipped the vault foundation; vault-migration script populated 6 secrets. Backend code STILL read `os.environ.get(KEY)` everywhere, so the encrypted vault was foundation-only and not yet consumed.

This doc covers what landed, what was deliberately skipped, the bootstrap chicken-and-egg constraint, and the Phase 2 plan to retire the .env plaintext for the 6 vault-ready secrets.

---

## What shipped (Phase 1)

### 1. New helper: `backend/security/secret_reader.py` (~200 lines)

Public API:

```python
get_secret(key: str, *, default: str | None = None, vault_first: bool = True) -> str | None
health() -> dict
reset_for_tests() -> None
```

Resolution order:

1. (when `vault_first=True`) consult `secrets_store.get_secret(key)` against the DPAPI/Fernet vault at `C:\CHL\data\secrets_store.json`.
2. Fall back to `os.environ.get(key)`.
3. Return the caller's `default`.

Module-level counters (reset via `reset_for_tests()`):

- `_vault_hits` -- key resolved from the encrypted vault.
- `_env_fallback_hits` -- key absent from vault, resolved from env.
- `_miss_with_default` -- absent from both, default returned.
- `_miss_no_default` -- absent from both, no default; None returned.
- `_vault_errors` -- vault read raised (counted separately so we can flag a broken vault distinct from "not in vault yet").

Logging:

- INFO on vault hit (key name only, never the value).
- WARNING on env fallback hit (highlights call sites still relying on .env plaintext).
- ERROR on miss-no-default (most likely a misconfiguration).

The vault read import is lazy (inside `_try_vault`) so the module stays importable in environments that haven't yet provisioned `CHL_SECRETS_MASTER_KEY`. Vault errors are caught and the helper falls through to env -- the server stays up even if the vault file goes missing.

### 2. Call sites migrated

Six secrets are vault-ready: `TELNYX_API_KEY`, `STRIPE_API_KEY`, `STRIPE_PUBLISHABLE_KEY`, `STRIPE_WEBHOOK_SECRET`, `JWT_SECRET`, `ADMIN_PASSWORD`. We migrated the call sites that actually exist in code today.

| File | Line(s) | Key | Notes |
|---|---|---|---|
| `backend/server.py` | 38 | `STRIPE_API_KEY` | module-level singleton; vault-first lookup at import |
| `backend/server.py` | 75 | `JWT_SECRET` | vault re-resolution after the boot guard (env-only by design) |
| `backend/server.py` | 591 | `ADMIN_PASSWORD` | `seed_admin` now vault-first |
| `backend/server.py` | 2935 | `STRIPE_API_KEY` | per-request Stripe Connect transfer key |
| `backend/server.py` | 4016 | `STRIPE_WEBHOOK_SECRET` | webhook signature verification |
| `backend/bulk_outreach_blast.py` | 61-62 | `JWT_SECRET`, `ADMIN_PASSWORD` | track-token HMAC secret |
| `backend/factor_autopair.py` | 49-50 | `JWT_SECRET`, `ADMIN_PASSWORD` | click-token HMAC secret |

Total: **5 call sites in server.py** (including module-level singletons), **4 call sites in subordinate modules** = 9 migrated.

### 3. Tests: `backend/tests/test_secret_reader.py` (~250 lines, 10 tests)

All 10 pass:

- `test_vault_hit_returns_vault_value` -- vault wins over env when both set
- `test_env_fallback_when_not_in_vault` -- env serves when vault empty
- `test_returns_default_when_neither_has_it` -- default fallback path
- `test_returns_none_when_no_default_and_neither_has_it` -- bare miss path
- `test_logs_at_appropriate_levels` -- INFO/WARNING/ERROR levels per path
- `test_health_counters_increment_correctly` -- counter math + `vault_ratio`
- `test_health_handles_zero_lookups` -- avoids div-by-zero pre-traffic
- `test_vault_first_false_skips_vault` -- emergency rotation-bypass path
- `test_vault_error_falls_through_to_env` -- broken vault doesn't crash server
- `test_invalid_key_raises` -- defensive against `None` / empty key

The vault is mocked via `monkeypatch.setattr(secrets_store, "get_secret", _fake_get)` so tests are hermetic -- they never touch the real `C:\CHL\data\secrets_store.json`.

Regression suites (`test_secrets_store`, `test_login_rate_limiter`, `test_payments_status_migration`, `test_stripe_webhook_security`) all still green: **39 passed**.

Preflight: clean -- `172 routers + 18 crons + 5 health-registers verified`.

---

## What we did NOT migrate (and why)

### Bootstrap-stage env reads (chicken-and-egg)

The vault load itself depends on:

1. `dotenv` having loaded `.env` (so `CHL_SECRETS_MASTER_KEY` is in `os.environ`).
2. `cryptography` being importable (Fernet fallback) OR Windows DPAPI being available.
3. `secrets_store.py` being on `sys.path`.

Variables read BEFORE step 3 completes MUST stay env-only. Specifically:

- `MONGO_URL` (server.py line 25) -- needed before any module import that depends on Mongo state.
- `DB_NAME` (server.py line 27) -- same.
- `CHL_SECRETS_MASTER_KEY` (read inside `secrets_store._get_fernet_key`) -- the vault decrypts its own contents using this; reading it from the vault would be a cycle.
- `CHL_SECRETS_STORE_PATH` (read inside `secrets_store._store_path`) -- tells us where the vault file lives; can't be vaulted.
- `CHL_SECRETS_FORCE_FERNET` -- test/migration flag, env-only by design.

These are documented in `secret_reader.py` module docstring as "bootstrap env vars".

### JWT_SECRET hard-fail boot guard (server.py line 45-57)

The boot guard at `server.py:45-57` raises `RuntimeError` if `JWT_SECRET` is unset or still on the placeholder `"change-me-in-production"`. This guard intentionally stays as an `os.environ.get("JWT_SECRET")` check -- it runs BEFORE secret_reader is even worth consulting, because:

- We need `.env` loaded with a non-empty value regardless (required for vault migration to even seed the vault from .env in the first place).
- Fail-fast at boot beats fail-late at first signing attempt.
- It's the canary for "operator forgot to provision". The vault re-resolution at line 75 is a separate concern: "use the canonical encrypted value for runtime signing".

Concretely:

```python
JWT_SECRET = os.environ.get("JWT_SECRET")          # boot check
if not JWT_SECRET or JWT_SECRET == "change-me-in-production":
    raise RuntimeError(...)                         # fail-fast
JWT_SECRET = secret_reader.get_secret("JWT_SECRET", default=JWT_SECRET) or JWT_SECRET
```

### TELNYX_API_KEY consumer code

`TELNYX_API_KEY` is in the vault but no consumer code references it yet (post-port wiring). We skipped pre-wiring vault reads for it; when the Telnyx consumer module lands, it should use `secret_reader.get_secret("TELNYX_API_KEY")` from day one.

### STRIPE_PUBLISHABLE_KEY frontend config endpoint

Vaulted but not currently read anywhere in `backend/`. The future `/api/stripe/config` endpoint that exposes the publishable key to the frontend should use `secret_reader.get_secret("STRIPE_PUBLISHABLE_KEY")`.

### shipper_magic.py MAGIC_LINK_SECRET fallback

`shipper_magic.py:33` reads `os.environ.get("MAGIC_LINK_SECRET", os.environ.get("JWT_SECRET", "chl-dev-secret"))`. The `JWT_SECRET` fallback there is one os.environ.get -- migrating it would force a per-call vault hit. Deferred to Phase 2 (where MAGIC_LINK_SECRET also gets a vault entry).

### Test files

We did NOT migrate the test files that reference these env vars. Tests use `monkeypatch.setenv(...)` to control values; routing them through the vault would require per-test vault setup with no security gain.

---

## The env-fallback safety net

Phase 1 keeps `.env` plaintext as a fallback for two reasons:

1. **Boot resilience.** If `C:\CHL\data\secrets_store.json` goes missing (rolling backup restore, accidental delete, fresh checkout), the server keeps booting on .env until the operator can re-run the migration.
2. **Incremental migration.** Not every secret is in the vault yet (TELNYX_API_KEY, MAGIC_LINK_SECRET, ADMIN_EMAIL, MONGO_URL, ...). Vault-only would require provisioning ALL ~140 env-read sites first, which is a Phase 2 goal.

The cost of the fallback is one WARNING log per call site that hits env. The benefit is a clear migration-progress signal: as Phase 2 wraps, env-fallback log lines should trend toward zero, and `health()["vault_ratio"]` should approach 1.0.

---

## Phase 2 plan -- retire .env plaintext for the 6 vaulted secrets

**Goal:** for the 6 secrets currently in the vault, the .env file no longer contains the plaintext value. Only `CHL_SECRETS_MASTER_KEY` (the Fernet key) plus bootstrap vars (`MONGO_URL`, `DB_NAME`, ...) live in .env.

### Steps (priority order)

1. **Verify all 6 secrets resolve from vault in production.** Drive 24h of traffic through `secret_reader.health()` and confirm `vault_hits >= 100` for each of the 6 keys with `env_fallback_hits == 0`. Catches any call site we missed.

2. **One-shot prune of .env.** Write `scripts/prune_env_after_vault_migration.py` that reads `backend/.env`, removes the 6 specific lines (`TELNYX_API_KEY=`, `STRIPE_API_KEY=`, `STRIPE_PUBLISHABLE_KEY=`, `STRIPE_WEBHOOK_SECRET=`, `JWT_SECRET=`, `ADMIN_PASSWORD=`), and writes a backup at `backend/.env.pre-prune-2026_05_08.bak`. Idempotent: if a key is already absent, skip silently.

3. **Boot-time `os.environ.pop()` sweep.** In `server.py` after `load_dotenv` runs but BEFORE `secret_reader` is imported, pop each of the 6 keys from `os.environ`. This means even if .env still has them (operator forgot to prune), the secret_reader env-fallback path will miss for those 6, forcing a vault hit. Belt + suspenders.

4. **Verification harness.** Add `backend/tests/test_phase2_env_retired.py` that:
   - Asserts `os.environ.get(key) is None` for each of the 6 keys after server import.
   - Asserts `secret_reader.get_secret(key) is not None` for each (vault-only resolution works).
   - Asserts `secret_reader.health()["env_fallback_hits"] == 0` after a full server boot.

5. **Observability surface.** Add `/api/admin/secrets/health` endpoint (broker-auth gated) that returns `secret_reader.health()` + `secrets_store.health()`. Lets the operator dashboard show "vault hits / env fallbacks / vault ratio" at a glance.

6. **Boot guard for the JWT_SECRET line 45-57.** When Phase 2 lands, the `os.environ.get("JWT_SECRET")` boot guard at server.py:45 will START FAILING (because step 3 popped it). Update the guard to read from `secret_reader.get_secret("JWT_SECRET", vault_first=True)` and check that result -- still hard-fail-on-missing, just hard-fail off the vault rather than off env.

### Risks + mitigations

- **Forgot a call site.** Mitigation: step 3's `os.environ.pop()` sweep + step 4's verification harness. Any straggler will surface as `secret_reader: env fallback for X` log -> `secret_reader: X not in vault, not in env, no default` -> visible failure rather than silent fallback.
- **Vault file corruption / DPAPI key loss.** Mitigation: `scripts/migrate_secrets_to_vault.py` is re-runnable. Operator restores .env from backup, re-runs migration. Rolling D-drive snapshot covers vault file too.
- **Cross-host portability.** Vault entries are encrypted with DPAPI when run as the operator user; cross-user / cross-host requires re-encryption. Phase 2 should add a `secrets_store.export_for_migration()` that re-encrypts everything to Fernet for portable transit.

---

## Audit trail (this doc -- ASCII-only, no secret values)

- No secret VALUES appear in this doc. Per `feedback_secrets_local_only_no_bridge_transmission.md`, only env var NAMES, file paths, and counter mechanics are documented.
- The 6 vault-ready key names (`TELNYX_API_KEY`, `STRIPE_API_KEY`, `STRIPE_PUBLISHABLE_KEY`, `STRIPE_WEBHOOK_SECRET`, `JWT_SECRET`, `ADMIN_PASSWORD`) are not secret -- they're identifiers, like column names in a database schema.
- This doc is safe to commit to chl-memory (which is a separate git repo with its own access controls).

---

## Files touched (commit summary, dev side -- NOT auto-committed per stream constraints)

- **NEW:** `backend/security/secret_reader.py` (~200 lines) -- vault-first reader with health surface
- **NEW:** `backend/tests/test_secret_reader.py` (~250 lines, 10 tests) -- hermetic, all pass
- **MODIFIED:** `backend/server.py` -- 5 call sites + 1 import line
- **MODIFIED:** `backend/bulk_outreach_blast.py` -- 1 helper updated (2 vault reads)
- **MODIFIED:** `backend/factor_autopair.py` -- 1 helper updated (2 vault reads)

Stream constraint per directive: doc commits to chl-memory; code does NOT commit (operator review first).

---

## Top-3 follow-ups (handed off to next iter)

1. **Phase 2 .env prune sweep** -- see Phase 2 plan above. Highest priority because the value of vaulting the 6 secrets is partially undermined while the plaintext still sits in .env at rest.

2. **`CHL_SECRETS_MASTER_KEY` rotation procedure** -- the Fernet key in .env can be rotated, but only with all 6 secrets re-encrypted in a single coordinated step. Document the exact sequence: (a) generate new key, (b) decrypt-with-old, re-encrypt-with-new for each entry, (c) atomic swap of .env line + secrets_store.json contents, (d) restart-verify. Add `scripts/rotate_master_key.py`.

3. **Vault-read TTL cache (if perf hotspot found)** -- the helper currently reads the vault file fresh on every call. For 6 reads per request this is fine (small file, ~2ms); but if `secret_reader.health()["total_lookups"]` shows >100k/min we should add a 60s TTL cache to honor live rotation while amortizing the disk hit. Profile first.
