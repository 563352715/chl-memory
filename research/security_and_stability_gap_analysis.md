# Security + Stability Gap Analysis

**Status:** Captured 2026-05-08 EOD-13 per operator request. Inventory of what we're NOT doing today + prioritized roadmap. FINAL-PHASE-tier work; some items are pre-revenue tradeoffs to make explicit, others should ship before public-facing.

**Operator framing 2026-05-08 EOD-13 (verbatim):**

> "What can we do to keep this platform safe that we are not doing now? In terms of securing technology, stability of the platform etc...?"

---

## Gap inventory (12 categories)

### Already addressed (✅)
- **Backup + redundancy** — EOD-11 shipped Tier 1 NSSM + Tier 2A in-process hot-spare + Tier 3 Mongo replica + S3/B2 nightly + weekly offsite. Code dormant pending hardware (5-10 TB SSD ordered).
- **Damage mitigation foundation** — circuit breakers per vendor, daily LLM budget caps, read-only mode toggle, cron pause master-switch, audit trail with 90d prune.
- **Self-healing observability** — anomaly dispatcher + context bundler + sub-agent dispatcher (propose-only) + outcome telemetry.
- **Carrier-comm cred guardrail** — sub_agent_dispatcher trust-gate matrix forbids auto-merge against Plivo/Twilio/outreach/voice/text/tollfree/credential/secret/.token paths.

### NOT yet addressed (gaps below, priority-ordered)

---

## Priority 1 — Pre-revenue must-fix (do BEFORE first paid load)

### 1.1 Secrets management — high leverage, low cost
**Today:** Secrets live in `backend/.env` (gitignored) + NSSM `AppEnvironmentExtra`. Anyone with Windows admin on the operator's machine can read them. No rotation policy. No vault.

**Risk:** A single compromised admin account exfiltrates Twilio + Plivo + Mercury + Stripe + OpenAI/Anthropic + FMCSA WebKey + Zoho IMAP password — all of operator's third-party accounts.

**Fix (~half-day):**
- Move secrets to a Windows DPAPI-encrypted store (or HashiCorp Vault if going multi-machine later). DPAPI encrypts per-user; only the operator's Windows account can decrypt.
- `set_env_var.ps1` already exists; extend it to write DPAPI-encrypted blobs that the backend reads via a small `secret_loader.py`.
- Document a 90-day rotation cadence for all third-party API keys; calendar reminders.
- Audit: scan git history for accidentally-committed secrets via `git log -p --all | grep -iE 'sk-|key=|token=|password='`. (Run once.)

**Effort:** ~4-6 hours. **Risk reduction:** Major.

### 1.2 Mongo + carrier_docs encryption at rest
**Today:** Mongo data files at the default Windows path are unencrypted. The 480K carrier records, factor submissions, patch_proposals, audit trails — all sit in cleartext on disk. Same for `C:/CHL/carrier_docs/{dot}/...` PDFs (signed COIs, W9s with last-4 SSN, MC Authority certs).

**Risk:** Lost laptop = full-DB exfiltration. Stolen drive = same.

**Fix (~1-2 hours):**
- Enable **Windows BitLocker** on the C: drive AND the new 5-10 TB SSD when it lands. BitLocker is free with Win10/11 Pro; encrypts the entire volume; requires nothing in code.
- For per-row PII encryption (tax_id_last_4, etc.) — add `cryptography.Fernet` field-level encryption in `extraction_store.py`; deferred to Phase-2 since it requires schema migration.

**Effort:** ~30 minutes for BitLocker; 4-6 hours for per-field encryption. **Risk reduction:** Major (BitLocker alone covers the lost-machine case).

### 1.3 CVE scanning automation
**Today:** No automated scanning of installed Python wheels or npm packages for known CVEs.

**Risk:** A new CVE in `cryptography`, `motor`, `fastapi`, or any of ~150 transitive deps could ship the platform with a known-exploitable vulnerability for weeks before we notice.

**Fix (~2 hours):**
- Daily cron: `pip-audit` against `backend/requirements.txt` + `npm audit` against `frontend/package.json`. Output to a JSON file; surface HIGH/CRITICAL findings via the existing observability triad's anomaly dispatcher (auto-route to operator alert).
- Add as `scripts/cve_scan_daily.py` + Task Scheduler nightly job.

**Effort:** ~2 hours. **Risk reduction:** Significant (catches known-exploitable issues).

### 1.4 Dependency lock + supply-chain integrity
**Today:** Likely have `requirements.txt` (Python) + `package.json` + `yarn.lock` (frontend). Audit how strict.

**Risk:** A compromised npm package or PyPI package (typosquat, hijacked maintainer) ships malicious code into production.

**Fix:**
- Pin all top-level Python deps to exact versions in `requirements.txt` (no `>=` ranges).
- Use `pip-compile` (pip-tools) to generate a fully-pinned `requirements.lock` from a slim `requirements.in`.
- Frontend: yarn.lock should already be checked in. Verify.
- Optional: enable npm/pip integrity-hash verification.

**Effort:** ~2-3 hours.

### 1.5 Auth hardening
**Today:** `require_broker` dependency in FastAPI gates write paths. Don't know strength of token format / session expiry / brute-force protection without auditing.

**Risk:** If session tokens are long-lived + non-rotating, a stolen token = persistent backend access.

**Fix (~3-4 hours, partial audit needed first):**
- Audit current `require_broker` impl + token TTL + cookie attributes (HttpOnly, Secure, SameSite=Strict).
- Add 2FA via TOTP (operator-only; small library — pyotp + a QR code at first login).
- Rate-limit failed login attempts (5 per 5 minutes; lockout at 10).
- Token rotation on every privileged action.

**Effort:** ~half-day to audit + ship.

---

## Priority 2 — Pre-public-facing (do BEFORE exposing to the internet)

### 2.1 TLS / HTTPS everywhere
**Today:** Backend on `127.0.0.1:8001` HTTP (local-only, OK for now). When going public-facing for shippers/carriers, plain HTTP is unacceptable.

**Fix:** Cloudflare Tunnel (free, 1-day setup) OR nginx + Let's Encrypt OR Caddy (single binary, auto-TLS). Cloudflare Tunnel is the lowest-touch option — also gets you DDoS protection + WAF for free.

**Effort:** ~half-day setup + DNS config.

### 2.2 CORS lockdown
**Today:** FastAPI CORS likely permissive in dev (the existing setup may have `allow_origins=["*"]`).

**Fix:** Lock to specific origins (chl.continentalhaul.com or whatever the production domain is). Never `*` in production.

**Effort:** ~30 minutes after audit.

### 2.3 Rate limiting / DDoS protection
**Today:** No rate limits on any endpoint. A single bad actor could hammer `/api/carriers/{dot}/contact_enrichment/refresh` (which fires gpt-4o-vision) and burn $$$$ in LLM spend.

**Fix:** SlowAPI or fastapi-limiter — per-IP and per-user rate limits on expensive endpoints. The 60s cooldown on the doc-extraction refresh endpoint (EOD-10 verifier-fix) is the right pattern; extend to all expensive endpoints. Cloudflare tunnel handles edge DDoS.

**Effort:** ~3-4 hours.

### 2.4 Input validation / SSRF protection
**Today:** FastAPI + Pydantic gives basic type validation. But `carrier_contacts/enricher.py` does HTTP scraping via `httpx`; if a malicious domain ends up in the carrier's data, scraping that URL could trigger SSRF (server-side request forgery) hits to internal IPs (169.254.169.254 cloud metadata, etc.).

**Fix:** Allow-list outbound scraping domains. Block private IP ranges (RFC 1918, link-local, loopback) at the httpx call site. ~50 lines of helper.

**Effort:** ~2 hours.

### 2.5 File upload sandboxing
**Today:** Carrier doc upload accepts PDFs/images at `/api/carriers/{dot}/documents/{type}/upload`. Filename sanitized + size capped at 25MB. But: the file content is sent to gpt-4o-vision (EOD-10 doc_extractor) — what if it's a malformed PDF that crashes the parser, or a polyglot file?

**Fix:**
- Validate file content matches advertised MIME (don't trust Content-Type header alone). Use `python-magic` libmagic bindings.
- Run uploaded files through a virus scanner if going multi-tenant. ClamAV is free and Windows-compatible.
- Quarantine on upload until classifier signs off.

**Effort:** ~3-4 hours.

---

## Priority 3 — Operational stability (ship anytime, mostly low-effort)

### 3.1 Staging environment
**Today:** Single CHL-Backend production. Code goes commit -> push -> manual restart of prod.

**Fix:** Run a SECOND CHL-Backend NSSM service on port 8003 as `CHL-Backend-Staging` with a separate `CHL_DB_NAME=test_database_staging`. Smoke-test new commits there before flipping prod. (Tier 2 Pattern A hot-spare on port 8002 from EOD-11 already provides 90% of this; adding staging is incremental.)

**Effort:** ~2 hours after EOD-11 hot-spare lights up.

### 3.2 CI/CD pipeline
**Today:** No GitHub Actions / Jenkins / etc. Manual: dev commits, dev pushes, dev tells operator to restart.

**Fix:** GitHub Actions workflow on push: run pytest, run `pip-audit`, run `npm audit`, run pylint/mypy, fail the push if any HIGH/CRITICAL finding. ~50-line YAML.

**Effort:** ~2-3 hours.

### 3.3 Wire-up the existing kill-switches (PHASE-2 deferred from EOD-11)
**Today:** `CHL_READ_ONLY` and `CHL_CRONS_PAUSED` env flags exist but are referenced by ZERO crons / write endpoints. They do nothing in production.

**Fix:** Sweep all 17+ crons + add `if should_skip_tick(): continue` at top of tick. Apply `@require_writable` to all write endpoints (~50-100 endpoints). This is the explicitly-deferred work from the EOD-11 verifier.

**Effort:** ~half-day. Captured in BUILD_STATUS_REPORT.

### 3.4 Synthetic 21-stage end-to-end test (FINAL PHASE prep)
Already captured at `chl-memory/research/final_phase_acceptance_test_framework.md`. The single best stability investment we can make: a pytest that runs an entire load lifecycle and asserts DB state at every stage. Catches regressions across iters that individual-batch verifiers miss.

### 3.5 Operator manual override surface (FINAL PHASE prep)
Pipeline Walkthrough tab — already captured. Lets operator step into any pipeline stage when automation gets stuck.

---

## Priority 4 — Mature / scaled-state (don't build until revenue justifies)

### 4.1 SOC2-style audit logging
PII access logs, admin action logs, retention policies. Required for enterprise deals. Cost: ~1-2 weeks of work + tooling.

### 4.2 SIEM / threat detection
Splunk / ELK / Datadog. Real-time alerting on anomalous traffic patterns. Cost: ~$50-200/month + 1 week setup.

### 4.3 SOC2 / ISO 27001 cert
Multi-month effort + ~$10-30K in external audit. Don't pursue until enterprise customers ask.

### 4.4 Incident response runbooks
Documented procedures for: data breach, credential exposure, ransomware, vendor outage, etc. Some captured in `tier3_redundancy_setup.md` for the data-loss case.

---

## Summary recommendations

**Do now (this month, ~2 dev-days total):**
1. BitLocker on C: drive + new SSD when it lands (30 min)
2. CVE scanning daily cron (2 hours)
3. Pin Python deps via pip-compile (2 hours)
4. Audit `require_broker` impl + add 2FA (4 hours)
5. DPAPI-encrypted secrets store (4 hours)
6. Wire up existing kill-switches into crons + write endpoints (Phase-2 PR; half-day)

**Do before going public-facing (~3-4 dev-days):**
7. Cloudflare Tunnel + TLS
8. CORS lockdown + rate-limiting
9. SSRF allow-list on scraping
10. File upload content-type validation

**Final-phase prep (already captured in other docs):**
- 21-stage synthetic walkthrough test
- Pipeline Walkthrough operator surface
- Whole-build code audit (Component A from `platform_integrity_and_failover_plan.md`)

**Don't build yet (until revenue / enterprise customers):**
- SOC2-style audit logging
- SIEM tooling
- Multi-month security cert work

---

## Why this doc exists

Operator-mandated 2026-05-08 EOD-13. Without persistent capture, "what about security" gets re-asked every few sessions and answered fresh each time, missing items each round. This doc is the canonical inventory. Update it as items ship; mark them ✅ + move to "addressed" section.

For the agent picking this up: read alongside `platform_integrity_and_failover_plan.md` (operational redundancy) and `final_phase_acceptance_test_framework.md` (acceptance testing). Together those three docs cover the full pre-launch hardening checklist.
