# Auth posture audit — 2026-05-08

**Author:** sub-agent CC3 (security/stability gap-analysis stream, item 1.5).
**Scope:** every FastAPI route under `backend/` + the auth dependency wrappers
in `backend/server.py` lines 39–164.
**Method:** grep-based static audit; no live traffic. Findings are static —
runtime checks (token TTL, cookie attributes, etc.) confirmed by source read.
**Why now:** EOD-13 secret-leak incident exposed ADMIN_PASSWORD + auth PIN in
git history. Rotation done; the broader story is auth posture is unaudited.
This doc is the canonical posture inventory + the queue for the next iter.

---

## 1. Auth-dependency inventory

The five gates wrapping `get_current_user` in `backend/server.py`:

| Dep | Effect | Total uses (backend-wide) |
| --- | --- | --- |
| `Depends(require_broker)` | role == "broker" | 486 |
| `Depends(require_owner)` | email in OWNER_EMAILS allowlist | 217 |
| `Depends(require_carrier)` | role == "carrier" | 16 |
| `Depends(require_auth)` | any authed user | 9 |
| `Depends(require_broker_or_carrier)` | role in {broker, carrier} | 1 |
| `Depends(get_current_user)` | direct (any authed user; no role gate) | 103 in server.py + many module-level |
| `Depends(get_current_user_optional)` | soft auth (None on miss) | 2 |

Total auth-gated decorators across backend/: ~730 site-uses, distributed across
~165 router files. The pattern is consistent — almost every write endpoint is
wrapped. The exceptions are below.

---

## 2. Endpoint-by-endpoint posture (representative write endpoints)

This is not exhaustive — backend/ has 175+ endpoint files. The table covers
the high-value write surfaces (anything that mutates DB, contacts a vendor,
or moves money). Risk levels:

* **HIGH** — gap or weak gate on a money / vendor / PII path.
* **MED** — gap on a non-money mutating path; still wants tightening.
* **LOW** — gap on a low-impact path or already-mitigated by a layer above.

| Path | Method | Gate | Risk | Notes |
| --- | --- | --- | --- | --- |
| `/api/auth/login` | POST | none (input) | n/a | Mongo + (NEW) in-mem rate limit + bcrypt |
| `/api/auth/login-with-pin` | POST | IP rate-limit only | MED | PIN is owner-only; 4-12 digit PINs are weak; needs 2FA second factor |
| `/api/auth/owner-reset` | POST | EMERGENCY_RESET_KEY OR current_password | MED | If EMERGENCY_RESET_KEY leaks, full owner takeover; rotation + monitoring needed |
| `/api/auth/register` | POST | none (carriers self-register) | LOW | Role is forced "carrier"; password >= 6 chars (weak; bump to 8) |
| `/api/auth/register-shipper` | POST | none (public) | LOW | Same shape as carrier register |
| `/api/auth/change-password` | POST | get_current_user | LOW | Verifies current pw; OK |
| `/api/auth/refresh` | POST | refresh_token cookie | MED | Refresh has no rotation — any captured refresh token usable for 30d |
| `/api/auth/logout` | POST | get_current_user | LOW | Clears cookies; does NOT revoke refresh server-side (no token_version bump) |
| `/api/auth/set-pin` | POST | is_owner | LOW | Hashed at rest; OK |
| `/api/_client_errors` | POST | NONE | MED | `client_error_logger.py:217`. Public log endpoint — easy spam vector |
| `/api/telemetry/error` | POST | NONE | MED | `telemetry.py:52`. Same pattern |
| `/api/telemetry/{event_id}/resolve` | POST | NONE | MED | `telemetry.py:151` — mutates DB row by event_id with no auth |
| `/api/auth/magic-link/request` | POST | NONE | MED | `magic_link.py:139` — public request flow OK; rate-limit-light |
| `/api/auth/magic-link/consume` | POST | token-based | LOW | Single-use; signed; OK |
| `/api/failover/promote` | POST | NONE | **HIGH** | `failover_router.py:76` — public failover trigger?! Confirm gate via env-only; needs require_owner |
| `/api/failover/demote` | POST | NONE | **HIGH** | Same as above |
| `/api/fast-lane/submit` | POST | NONE | MED | `fast_lane.py:62` — public job submission |
| `/api/loads/{id}/dispatch` | POST | NONE | **HIGH** | `integrity/operational_state.py:44` — write path; if this is *the* dispatch endpoint it MUST be gated |
| `/api/loads/{load_ref}/dispatcher-message` | POST | NONE | MED | `command_control.py:338` |
| `/api/loads/{load_ref}/emergency-contact` | POST | NONE | MED | `command_control.py:409` |
| `/api/driver/{driver_id}/messages/{message_id}/read` | POST | NONE | LOW | `command_control.py:506` — read-flag mutation |
| `/api/employees` (CRUD) | POST/PATCH/DELETE | require_broker | LOW | OK |
| `/api/payroll/run` | POST | require_broker | MED | Money path — should be require_owner, not just broker |
| `/api/payroll/runs/{id}/process` | POST | require_broker | MED | Same — owner-only |
| `/api/cash-valve/sweep-now` | POST | require_owner | LOW | Correct (money) |
| `/api/cash-valve/sweep-now-v2` | POST | require_owner | LOW | Correct |
| `/api/stripe-connect/start` | POST | require_broker | MED | Money/vendor path — should be require_owner |
| `/api/stripe-connect/status` | GET | require_broker | LOW | Read-only OK |
| `/api/proximity-blast` | POST | require_broker | MED | Vendor cost (SMS) — should add per-broker daily cap |
| `/api/phone/send-sms` | POST | require_broker | MED | Vendor cost — daily cap recommended |
| `/api/phone/sms-broadcast` | POST | require_broker | MED | Vendor cost — daily cap recommended |
| `/api/phone/click-to-call` | POST | require_broker | MED | Vendor cost — daily cap recommended |
| `/api/instant-quote` | POST | require_broker | LOW | OK |
| `/api/business-settings` | PATCH | require_broker | MED | Mutates org settings — should be require_owner |
| `/api/loads/*` | POST/PATCH/DELETE | require_broker (most) | LOW | OK |
| `/api/drivers/*` | POST/PATCH/PUT | get_current_user | MED | No role gate — any authed user can mutate driver records |
| `/api/loads/*/cancellation-reasons` | GET | none (public list) | LOW | Static reference list; OK |
| `/api/share/*` (public viewer) | GET | token-based | LOW | Token IS the auth; correct pattern |
| `/api/carriers/{dot}/contact_enrichment/refresh` | POST | require_broker | LOW | Has 60s cooldown (the only rate limit in backend pre-CC3) |
| `/api/carriers/{dot}/documents/{type}/upload` | POST | require_broker + size cap | LOW | OK pre-MIME-validation; gap 2.5 |

**Key gaps (must fix; numbered for tracking):**

1. **`/api/failover/promote` + `/api/failover/demote`** — failover_router.py registers these without `Depends(require_owner)`. If the env-only gate doesn't kick in, an unauthenticated POST flips the cluster. **HIGH.**
2. **`/api/loads/{id}/dispatch`** in operational_state.py — write path with no auth dep. Confirm it's not THE dispatch endpoint; if so, gate immediately. **HIGH.**
3. **`/api/_client_errors` + `/api/telemetry/error` + `/api/telemetry/{id}/resolve`** — public POST endpoints. Spam-vector + mutating-by-id are MED individually but together = telemetry log corruption / DoS via floods. **MED.**
4. **`/api/payroll/run` + `/api/stripe-connect/start`** are gated by `require_broker` not `require_owner`. Money paths must be owner-only — broker employees should not be able to wire payroll or attach Stripe accounts. **MED.**
5. **`/api/business-settings` PATCH** — mutates org-wide settings under `require_broker`; should be owner-only. **MED.**
6. **`/api/drivers/*` writes** under `Depends(get_current_user)` only (no role gate). Any authed user (carrier role included) can mutate driver records. **MED.**
7. **`/api/auth/login-with-pin`** — 4-12 digit PIN with no 2FA backstop. Soft lockout 5/5 + 15min OK; cumulative cap missing. CC3 stream B fixed the cumulative cap on email login but PIN endpoint still uses the older Mongo-only path. **MED.**
8. **`/api/auth/refresh`** — no token rotation. Captured refresh token = 30 days of access. Recommend bumping `token_version` on every privileged action OR rotating refresh on use. **MED.**

---

## 3. Token + cookie posture

* **JWT secret:** `JWT_SECRET` env var; defaults to `"change-me-in-production"` at server.py:45 if unset. **HIGH** — verify the live deploy has this set; the default would let anyone mint a valid access token.
* **Algorithm:** HS256. OK for single-issuer.
* **Access token TTL:** 8 hours (`ACCESS_TOKEN_MINUTES = 60 * 8`). On the longer end of acceptable; tradeoff = broker workflow convenience.
* **Refresh token TTL:** 30 days. Long. Combined with no-rotation = single capture is durable.
* **Cookie attrs:** `httponly=True, secure=True, samesite="none"`. Good (httpOnly + Secure). `SameSite=None` is required because the PWA is cross-origin to the API; OK if served over HTTPS only.
* **`token_version` field:** present on user docs but only bumped on `owner-reset` (line 322). NOT bumped on logout, password change, or "log out everywhere" — so a stolen access token outlives a password change.

**Recommended fixes:**

* Hard-fail on startup if `JWT_SECRET` is the default. ~5 lines in `seed_admin()`.
* Bump `token_version` on `change-password`, `logout-all-devices` (new endpoint), and on every `set-pin`. Validate `token_version` in `get_current_user`.
* Halve refresh-token TTL to 7 days; add rotate-on-use.

---

## 4. Rate-limit posture

| Surface | Rate limit today | Recommend |
| --- | --- | --- |
| `/api/auth/login` (email) | Mongo lockout: 5 in window, 15min | + In-mem pre-Mongo gate (CC3 stream B; SHIPPING THIS PR) + cumulative 10 cap |
| `/api/auth/login-with-pin` | Mongo lockout: 5 / 15min by IP | + cumulative cap; needs 2FA after enrollment |
| `/api/auth/owner-reset` | NONE | Add 3-per-hour-per-IP cap |
| `/api/auth/register` | NONE | Add 5-per-day-per-IP cap (bot signup vector) |
| `/api/auth/register-shipper` | NONE | Same |
| `/api/auth/magic-link/request` | NONE | Add 3-per-hour-per-email cap |
| `/api/_client_errors` | NONE | Add 60-per-min-per-IP cap |
| `/api/telemetry/*` | NONE | Same |
| `/api/carriers/{dot}/contact_enrichment/refresh` | 60s cooldown (the only one) | Keep; pattern to extend |
| `/api/carriers/{dot}/documents/{type}/upload` | 25MB size cap; no rate | Add 10-per-min-per-broker cap |
| `/api/phone/send-sms` etc. | NONE | Add daily-spend cap (vendor cost) |
| `/api/proximity-blast` | NONE | Same |
| All `/api/*` writes (general) | NONE | Add a global 600-rpm-per-IP middleware |

**Path forward:** the 60s cooldown pattern at `carrier_docs/extraction_router.py:228` is the right shape. Extend it to a reusable `@rate_limit(...)` decorator + a global middleware bucket. SlowAPI is the natural fit. Captured as security gap 2.3.

---

## 5. CC3 stream B in-memory rate limiter (NEW this iter)

`backend/auth/login_rate_limiter.py` (250 lines). Sits in front of the existing
Mongo lockout for the email-login endpoint:

* Soft lockout: 5 failures in 5min -> 15min block
* Hard lockout: 10 cumulative -> 60min block
* LRU-bounded at 10000 entries (DoS-resistant)
* Owner emails immune (uses existing `_is_owner_email` check)
* Generic 429 on lockout — does NOT leak username existence
* All in-process; survives request-to-request without DB hit

Tests in `backend/tests/test_login_rate_limiter.py` (6/6 passing). Wired into
`/api/auth/login` only this iter; PIN login + owner-reset + magic-link still
to wire (queued as next-iter follow-ups).

---

## 6. Future schema (TOTP 2FA scaffolding — landed this iter, NOT enabled)

`backend/auth/totp_2fa.py` (200 lines) ships the cryptographic primitives.
Schema not yet migrated; capture here for the next iter:

| Field | Type | Notes |
| --- | --- | --- |
| `totp_secret_enc` | string \| null | Fernet-wrapped base32 secret. Null = 2FA not enrolled. |
| `totp_enrolled_at` | ISO datetime \| null | First successful enrollment confirmation. |
| `totp_backup_codes_enc` | array of strings \| null | One-time recovery codes, hashed at rest. Generate 10 at enrollment. |
| `totp_last_used_step` | int \| null | RFC 6238 30-second step counter of the most-recent successful verification. **Required for replay protection** — every verify must reject codes whose matched step <= `totp_last_used_step`. |

**Migration plan for the next iter:**

1. Add the four fields to `auth_users` collection (Motor; no schema enforcement; just defensive defaults of null).
2. Index `auth_users.email` (already present) + a partial index on `totp_enrolled_at` to accelerate "is 2FA on?" queries.
3. New endpoints:
    * `POST /api/auth/2fa/enroll/start` -> returns secret + provisioning URI for QR
    * `POST /api/auth/2fa/enroll/confirm` -> verifies first code; on success, persist encrypted secret + 10 backup codes
    * `POST /api/auth/2fa/verify` -> mid-session re-auth for privileged ops
    * `POST /api/auth/2fa/disable` -> requires current password + a current TOTP code
4. Modify `login` flow to require 2FA when `totp_enrolled_at IS NOT NULL`. New intermediate state: "password OK; awaiting 2FA"; issue a short-lived "pre-auth" token (5min TTL) that only `/2fa/verify` accepts.
5. **DO NOT** enable on existing users without explicit operator sign-off. Owner enrolls first, validates flow on their own account, then opens enrollment to broker employees.

**At-rest key:** `CHL_TOTP_AT_REST_KEY` env var (Fernet 32-byte urlsafe base64). The next iter must:

1. Generate one (`python -c "from cryptography.fernet import Fernet; print(Fernet.generate_key().decode())"`)
2. Persist via `scripts/set_env_var.ps1 CHL_TOTP_AT_REST_KEY <key>`
3. Restart `CHL-Backend` service

The TOTP module's `_load_or_init_at_rest_key` will generate an in-process key if the env var is unset, but that key dies on restart and decrypts nothing — which is the right scaffolding behavior.

---

## 7. Recommended ship order (next iter, priority-ordered)

| Order | Item | Effort | Risk reduction |
| --- | --- | --- | --- |
| 1 | Hard-fail JWT_SECRET == default on startup | 15 min | High |
| 2 | Gate `/api/failover/promote` + `/demote` with require_owner | 30 min | High |
| 3 | Confirm + gate `/api/loads/{id}/dispatch` in operational_state.py | 30 min | High |
| 4 | Bump `token_version` on `change-password` + `set-pin` + new `logout-all-devices` | 1 hour | Med |
| 5 | Move `/api/payroll/*` + `/api/stripe-connect/*` from require_broker -> require_owner | 30 min | Med |
| 6 | TOTP 2FA migration + enrollment endpoint (operator-only) | 4 hours | High |
| 7 | Wire in-memory rate limiter into PIN login + owner-reset + magic-link request | 30 min | Med |
| 8 | Generic write-endpoint global rate limit middleware | 2 hours | Med |
| 9 | Tighten role gates on `/api/drivers/*` | 30 min | Med |
| 10 | Reduce refresh-token TTL to 7d + rotate-on-use | 1 hour | Med |

Total ~10 hours of work; covers everything except the broader 2FA roll-out
and the global rate-limit middleware, both of which are bigger.

---

## 8. Out-of-scope for this iter

* Per-PII field encryption (`tax_id_last_4`, etc.) — captured as gap 1.2.
* Cloudflare Tunnel / TLS — gap 2.1.
* SSRF allow-list — gap 2.4.
* File-content-type MIME validation — gap 2.5.
* CORS lockdown — gap 2.2; verify no `*` in production CORS_ORIGINS.
* CVE scanning automation — gap 1.3 (already specced).
* Dependency pinning + lockfile — gap 1.4.

These all live in `chl-memory/research/security_and_stability_gap_analysis.md`
and should NOT be merged into this audit; this audit is scoped to auth posture.

---

## Appendix A — files touched in this stream

* NEW: `backend/auth/__init__.py`
* NEW: `backend/auth/login_rate_limiter.py`
* NEW: `backend/auth/totp_2fa.py`
* NEW: `backend/tests/test_login_rate_limiter.py`
* NEW: `backend/tests/test_totp_2fa.py`
* MODIFIED: `backend/server.py` — wire in-memory rate limiter into `/api/auth/login`.

## Appendix B — verification

* `python -m pytest backend/tests/test_login_rate_limiter.py backend/tests/test_totp_2fa.py -v` -> 10/10 pass.
* `python scripts/preflight_server_smoke.py` -> "preflight OK: 167 routers + 18 crons + 5 health-registers verified".
* No new external dependencies. `cryptography==46.0.7` already in `requirements.txt`.
