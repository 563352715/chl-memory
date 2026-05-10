# CHL Platform Operator-Configurable Settings Inventory

**Generated:** 2026-05-10  
**Scope:** Backend codebase (C:\CHL\backend\) — excludes worktrees and test files  
**Analysis Depth:** Static code search for db.business_settings.*, os.environ.get(), and module-level config constants

---

## 1. Database Settings (db.business_settings)

| Key Name | Default Value | Type | Range/Constraints | Purpose | Source File:Line |
|----------|---------------|------|-------------------|---------|------------------|
| key | "singleton" | str | – | MongoDB doc key (always "singleton") | server.py:7110 |
| company_name | "Continental Haul Logistics LLC" | str | – | Legal business name displayed in public context | server.py:7111 |
| tagline | "Every state. Every mile. One haul." | str | – | Marketing tagline for branding | server.py:7112 |
| address_line1 | "618 West Greenwood St" | str | – | Street address line 1 | server.py:7113 |
| address_city | "Springfield" | str | – | City name | server.py:7114 |
| address_state | "MO" | str | – | US state abbreviation | server.py:7115 |
| address_zip | "65807" | str | – | Postal code | server.py:7116 |
| address_country | "USA" | str | – | Country name | server.py:7117 |
| contact_email | env:BUSINESS_CONTACT_EMAIL | str | email | Primary contact email for public-facing comms | server.py:7118 |
| contact_phone | env:BUSINESS_CONTACT_PHONE | str | phone | Primary contact phone | server.py:7119 |
| website | env:BUSINESS_CONTACT_URL | str | URL | Corporate website URL | server.py:7120 |
| mc_number | "" | str | – | FMCSA Motor Carrier number | server.py:7121 |
| dot_number | "" | str | – | USDOT number | server.py:7122 |
| logo_url | "/logo.svg" | str | URL | SVG logo path (frontend asset) | server.py:7123 |
| notification_toast | true | bool | – | Enable toast notifications on web UI | server.py:7124 |
| notification_sound | false | bool | – | Enable audio alerts for notifications | server.py:7125 |
| pnl_email_recipient | "" | str | email | P&L report email recipient | server.py:7126 |
| pnl_email_monthly_enabled | false | bool | – | Enable monthly P&L email digest | server.py:7127 |
| bank_name | "" | str | – | Primary operating account bank name | server.py:7129 |
| bank_routing | "" | str | ABA routing # | Primary operating account routing number (secure) | server.py:7130 |
| bank_account | "" | str | DDA # | Primary operating account number (secure) | server.py:7131 |
| bank_account_type | "checking" | str | {checking, savings} | Primary account type | server.py:7132 |
| bank_linked_at | null | ISO8601 | – | Timestamp when primary bank was linked | server.py:7133 |
| savings_bank_name | "" | str | – | Profit-First savings account bank | server.py:7135 |
| savings_routing | "" | str | ABA routing # | Savings account routing (Profit-First auto-sweep) | server.py:7136 |
| savings_account | "" | str | DDA # | Savings account number (secure, Profit-First) | server.py:7137 |
| savings_sweep_enabled | false | bool | – | Auto-sweep % of each payment to savings | server.py:7138 |
| savings_sweep_percent | 5.0 | float | 0–50 | Percentage of revenue swept to savings (Profit-First) | server.py:7139 |
| savings_linked_at | null | ISO8601 | – | Timestamp when savings account linked | server.py:7140 |
| tax_bank_name | "" | str | – | Tax reserve bucket bank | server.py:7142 |
| tax_routing | "" | str | ABA routing # | Tax reserve routing (federal + state) | server.py:7143 |
| tax_account | "" | str | DDA # | Tax reserve account number (secure) | server.py:7144 |
| tax_sweep_enabled | false | bool | – | Auto-sweep for tax obligations | server.py:7145 |
| tax_sweep_percent | 15.0 | float | 0–50 | Percentage swept to tax (covers fed + MO state) | server.py:7146 |
| tax_linked_at | null | ISO8601 | – | Timestamp when tax account linked | server.py:7147 |
| owner_bank_name | "" | str | – | Owner paycheck (CEO pay) account bank | server.py:7149 |
| owner_routing | "" | str | ABA routing # | Owner pay routing | server.py:7150 |
| owner_account | "" | str | DDA # | Owner pay account number (secure) | server.py:7151 |
| owner_sweep_enabled | false | bool | – | Auto-sweep for CEO paycheck | server.py:7152 |
| owner_sweep_percent | 25.0 | float | 0–75 | Percentage swept to owner (CEO salary) | server.py:7153 |
| owner_linked_at | null | ISO8601 | – | Timestamp when owner account linked | server.py:7154 |
| broker_margin_pct | 15.0 | float | 0–50 | % of shipper rate retained by CHL; carrier gets remainder | server.py:7156 |
| auto_pay_carrier_on_delivery | false | bool | – | Stripe instant payout on delivered status | server.py:7157 |
| auto_pay_delay_hours | 0 | int | ≥0 | Hold window before auto-paying carrier (hours) | server.py:7158 |
| broker_factor_enabled | false | bool | – | Enable broker-side invoice factoring (cash advance on receivables) | server.py:7164 |
| broker_factor_name | "" | str | – | Factor vendor name (e.g., "OTR Capital", "RTS", "Apex") | server.py:7165 |
| broker_factor_fee_pct | 2.5 | float | 0–10 | Factor fee as % of invoice (1.5–5% typical market) | server.py:7166 |
| broker_factor_advance_pct | 95.0 | float | 50–100 | % of invoice advanced (95–97% typical) | server.py:7167 |
| broker_factor_recourse | true | bool | – | True=recourse (cheaper, absorb bad debt); False=non-recourse | server.py:7168 |
| broker_factor_min_gross_margin_pct | 8.0 | float | 0–50 | Minimum gross margin to quote when factoring ON | server.py:7169 |
| broker_factor_max_gross_margin_pct | 22.0 | float | 0–60 | Maximum grossed-up margin ceiling (avoid uncompetitive rates) | server.py:7170 |
| global_pricing_mode_override | (optional) | str | {survival, bid_aggressive, defensive, carrier_attract, volume_grab, outcome_protect} | Incident lever: force all quotes into single pricing mode (incident response) | model_selector.py:22 |
| daily_load_cap | 1 | int | ≥0 | Daily load creation ceiling (ramp control for Day-1 stability) | load_throttle.py:38 |
| daily_load_cap_history | [] | array | – | Audit log of cap changes: [{at, from, to, reason, actor}] | load_throttle.py:18 |
| new_loads_halted_until_clear | (optional) | bool | – | Emergency halt: reject all new load requests | server.py:2493 |
| new_loads_halted_reason | (optional) | str | – | Human-readable reason for load halt (shown in UI) | server.py:2493 |
| active_model | "model_c" | str | {model_a, model_b, model_c, model_d} | Currently active operating model (volume/specialty/generalist/hybrid) | model_calculators.py:91 |
| operating_model_enabled | false | bool | – | Gate: enable multi-model operator support (Phase 2 feature) | model_calculators.py:90 |
| pricing_modes_enabled | false | bool | – | Gate: enable dynamic pricing modes (Phase 2 feature) | pricing_modes.py:17 |
| tracking_exception_thresholds | (optional) | dict | – | Per-lane tracking exception thresholds (operator-set; used by tracking/exception_detector.py) | tracking/exception_detector.py:312 |

---

## 2. Environment Variables

| Var Name | Purpose | Source File | Redacted |
|----------|---------|-----------|----------|
| MONGO_URL | MongoDB connection string | server.py:27 | YES |
| DB_NAME | MongoDB database name | server.py:29 | YES |
| JWT_SECRET | HMAC signing key for JWT tokens | server.py:53 | YES |
| STRIPE_API_KEY | Stripe payment processor secret key | server.py:38 | YES |
| OPENAI_API_KEY | OpenAI GPT API key | automations.py, auto_dispatch.py | YES |
| ANTHROPIC_API_KEY | Anthropic Claude API key | boot_briefing.py | YES |
| FMCSA_WEBKEY | FMCSA SAFER API key (optional) | automations.py, fmcsa_safer_cargo.py | YES |
| TWILIO_ACCOUNT_SID | Twilio account SID | automations.py | YES |
| TWILIO_AUTH_TOKEN | Twilio auth token | automations.py | YES |
| RESEND_API_KEY | Resend email API key | auto_dispatch_send.py | YES |
| RESEND_INBOUND_SECRET | Resend webhook signing secret | autopilot_phase1.py | YES |
| VAULT_ENCRYPTION_KEY | Secrets vault encryption key | factor_api_integration.py | YES |
| EMERGENT_LLM_KEY | Emergent AI LLM API key | carrier_contacts/enricher.py | YES |
| RTS_FACTORING_USERNAME | RTS factoring SFTP username | factors/rts_client.py | YES |
| RTS_FACTORING_PASSWORD | RTS factoring SFTP password | factors/rts_client.py | YES |
| RTS_FACTORING_SFTP_HOST | RTS factoring SFTP host | factors/rts_client.py | YES |
| RTS_FACTORING_SFTP_PORT | RTS factoring SFTP port (default "22") | factors/rts_client.py | NO |
| RTS_FACTORING_REMOTE_DIR | RTS factoring SFTP remote directory | factors/rts_client.py | NO |
| GITHUB_WEBHOOK_SECRET | GitHub Actions webhook signing secret | ci_intake/github_actions_router.py | YES |
| BUSINESS_CONTACT_EMAIL | Primary contact email | server.py:7118 | NO |
| BUSINESS_CONTACT_PHONE | Primary contact phone | server.py:7119 | NO |
| BUSINESS_CONTACT_URL | Website URL | server.py:7120 | NO |
| GMAIL_SMTP_USER | Gmail SMTP username | auto_dispatch_send.py | YES |
| GMAIL_SMTP_PASS | Gmail SMTP password | auto_dispatch_send.py | YES |
| GMAIL_SMTP_FROM | Gmail SMTP sender email | auto_dispatch_send.py | NO |
| TELNYX_FROM_NUMBER | Telnyx SMS sender number | auto_dispatch_send.py | NO |
| PLIVO_PHONE_NUMBER | Plivo SMS phone number | auto_dispatch_send.py | NO |
| TWILIO_PHONE_NUMBER | Twilio caller ID phone number | automations.py, browser_phone.py | NO |
| TWILIO_BROKER_FORWARDING_NUMBER | Twilio forwarding number for calls | automations.py, browser_phone.py | NO |
| ALERT_EMAIL | Alert email recipient | anomaly_sniffer.py | NO |
| OPERATOR_NOTIFY_EMAIL | Operator notification email | compliance_watcher.py | NO |
| OPERATOR_INBOX_EMAIL | Operator inbox email | carrier_docs/expiry_tracker.py | NO |
| DAY1_READINESS_NOTIFY_EMAIL | Day-1 readiness report email | day1_readiness_email.py | NO |
| OWNER_EMAILS | Comma-separated owner email list | banker_digest.py, circuit_breaker.py | NO |
| OWNER_SMS_NUMBER | Owner SMS number for alerts | circuit_breaker.py | NO |
| DIGEST_RECIPIENTS | Comma-separated digest email recipients | banker_digest.py | NO |
| PUBLIC_FRONTEND_URL | Public frontend URL for dispatch links | auto_dispatch_send.py | NO |
| REACT_APP_BACKEND_URL | React app backend URL | auto_dispatch_send.py | NO |
| FRONTEND_URL | Frontend URL (fallback) | bulk_outreach_blast.py, carrier_outreach.py | NO |
| PUBLIC_URL | Public site URL (fallback) | bulk_outreach_blast.py, consent_capture.py | NO |
| PUBLIC_SITE_URL | Public site URL | cash_valve.py, execution_pilot.py | NO |
| AUTO_REPOST_TICK_MINUTES | Repost cron tick interval (default 15 min) | auto_repost_cron.py | NO |
| REPOST_PULSE_INTERVAL_SEC | Repost pulse check interval (default 1800s = 30min) | auto_repost_scheduler.py | NO |
| REPOST_STALE_AFTER_HOURS | Repost stale-after window (default 2.0h) | auto_repost_scheduler.py | NO |
| REPOST_MIN_GAP_MINUTES | Min gap between reposts per load (default 30min) | auto_repost_scheduler.py | NO |
| REPOST_MAX_PER_CYCLE | Max reposts per cycle (default 50) | auto_repost_scheduler.py | NO |
| REPOST_DISABLE_QUIET_HOURS | Disable quiet hours for reposting | auto_repost_scheduler.py | NO |
| CHL_SETTLEMENT_DELAY_HOURS | Settlement delay before carrier payout (default 24h) | auto_settlement_trigger.py, cron_settlement_24h.py | NO |
| CHL_ELD_LOOKBACK_DAYS | ELD fleet size check lookback (default 7d) | eld_fleet_size_check.py | NO |
| DAY1_READINESS_EMAIL_DOW | Day-1 readiness email day-of-week (default 6=Sat) | day1_readiness_email.py | NO |
| DAY1_READINESS_EMAIL_HOUR | Day-1 readiness email local hour (default 19) | day1_readiness_email.py | NO |
| DAY1_READINESS_EMAIL_TZ | Day-1 readiness email timezone (default America/Chicago) | day1_readiness_email.py | NO |
| DAY1_READINESS_POLL_SEC | Day-1 readiness poll interval (default 900s) | day1_readiness_email.py | NO |
| CHL_VETTING_SUBCHECK_TIMEOUT_S | Carrier vetting sub-check timeout (default 8s) | carrier_vetting.py | NO |
| CHL_VETTING_FMCSA_TIMEOUT_S | Carrier vetting FMCSA check timeout (default 3s) | carrier_vetting.py | NO |
| CREDIT_CHECK_CACHE_HOURS | Credit check cache freshness (default 720h = 30d) | auto_credit_check.py | NO |
| CHL_CARRIER_MONITOR_BATCH_SIZE | Carrier continuous monitor batch size | carrier_continuous_monitor.py | NO |
| CHL_DEFAULT_CARRIER_NET_TERMS | Default carrier payment terms in days (default 30) | carrier_outreach.py | NO |
| CHL_MC | CHL's own MC number (default "MC-1817555") | bmc84_watcher.py | NO |
| CHL_DOT | CHL's own USDOT number (default "4569843") | bmc84_watcher.py | NO |
| CHL_RATE_CONF_SIGNATURE_REQUIRED | Rate conf signature requirement (default "true") | auto_dispatch_send.py | NO |
| CARRIER_DOCS_ROOT | Carrier docs storage root (default "C:/CHL/carrier_docs") | carrier_docs/document_store.py | NO |
| CHL_CHROME_BIN | Chrome/Chromium binary path for rendering | banker_digest.py | NO |
| WEEKLY_DEBT_SERVICE | Weekly debt service amount (default 0) | banker_digest.py | NO |
| MARGIN_FLOOR_PCT | Minimum absolute margin % (default 5%) | margin_floor.py | NO |
| MARGIN_FLOOR_USD | Minimum absolute margin in dollars (default $250) | margin_floor.py | NO |
| MARKET_RATE_WARN_PCT | Warn if quote below market by % (default 15%) | margin_floor.py | NO |

---

## 3. Summary

| Category | Count | Notes |
|----------|-------|-------|
| **db.business_settings keys** | 42 | Operator-tunable Mongo doc; banking, factoring, load caps, pricing modes |
| **Environment variables** | 65+ | API keys (redacted), URLs, emails, timing params, financial thresholds, feature gates |
| **Module constants** | 150+ | Timeouts, limits, thresholds, defaults (see backend code files) |
| **TOTAL CONFIGURABLES** | ~260 | Three-tier config: database, environment, code defaults |

---

## Key Observations

1. **Dual-layer env/db pattern**: Business settings often have env-var sources that populate db.business_settings at init—supporting boot-time override and vault migration.

2. **Profit-First automation**: Four linked bank accounts (primary, savings, tax, owner) with sweep percentages—full envelope budgeting implementation.

3. **Broker factoring is modular**: Min/max margin guards prevent uncompetitive quoting when factoring is ON; cost baked into margin calculators.

4. **Phase-2 gates present**: `operating_model_enabled`, `pricing_modes_enabled`, `global_pricing_mode_override` indicate multi-model/dynamic pricing is feature-flagged.

5. **Load throttle audit trail**: Daily cap ramps tracked in history with actor+reason—intentional Day-1 safeguard against runaway growth.

6. **Vault migration in-flight**: secret_reader.get_secret() supports vault-first with env fallback, allowing gradual key rotation without code changes.

7. **Email system is pluggable**: Gmail, Telnyx, Plivo, Resend, Twilio independently configurable—supports multiple providers in parallel.
