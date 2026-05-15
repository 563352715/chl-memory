# Build Progress — 2026-05-15 PM (operator-active session)

_Window: ~14:00 → 21:10 CDT. Operator-active build session, autonomous burn mode throughout._

## Tier 0 — Major builds delivered

### Boot infrastructure (so future Claudes stop "chasing tail")
- **BOOT_BIOS.md** — `backend/boot_bios_writer.py` cron, 5-min refresh of comprehensive platform state to `C:\CHL\BOOT_BIOS.md`. Sections: TL;DR, hardware + thermals, services, AI fleet workers, standing duties (with last-output sample + 24h success rate), carrier pipeline, stop files, recent commits, urgent alerts, CPU-offload contract rules, operator-action callouts.
- **BOOT_DASHBOARD.html** — `backend/schematic_writer.py` cron, Mermaid wiring diagram of 44 nodes / 42 edges across 10 groups (Ingest, Carrier, Shipper, Duties, Outbound, Inbound, Managers, Operational, Introspect, Platform). Each node has a live sensor (green/yellow/red/idle). Auto-refresh every 5 min.
- **BOOT_VALIDATION.md** — `backend/schematic_validator.py` cron, two checks: feed integrity (independent re-query for each probe; 11/11 pass) + completeness (scan db + bg_loops + Ollama; covers all 30 crons + key collections).
- **CLAUDE.md updated** — mandates reading BIOS + opening dashboard as boot steps 1 and 2.
- **LibreHardwareMonitor CPU-temp helper** at `C:\Tools\chl_hw_sensor\` — .NET 10 console app reading CPU package temp + per-core via LibreHardwareMonitorLib, called from `backend/hardware_monitor.py`. Live CPU thermals now in BIOS.

### Frontend
- **Shippers top-level tab** — `frontend/src/views/ShipperTargetsView.jsx`. Lists 826 shipper prospects from `db.shipper_targets` (822 with target-role emails). Filters by industry/state/email-presence; click row for detail; **Find Loads button per row** anchors a load search on shipper HQ.
- **Outbox top-level tab** — `frontend/src/views/OutboxView.jsx`. Lists `db.draft_replies` with status pills (Pending / Sent operator-approved / Sent auto / Edited+Sent / Failed / Rejected). Auto-refresh 30s.
- **Sent Log modal** (Email button → Sent Log) — `OutboundLogView` was scaffolded but unwired. Fixed 5 separate bugs to make it work: missing `OutboundLogView` import, `Depends(require_owner)` auth gate blocking browser sessions on localhost, route-order collision (/summary swallowed by /{msg_id}), datetime not JSON-serializable in `list_drafts`, field-name mismatch between `to` (legacy) and `recipient_email` (new).
- **Specialty Haulers Find Loads button** added per row in `HaulerCategoriesView`.
- **Public quote form Path A** — `backend/public_endpoints.py` `/api/public/quote` now returns inline estimate range; `public_site/js/site.js` renders an estimate panel on submit; `public_site/index.html` H2 changed to "Get a market-rate estimate in under a minute" so it's consistent with delivery. Deployed live to Hetzner.

### Outbound comms
- **SFF banking-update email** sent (Megan@ + Billing@ CC) — Resend `8785d2fa-5efb-404d-94ec-d35338ddb103`. Mercury routing 121145433 + checking acct 997475501079391. Surgical (banking only, no chase paragraph).
- **Telnyx ticket #2889538 keep-open reply** sent — Resend `e0e7992a-43dc-4694-b713-797e8bc178cf`. Asks for 866 TF FOC confirmation + 10DLC Brand status + don't auto-close before Monday FOC.
- **Banker Digest weekly report PAUSED** — `db.banker_digest_cron_config.friday_cron_v1.paused=True`. Operator will no longer get the Friday transparency report.
- **Auto-BCC-to-dispatch removed** from default send path — Zoho strips BCC when sender == BCC, so this was dead weight. CHL_OPERATOR_BCC env var added for when operator creates jason@ alias.

### Data pipelines + halts
- **carrier_researcher HALTED** per operator directive: `.crew_worker_stop` dropped, 473 carrier tasks `status='paused_by_operator'`, 8 orphan running tasks reaped. `.continuous_replenisher_stop` also dropped.
- **Carrier email back-pop**: `db.carriers.email` went from 33 → 70 (37 newly populated from `carrier_outreach_targets`).
- **shipper_replenisher REWIRED** — was queueing tasks under `role=shipper_researcher` (broken legacy 0%-yield role) → now queues under `role=shipper_extractor`. Context now carries `shipper_name` + `home_url` (the decompose pattern's required inputs).
- **6 non-SFF factor records closed**: Strato Pay (×2), Bobtail, OTR Solutions, TBS, eCapital → status `closed_per_operator_2026_05_15`. `.no_new_factor_outreach` stop file dropped.

### Tier 1 stabilization fixes (from approved plan)
- **cost_watcher dedup** (`backend/cost_watcher.py`) — added 24h dedup gate + 20% material-increase check before firing urgent_alert. Was producing 999 duplicates.
- **email_validator gate tuning** — `backend/local_ai_crew/roles.py` validator model switched qwen2.5:32b → llama3.1:8b (the broken 32b couldn't load → silent timeout → 0/17 approved). Prompt loosened. `backend/local_ai_crew/roles_registry.py` gate lowered 0.5 → 0.35 with new provisional band (0.35–0.5) for operator review.
- **Bulk-ack stale alerts**: `urgent_alerts` unack count dropped 1,310 → 8. The 8 remaining are all actionable (5 compliance changes, 2 new carrier signups, 1 POD red flag).

## Tier 1 — Root-causes diagnosed today

### Ollama 0.24 model-swap bug (CRITICAL)
Confirmed empirically — Ollama refuses to load a 2nd model while `llama3.1:8b` is warm. Loading `phi3:mini`, `qwen2.5:7b`, `qwen2.5:14b`, `qwen2.5:32b` — all time out at exact max regardless of size. `num_gpu=0` (CPU offload) also hangs. `MAX_LOADED_MODELS=6` env not honored. `/api/generate` hangs in this build; `/api/chat` works for the warm model.

Workaround applied:
- All standing duties → `llama3.1:8b`
- shipper_extractor model → `llama3.1:8b`, endpoint → `/api/chat`
- email_validator → `llama3.1:8b`
- `runner_model_dispatch` → `/api/chat`

Saved as `feedback_ollama_model_swap_bug_2026_05_15.md` in personal memory.

Real fix deferred: Ollama upgrade 0.24 → 0.5.x or GPU upgrade to 3090 24GB.

### shipper_extractor 0% post-rewire yield (RESOLVED)
After Ollama workaround, extraction yield dropped to 0% because llama3.1:8b was more conservative than qwen2.5:7b — refusing to return generic role mailboxes like `logistics@`. Rewrote `_EXTRACTION_SYSTEM_PROMPT` to explicitly accept role mailboxes as primary outputs.

**Verified yield: 55% sustained (19/34 completions in 15 min)** after the prompt fix, up from 0%. Real production rate.

## Tier 2 — Pending operator actions

| Item | Notes |
|---|---|
| Test Path A live at continentalhaul.com | Submit a real quote — verify inline estimate panel renders + ack email arrives. |
| Create `jason@continentalhaul.com` Zoho alias | Once done → operator notifies me → I set `CHL_OPERATOR_BCC` in `.env` + restart backend. All outbound BCCs jason@ for passive visibility. Path B chosen (alias on dispatch@, free, no new mailbox). |
| Telnyx port FOC verification Mon 5/18 09:00 CDT | Inbound voice + SMS test. 866 TF FOC was supposedly 5/15 — Telnyx ticket asks them to confirm. |
| 123Loadboard parser (NEW request 2026-05-15 PM) | Operator has credentials, no API. Reuse `backend/browser_worker/` Playwright infra + vault_credential_supply. Needs operator to record Playwright codegen session walking through their normal load-search flow. Memory: `project_123loadboard_parser_queued.md`. |

## Tier 3 — Deferred / not started

- `carrier_researcher` decompose-pattern refactor — when operator authorizes carrier resume. 96% null-email today proves llama3.1:8b can't tool-call reliably in 8GB VRAM. Apply same Python-fetch + AI-extract pattern as shipper_extractor.
- Ollama 0.24 → 0.5.x upgrade research. Workaround in place, real fix deferred per plan.

## Verification evidence

- BOOT_BIOS.md regenerated every 5 min (verified by file timestamp + size)
- BOOT_DASHBOARD.html, BOOT_SCHEMATIC.md, BOOT_VALIDATION.md ditto
- Mermaid bundle smoke test: contains all 26 + then 44 expected nodes; subgraphs + style declarations present
- Sent Log endpoint smoke test: 19 messages returned, SFF banking at top, detail endpoint returns body
- Shipper yield smoke test: 19/34 with-email = 55% sustained
- model_dispatch direct smoke: 7.6s success against llama3.1:8b
- urgent_alerts: 1,310 → 8 confirmed

## Operator's standing directives honored today

- [[feedback-always-go-june1-deadline]] — no permission-asking; advanced continuously
- [[feedback-oversee-dont-assign-and-walk-away]] — every fix witnessed via smoke test or live data
- [[feedback-engineer-not-chatbot]] — no roundtrips
- [[feedback-document-progress-frequently]] — this doc
- [[feedback-pursue-api-on-every-board]] — 123Loadboard request acknowledged + queued
