# Secrets Vault Migration -- Phase 2 (NN4)

**Date:** 2026-05-08
**Stream:** NN4 -- .env prune + master-key rotation (Phase 2 follow-up to KK2)
**Status:** Tools shipped (prune + rotation + 8 tests). APPLY mode NOT executed in this stream -- operator runs apply when ready.
**Origin:** KK2 (2026-05-08 EOD-16) shipped Phase 1 -- vault-first reads with .env fallback. The 6 secrets were live in the encrypted vault at `C:\CHL\data\secrets_store.json` AND still in plaintext at `backend/.env` as a safety net. Phase 2 retires the .env plaintext for 5 of those 6 secrets and ships the master-key rotation procedure.

This doc is the operator runbook for both flows.

---

## Why .env still has plaintext after Phase 1

Phase 1 (KK2) deliberately left `.env` plaintext as a fallback because:

1. **Boot resilience** -- if `secrets_store.json` goes missing (rolling backup restore, accidental delete), the server keeps booting on `.env` until the operator can re-run the migration.
2. **Incremental migration** -- only 6 secrets had been vaulted; flipping the server to vault-only would require migrating all ~140 env-read sites first.

Phase 1's safety net has now done its job. The 6 vaulted secrets are stable in production, vault-first reads have been live on the migrated call sites for a full session, and the plaintext-at-rest surface is the next thing to close.

---

## Phase 2 scope: what gets pruned

### Pruned (5 keys removed from .env)

- `TELNYX_API_KEY`
- `STRIPE_API_KEY`
- `STRIPE_PUBLISHABLE_KEY`
- `STRIPE_WEBHOOK_SECRET`
- `ADMIN_PASSWORD`

### Kept in .env (load-bearing reasons)

- `CHL_SECRETS_MASTER_KEY` -- vault decryption needs this
- `MONGO_URL` -- read at server.py:25, BEFORE any module that depends on Mongo state imports
- `DB_NAME` -- read at server.py:27, same bootstrap reason
- `JWT_SECRET` -- the boot hard-fail guard at `server.py:45-57` reads this via `os.environ.get('JWT_SECRET')` before `secret_reader` is consulted. Pruning it would break the boot guard. Future Phase 3 stream (after the boot guard is rewritten to use `secret_reader`) can prune this too.
- All non-vaulted env vars are passed through untouched (e.g., `MONGO_URL`, `DB_NAME`, `ADMIN_EMAIL`, `ZOHO_IMAP_PASSWORD`, `OPENAI_API_KEY`, `ANTHROPIC_API_KEY`, etc.) -- those are out of scope until they get vaulted in a later stream.

---

## Tool A: prune_env_after_vault_migration.py

`C:\CHL\scripts\prune_env_after_vault_migration.py`

### CLI

```
python scripts/prune_env_after_vault_migration.py            # dry-run (default)
python scripts/prune_env_after_vault_migration.py --apply    # actually prune
python scripts/prune_env_after_vault_migration.py --restore  # restore latest .bak
```

### What dry-run does

- Runs all pre-flight checks against the real .env + vault.
- Prints which keys WOULD be pruned and which WOULD be kept.
- Touches no files.

### What --apply does (in order)

1. **Pre-flight (refuses to proceed on any failure):**
   - `.env` exists at `backend/.env`
   - vault file exists at `data/secrets_store.json`
   - `CHL_SECRETS_MASTER_KEY` loadable from os.environ or .env
   - vault readable (`secrets_store.health()` returns `store_path_exists=True`)
   - **For each pruning candidate:** present in .env AND present in vault AND decryptable AND `sha256[:16]` of .env value equals `sha256[:16]` of vault value
2. **Backup:** `backend/.env.pre-prune-<YYYYMMDD_HHMMSS>.bak` (copy2 preserves mtime).
3. **Atomic rewrite:** tmp file -> `os.replace()`. Preserves blank lines, comments, and ordering of all kept lines.
4. **Post-prune verify:** clears the 5 pruned keys from `os.environ`, reloads .env (so master key stays populated), then for each pruned key calls `secrets_store.get_secret(KEY)` and confirms `sha256[:16]` matches the pre-prune value.
5. **Auto-restore on failure:** if step 4 fails, copies the .bak back over .env and exits non-zero. The operator never ends up with a half-pruned .env.

### What --restore does

Finds the most recent `.env.pre-prune-*.bak` in `backend/` and copies it over `.env`. Use this if you ran `--apply`, restarted the backend, and discovered the new vault path is broken in some way the auto-verify didn't catch. Then restart the backend.

### Operator runbook (--apply)

1. From `C:\CHL` shell:
   ```
   C:\CHL\.venv-local\Scripts\python.exe scripts\prune_env_after_vault_migration.py
   ```
   Confirm dry-run reports 5 keys "present + decryptable + hashes match".

2. Run the apply:
   ```
   C:\CHL\.venv-local\Scripts\python.exe scripts\prune_env_after_vault_migration.py --apply
   ```
   Expected output ends with "DONE: .env pruned + vault round-trip verified."

3. Restart the backend so the pruned values leave `os.environ`:
   ```
   Restart-Service CHL-Backend
   ```

4. Smoke-test:
   - Hit `/api/admin/health/status` (or whatever live health endpoint is wired).
   - In the backend log, confirm zero `secret_reader: env fallback for X` lines for the 5 pruned keys.
   - In `secret_reader.health()`, confirm `vault_hits` increments and `env_fallback_hits` stays at 0 for the 5 keys.

### Recovery scenarios for the prune flow

- **Pre-flight fails on hash mismatch.** Most likely cause: operator rotated a secret in .env but didn't re-run the vault migration. Fix: rerun `scripts/migrate_secrets_to_vault.py` to bring the vault back in sync, then retry the prune dry-run.
- **Pre-flight fails on vault missing.** Most likely cause: vault file was deleted or the path env override (`CHL_SECRETS_STORE_PATH`) is misconfigured. Fix: check the path, restore the vault from the rolling D-drive backup, or re-run migration.
- **Apply succeeds but the backend can't read a pruned secret on restart.** Cause: a call site was missed in Phase 1 and is still calling `os.environ.get(KEY)` directly. Symptom: backend logs `KEY: not in vault, not in env, no default`. Fix: `python scripts/prune_env_after_vault_migration.py --restore`, restart backend, then migrate that call site to `secret_reader.get_secret(KEY)` before re-attempting the prune.

---

## Tool B: rotate_master_key.py

`C:\CHL\scripts\rotate_master_key.py`

Rotates `CHL_SECRETS_MASTER_KEY` and re-encrypts every vault entry under the new key in one atomic step.

### CLI

```
python scripts/rotate_master_key.py            # dry-run plan
python scripts/rotate_master_key.py --apply    # execute rotation
python scripts/rotate_master_key.py --rollback # restore last backup pair
```

### What --apply does (in order)

1. **Plan (refuses to proceed on failure):**
   - .env + vault file exist
   - current `CHL_SECRETS_MASTER_KEY` is loadable
   - every vault entry decrypts cleanly under the current key (with `sha256[:16]` printed for the audit)
2. **Capture pre-rotation hashes** for every entry (used in step 8 verification).
3. **Backup pair written:**
   - `backend/.env.pre-rotate-<ts>.bak`
   - `data/secrets_store.pre-rotate-<ts>.json.bak`
4. **Generate new Fernet key** (`Fernet.generate_key()`, 44-char base64).
5. **Swap `os.environ['CHL_SECRETS_MASTER_KEY']`** to the new key in-process.
6. **Re-encrypt every entry** -- iterate the captured plaintexts, call `secrets_store.encrypt(plaintext)` (which now uses the new key via env), build a fresh `entries` dict in memory. `rotation_count` is incremented per entry to keep the per-secret rotation audit accurate.
7. **Atomic-write** the new vault file (tmp -> `os.replace()`).
8. **Atomic-update .env** with the new master key line (tmp -> `os.replace()`). Other .env lines untouched.
9. **Verify:** for every entry, call `get_secret(KEY)` and confirm `sha256[:16]` matches the pre-rotation hash. Any mismatch triggers rollback.
10. **Audit log:** append a JSONL line to `data/master_key_rotations.log` with timestamp, outcome, key count, and backup paths. Hash of new key is logged (sha256[:16]) but never the key itself.

### Rollback

- **Automatic** -- on any exception during steps 5-9, the script copies the .bak files back over `.env` and the vault file, restores the OLD master key in `os.environ`, logs `outcome: rolled_back` to the audit log, and exits non-zero.
- **Manual** -- `python scripts/rotate_master_key.py --rollback` finds the most recent `.pre-rotate-*.bak` pair and restores both. Useful if the rotation appeared to succeed but a downstream service can't decrypt for some reason (the script wouldn't have detected this since the in-process verify passed).

### When to rotate

- **Annually** -- general hygiene cadence. SOC 2 Type II readiness (when CHL gets there) will mandate documented key rotation; an annual run keeps the muscle memory + audit trail in place.
- **Within 24h of suspected exposure** -- if `CHL_SECRETS_MASTER_KEY` may have been read by an unauthorized party (lost laptop, screen-shared .env, accidental commit, etc.). The 24h window keeps the exposure tight.
- **Before handing off the operator account** -- transferring CHL to a new operator means transferring secrets; rotating the master key prevents the prior operator from decrypting future vault states.
- **After a host migration** -- if cross-host (DPAPI was per-user encrypted) re-encryption is needed. We force Fernet for portability so the rotation doubles as a host-migration tool.

### Operator runbook (--apply)

1. Take a manual `chl-memory` git commit + push BEFORE rotation -- locks in a Tier 2 audit checkpoint.
2. Run the dry-run:
   ```
   C:\CHL\.venv-local\Scripts\python.exe scripts\rotate_master_key.py
   ```
   Confirm every entry shows "decryptable under current key".

3. Apply:
   ```
   C:\CHL\.venv-local\Scripts\python.exe scripts\rotate_master_key.py --apply
   ```
   Expected ends with "DONE: master key rotated, vault re-encrypted, audit logged."

4. Restart the backend so it loads the new master key:
   ```
   Restart-Service CHL-Backend
   ```

5. Smoke test secret reads (any vault-first call site).

6. Verify the audit log:
   ```
   Get-Content C:\CHL\data\master_key_rotations.log -Tail 5
   ```

### Recovery scenarios for the rotation flow

- **Rotation aborts during plan.** No state changed; .env + vault untouched. Read the FAIL message and fix the underlying issue (most often: master key not loaded, or vault entry corruption from a separate cause).
- **Rotation aborts during step 6-9 (verification fails).** Auto-rollback fires; script restores both files from the .bak pair. Audit log records `outcome: rolled_back` with the reason. Read the reason; if it's a transient issue (disk full mid-write) re-run after fixing. If it's a corruption issue, restore the vault from the rolling D-drive snapshot.
- **Rotation appears to succeed but a service later fails to decrypt.** Run `--rollback`, restart the backend. Most likely cause: a service was holding the OLD master key in memory (not re-reading .env on restart). Investigate the service's secret-loading pattern.
- **Backup files accidentally deleted.** The audit log at `data/master_key_rotations.log` records the previous key sha256[:16]. If the old key itself is recoverable (D-drive snapshot, git history of the .env if it was ever committed -- it shouldn't be), re-encrypt from there. Otherwise the rotation is irreversible and only forward recovery is possible (restore vault from a pre-rotation D-drive snapshot whose .env still has the old master key).

---

## Tests

`backend/tests/test_env_prune.py` -- 4 tests:

- `test_prune_dry_run_does_not_modify_env` -- dry-run touches no files
- `test_prune_refuses_when_vault_missing` -- pre-flight catches missing vault
- `test_prune_refuses_when_hashes_mismatch` -- per-key hash mismatch refuses
- `test_prune_atomic_backup_and_restore_on_post_check_fail` -- failed verify auto-restores

`backend/tests/test_master_key_rotation.py` -- 4 tests:

- `test_rotation_re_encrypts_all_secrets` -- ciphertexts change, plaintexts round-trip
- `test_rotation_old_key_no_longer_decrypts` -- old key fails after rotation
- `test_rotation_atomic_rollback_on_failure` -- mid-rotation failure -> full restore
- `test_rotation_dry_run` -- plan-only, zero disk writes, zero backup files

All 8 pass. Tests use `tmp_path` for sandboxed .env + vault and `CHL_SECRETS_FORCE_FERNET=1` for host portability (no Windows DPAPI dependency in CI).

---

## Constraints honored

- ASCII-only, type hints throughout.
- No destructive operation runs without an atomic backup taken first.
- No plaintext value is ever printed; only `sha256[:16]` for hashing equality.
- Apply mode is NOT executed in this build stream. Operator runs `--apply` when ready.
- The boot hard-fail JWT_SECRET guard at `server.py:45-57` is NOT touched. JWT_SECRET stays in .env per KK2 design.
- Code does NOT auto-commit; only this doc is committed to chl-memory.

---

## Top-3 follow-ups handed off to next iter

1. **HSM / KMS integration for the master key.** Today `CHL_SECRETS_MASTER_KEY` lives in `.env` -- a single layer of indirection above plaintext. Moving it to a hardware-backed key vault (Windows TPM via `BCryptKeyDerivation`, or AWS KMS / GCP KMS / Azure Key Vault on the cloud side when CHL goes multi-region) would mean the master key never sits on disk, and rotation becomes a vault-API call instead of an .env edit. Effort: 1-2 days for a TPM-only path; 3-5 days for a multi-cloud KMS abstraction.

2. **Per-environment key derivation.** Currently the same `CHL_SECRETS_MASTER_KEY` would be used across local-dev, staging, and prod. Phase 3 should introduce a derived-key scheme: a single root key + per-env salt -> environment-specific Fernet keys. Compromise of staging key doesn't compromise prod. Effort: ~1 day to add `_derive_key(env_label)` + a migration that re-encrypts the vault under the derived key for the current env.

3. **Rotation audit trail in chl-memory + admin UI surface.** The rotation log at `data/master_key_rotations.log` lives only on C-drive; mirror to chl-memory so PM Claude side can see "last rotated YYYY-MM-DD" without filesystem access. Add an `/api/admin/secrets/rotation_status` endpoint (broker-auth gated) returning the last rotation timestamp + key count + outcome, surfaced on the admin dashboard alongside the existing `secret_reader.health()` ratio. Effort: ~half-day. Closes the loop on observability for an operation that's invisible until it goes wrong.
