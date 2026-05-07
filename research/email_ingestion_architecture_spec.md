# Email Ingestion Architecture Spec — Operator Inbox as Platform Input Surface

> **Source:** PM Claude (`@pm-lead`) drafted 2026-05-07 EOD-3 per @dev-engineer task brief. Designs the email-reading capability that closes the **4th input surface** to the iter 145.1 self-healing dispatcher (alongside web frontend errors, backend health/SLA/throttle, and data freshness). Operator stated 2026-05-07: *"I will need AI embedded into it to monitor and write code while the system is being used."* — email is the first input surface the platform doesn't have today.
> **Status:** Forward-looking architecture spec (not a binding agenda). Full agenda when operator authorizes iter 143.x kickoff.
> **Pre-existing slot:** `# ZOHO_IMAP_PASSWORD=...` already commented in `backend/.env` per dev's note — env-var slot exists; integration doesn't.

---

## 1. Why — what observation surface email gives us

The operator's inbox is the **canonical async-event surface** for the freight brokerage business. Many state changes that matter operationally arrive there FIRST and are invisible to every other CHL surface today. Each email below = a state change the platform should respond to:

| Email source | Email content | State change it represents | Platform response (post-this-spec) |
|---|---|---|---|
| **FMCSA** (`*@fmcsa.dot.gov`) | "Your authority MC-1817555 is granted/revoked/suspended/pending" | Operating-authority state flip | Auto-flip `business_settings.authority_active`; gate `auto_dispatch.try_auto_accept` accordingly; unblocks iter 141.2 DAT subscription if granted |
| **Mercury Bank** (`*@mercury.com`) | "Deposit of $2,850 received from Triumph Factor on Invoice #INV-12345" | Factor wire confirmed → invoice paid | Auto-flip `db.invoices.{id}.status: "paid"`; close the AR row; update `aged_AR` rollup |
| **Triumph / Apex / TBS Factor** | "Wire instructions confirmed; funds released" / "Wire failed: BSA flag" | Payment-pipeline event | Closes payment loop OR triggers payment-pipeline anomaly per iter 145.1 dispatcher |
| **Shipper portals** (Coyote, CHRW, etc.) | "Your password expires in 7 days" / "Account locked" | Operator-touch event before it becomes a missed-load event | Auto-renew where supported; queue operator-triage for manual reset |
| **Plivo / Twilio billing** | "Your $20 credit was consumed" / "Brand registration approved" / "Campaign rejected" | SMS-vendor state change | iter 141.3 unblock-detector (campaign approved!); cost-monitoring alert at v1 spend ceiling |
| **10DLC / TCR** | "Brand registration outcome" / "Campaign vetting outcome" | Compliance-pipeline state | Direct iter 141.3 stage 1c gate event |
| **DAT / Truckstop billing + load board** | "Subscription renewing on X" / "Lane match found for your search" | External-load-board event | Cost-monitoring; lane-match → seed RFQ inbound flow |
| **State / federal regulatory** (`*.gov` outside FMCSA) | "New 10DLC rule taking effect" / "Hazmat compliance update" | Regulatory state change | Operator-triage queue (high-importance, low-volume, can't auto-action without operator review) |
| **Customer comms** (shipper email replies, carrier email replies) | "Acknowledged your rate-con" / "Need detention authorization" | Per-load communication event | Append to `db.loads.{id}.comms_log`; if BOL/POD attached → route to `load_completion_workflow` |
| **Vendor notifications** (Resend, Anthropic API, OpenAI, Stripe, etc.) | "Service degradation notice" / "API key expiring" / "Bill due" | Cost / API-availability state | Cost-monitoring; API-key-expiring → operator-DM; degradation → throttle response |

**Pattern recognition:** every email above is an observation that ALREADY happens; the operator sees it; today the operator is the parser+classifier+actor for each one. **Email ingestion makes the platform the parser+classifier; for HIGH-confidence routes the platform becomes the actor; for LOW-confidence the operator stays in the loop but with a triage queue instead of an inbox-as-task-list.**

**Non-goals (explicitly out of scope at v1):**
- Outbound email sending — already covered by Resend integration (`automations.py`); this spec is INBOUND only.
- Calendar/meeting parsing — operator's inbox is mostly transactional, not meeting-heavy at v1.
- Slack / Discord / SMS-as-channel — separate input surfaces; SMS is already partially covered via Twilio + future Plivo webhooks.
- Multi-account ingestion — single Zoho mailbox at v1 (operator's `dispatch@continentalhaul.com`); multi-account when CHL adds team members.

---

## 2. Inbound Flow Architecture

### IMAP poll cron

- **Provider:** Zoho Mail (operator's existing mailbox).
- **Host / port:** `imap.zoho.com` / `993` / TLS.
- **Auth:** username = `dispatch@continentalhaul.com`; password = `ZOHO_IMAP_PASSWORD` env var (already commented-slot exists in `backend/.env`; operator generates app-specific password via Zoho admin console + populates).
- **Cron schedule:** every 5 minutes (300s). Throttle-respecting (skip on not-GREEN per existing `lane_scoring_cron` pattern). `@track_sla("inbound_email_poll", target_ms=30_000)` — 5-min poll cycle should complete in <30s for a typical 5–20-message batch.
- **Health-check registration:** `inbound_email_poller` module appears in `/api/health/system` (extends today's 14-module count to 15+); reports `loop_running: true` + `last_run_success_at` + `messages_ingested_last_run`.

### Fetch + dedup logic

```python
async def poll_inbox():
    cursor = await db.inbound_email_cursor.find_one({"_id": "zoho_dispatch"}) or {"last_uid": 0}
    
    with imaplib.IMAP4_SSL("imap.zoho.com", 993) as conn:
        conn.login(USER, ZOHO_IMAP_PASSWORD)
        conn.select("INBOX")
        # Fetch UIDs > last_uid (Zoho UIDs are monotonic per folder)
        _, data = conn.uid("SEARCH", None, f"UID {cursor['last_uid']+1}:*")
        uids = data[0].split()
        
        for uid in uids[:50]:  # batch cap: 50 per cycle to bound work
            _, fetched = conn.uid("FETCH", uid, "(RFC822)")
            raw = fetched[0][1]
            msg = email.message_from_bytes(raw)
            
            # Dedup via Message-ID (defense against UID-rollback / re-ingest)
            msg_id = msg.get("Message-ID")
            if await db.inbound_emails.find_one({"message_id": msg_id}):
                continue
            
            await ingest_one(msg, uid, raw)
        
        if uids:
            await db.inbound_email_cursor.update_one(
                {"_id": "zoho_dispatch"},
                {"$set": {"last_uid": int(uids[-1]), "last_poll_at": now_utc()}},
                upsert=True,
            )
```

**Idempotent re-sync on cursor reset:** if `db.inbound_email_cursor` is dropped or reset to `last_uid: 0`, the poller re-fetches everything; dedup-via-Message-ID prevents duplicate `db.inbound_emails` rows. Operator-runnable recovery path: `db.inbound_email_cursor.deleteOne({"_id": "zoho_dispatch"})` → next cron tick re-baselines.

### `db.inbound_emails` schema

```
{
  _id: ObjectId,
  message_id: str (RFC 2822 Message-ID; unique index),
  uid: int (Zoho IMAP UID at fetch time),
  fetched_at: datetime,
  from_addr: str (parsed sender email),
  from_display: str (display name),
  to_addrs: [str],
  cc_addrs: [str],
  subject: str,
  date_header: datetime (sender's claimed send time),
  body_text: str (sanitized; HTML stripped via bleach or beautifulsoup),
  body_html_ref: str | None (path to attachment-disk if HTML preserved for rendering),
  attachments: [
    {filename: str, content_type: str, size_bytes: int, disk_path: str, sha256: str}
  ],
  headers_full: dict (all headers, useful for downstream classification),
  classified_at: datetime | None (set by Tier 1/2 classifier),
  lane: str | None,                       # set by classifier
  classifier_confidence: float | None,
  classifier_tier: "tier1" | "tier2" | "unclassified" | None,
  action_taken: str | None,                # set by action-surface handler
  requires_operator_review: bool | None,
}
```

Indexes:
- Unique on `message_id` (dedup gate)
- Compound on `(from_addr, fetched_at desc)` (operator inbox-by-sender view)
- Compound on `(lane, classified_at desc)` (digest queries)
- TTL: NONE (see retention policy below — managed by archive cron, not Mongo TTL)

### Retention + attachment-quota policy

- **Mongo retention:** keep full row (headers + body_text) for **90 days**. After 90 days, archive to `db.inbound_emails_archive` collection with body stripped (headers + classification metadata only); body_text archived to `D:/CHL_backups/inbound_emails_archive/{YYYY-MM}/{message_id}.json.gz`.
- **Attachment retention:** disk path lifecycle:
  - Active: `C:/CHL/data/inbound_emails/{YYYY-MM-DD}/{message_id}/{filename}` (90 days)
  - Archived: `D:/CHL_backups/inbound_email_attachments/{YYYY-MM}/{message_id}.zip` (1 year)
  - Deleted: after 1 year archive
- **Quota:** active disk capped at **1 GB** (rotate-oldest-first if exceeded; archive cron runs nightly at 02:00 local). Operator-alert at 80%, hard-stop intake at 95% (don't fill the disk).
- **Attachment scrubbing:** PDF / DOCX / images preserved; executables (`.exe`, `.bat`, `.ps1`, `.scr`) silently dropped + flagged in `db.inbound_emails.attachments[].dropped: true` — never persisted to disk. Defense against malicious attachments at the sink.

---

## 3. Classifier Layer

Given the dimensionality of operator inbox content (10+ source categories from §1, dozens of subject-line patterns each), use a **2-tier classifier**:

### Tier 1 — rule-based regex / from-domain matching

**Cheap, deterministic, audit-trail-friendly.** Catches the high-confidence routes:

| Lane | Tier 1 rule | Confidence |
|---|---|---|
| `fmcsa_authority` | `from:.*@fmcsa\.dot\.gov` AND `subject:(authority|operating)` | 0.95 |
| `mercury_deposit` | `from:.*@mercury\.com` AND `subject:.*deposit` | 0.95 |
| `factor_wire_confirmation` | `from:.*@(triumphpay|apexcap|tbsfactoring)\.com` AND `subject:.*(wire|payment|funding)` | 0.90 |
| `plivo_billing` | `from:.*@plivo\.com` AND `subject:.*(invoice|billing|credit)` | 0.95 |
| `plivo_brand_campaign` | `from:.*@plivo\.com` AND `subject:.*(brand|campaign|10dlc)` | 0.90 |
| `dat_billing` | `from:.*@dat\.com` AND `subject:.*(invoice|subscription|renew)` | 0.90 |
| `shipper_portal_credential` | `from:.*@(coyote|chrobinson|jbhunt)\.com` AND `subject:.*(password|credential|account)` | 0.85 |
| `regulatory_dot_gov` | `from:.*\.gov$` AND NOT `fmcsa.dot.gov` (caught above) | 0.70 (LOW-confidence; requires_operator_review=true) |
| `customer_load_reply` | `from:.*` AND `references:` matches a recently-sent rate-con Message-ID | 0.85 |
| `vendor_billing_generic` | `from:.*@(stripe|anthropic|openai|aws|azure)\.com` AND `subject:.*(bill|invoice|usage)` | 0.85 |

Implementation: declarative rule list in `backend/email_classifier_rules.py`. Operator-tunable; new lanes added by appending rule dicts. Each rule has `{pattern, lane, confidence, requires_operator_review_default}`.

### Tier 2 — LLM-based classification (Tier 1 fallthrough)

For emails that fail all Tier 1 rules: dispatch to **gpt-4o via the existing `EMERGENT_LLM_KEY` AsyncOpenAI wrapper** (already used by `load_completion_workflow.py` for vision OCR + `boot_briefing.py` for text generation; reuse, don't add new SDKs).

**Output schema:**
```json
{
  "lane": "fmcsa_authority" | "mercury_deposit" | ... | "unclassified",
  "confidence": 0.0,
  "suggested_action": "auto_action" | "operator_review" | "ignore",
  "requires_operator_review": true | false,
  "reasoning": "1-2 sentence why this lane / why this confidence"
}
```

**Prompt template** (~500 tokens):
```
You classify operator emails for a freight brokerage. Given the email below, output JSON with these fields: {lane, confidence, suggested_action, requires_operator_review, reasoning}.

Lanes available: [fmcsa_authority, mercury_deposit, factor_wire_confirmation, plivo_billing, plivo_brand_campaign, dat_billing, shipper_portal_credential, regulatory_other, customer_load_reply, vendor_billing_generic, unclassified]

If no lane fits, output "unclassified" with confidence < 0.5 and requires_operator_review: true.

Email subject: {subject}
Email from: {from_addr}
Email body (first 2000 chars): {body_text[:2000]}
```

**Budget cap:** 500 Tier-2 calls per day (operator-tunable). At gpt-4o pricing (~$0.005 per call at 500-token input + 100-token output) = **~$2.50/day** max Tier-2 cost. On cap exceeded: subsequent emails fall through to `unclassified` lane (Tier 1 rules still apply; Tier 2 paused until next day OR operator-override).

**Cost monitoring:** `db.inbound_email_classifier_budget` daily counter; exposed at `/api/_inbound_emails/budget` (broker-JWT-protected). Operator-alert at 80% (~$2/day spent); hard-stop at 100% with logs.

### Output: classification metadata written to `db.inbound_emails`

Tier 1 hits: `classifier_tier: "tier1"`, `classifier_confidence` from rule, `lane` from rule, `classified_at: now`.
Tier 2 hits: same fields populated from LLM JSON output.
Unclassified: `lane: "unclassified"`, `classifier_tier: "tier2"` (LLM was tried), `requires_operator_review: true`.

---

## 4. Action Surfaces

### `db.inbound_email_events` collection

Each classified email becomes a structured event:
```
{
  event_id: ObjectId,
  source_email_id: ObjectId (FK to db.inbound_emails),
  message_id: str (denormalized for queries),
  lane: str,
  confidence: float,
  classifier_tier: "tier1" | "tier2",
  detected_at: datetime,
  state_changes_proposed: [
    {target: "business_settings.authority_active", old_value: ..., new_value: ..., applied: bool}
  ],
  action_taken: str | None,
  action_status: "pending" | "auto_executed" | "operator_review_pending" | "operator_approved" | "operator_rejected" | "failed",
  trust_gate_class: "low" | "med" | "high",
  reviewed_by: str | None,
  reviewed_at: datetime | None,
}
```

### Endpoint: `/api/_inbound_emails/digest?hours=24&lane=...`

- Mirrors `client_error_digest_router` shape (already shipped per CHL `d24b979`).
- `require_broker` JWT auth.
- Returns: `{total, by_lane: {...}, by_action_status: {...}, requires_operator_review_count: N, recent_events: [...]}`.
- Operator hits this from dashboard tile; default view = last 24h, all lanes, with "needs operator triage" count prominent.

### Downstream automation hooks (v1 list)

For HIGH-confidence Tier 1 lanes, trigger downstream automation post-classification:

| Lane | v1 hook | What it does |
|---|---|---|
| `fmcsa_authority` (`granted`) | `business_settings.authority_active = true` + `db.iters.{141.2}.gate_event = "fmcsa_authority_granted"` + operator-DM | Unblocks iter 141.2 DAT subscription |
| `fmcsa_authority` (`revoked|suspended`) | Operator-CRITICAL alert + auto-pause `auto_dispatch.try_auto_accept` (gate to `false`) | Stops dispatch when authority lost |
| `mercury_deposit` | Match deposit amount + memo to `db.invoices.{id}` open AR row → flip `status: "paid"` + `paid_at: now`; update `aged_AR` rollup | Closes payment loop |
| `factor_wire_confirmation` (success) | Match wire to `db.factor_dispatches.{id}` → flip `status: "wire_confirmed"` | Closes factor-funding loop |
| `factor_wire_confirmation` (failure / BSA flag) | Operator-CRITICAL alert + create `db.failure_events` row (`failure_mode: "factor_wire_rejected"`); trigger iter 145.1 anomaly dispatcher | Surfaces payment-pipeline anomaly |
| `plivo_billing` | Append spend to `db.cost_tracking.plivo` daily roll-up; alert if monthly spend trajectory exceeds operator's $20/wk threshold | Cost monitoring |
| `plivo_brand_campaign` (approved) | Operator-CRITICAL alert (iter 141.3 unblock detector) + `db.iters.{141.3}.gate_event = "plivo_campaign_approved"` | Unblocks iter 141.3 stage 1c |
| `dat_billing` | Append spend to `db.cost_tracking.dat`; alert if subscription auto-renewing within 7 days | Cost monitoring + renewal-decision surface |
| `shipper_portal_credential` | Queue operator-triage with one-click reset link extracted from email body | Operator-touch-elimination opportunity |
| `customer_load_reply` (with attachment) | If attachment looks like BOL/POD (filename match + content-type) → route to `load_completion_workflow.process_uploaded_doc(load_id, attachment)` | Auto-ingest of BOL/POD via email reply |

### Operator triage view

For LOW-confidence (Tier 2 below 0.7 confidence) / Tier-2-budget-exhausted unclassified / `requires_operator_review: true` lanes (e.g., `regulatory_other`):
- Render in operator dashboard "Inbox Triage" tile (frontend addition in stage 1d — see §7)
- Operator can: assign-to-lane (creates rule for future), defer, dismiss, escalate
- Tracked in `db.inbound_email_events.action_status: "operator_review_pending"` until resolved

---

## 5. Self-Healing Integration

Several inbound email lanes ARE detection events for the iter 145.1 anomaly-routing dispatcher (Layer 1 Detection). Wire as follows:

| Lane | Anomaly-dispatcher category | Why |
|---|---|---|
| `factor_wire_confirmation` (failure / BSA flag) | **BUSINESS-LOGIC** | Payment-pipeline failure mode; agent should diagnose root cause (BSA flag = compliance issue; wire failed = banking issue) + propose patch (operator alert template tuning, or extra defensive checks pre-wire) |
| `fmcsa_authority` (revoked / suspended) | **BUSINESS-LOGIC** | Authority loss = operational halt; agent diagnoses (unpaid renewal? compliance violation?) + proposes recovery sequence |
| `plivo_billing` (spend trajectory >threshold) | **PERF** (cost-perf) | Cost over-run = dispatcher diagnoses which campaign / message volume is driving spend |
| `vendor_billing_generic` (API key expiring) | **INTEGRATION** | API key expiring → agent diagnoses which env-var + dispatches operator-DM with renewal instructions |
| `regulatory_other` | **No dispatch** (operator-review only) | Regulatory changes need human judgment; not auto-diagnosable |
| `unclassified` (rate of unclassified > threshold) | **DATA** (classifier drift) | If unclassified rate exceeds 30% of weekly volume → classifier drift; agent diagnoses (new email sources? phrasing changes?) + proposes new Tier 1 rules |

**Bidirectional cross-ref to `phase_6_self_healing_roadmap.md` §1 Detection Layer:** add `inbound_email_lanes` to the "Existing primitives" list once iter 143.x ships.

---

## 6. Trust-Gate Matrix Update

For each automation hook from §4, classify per the iter 145.1 trust-gate matrix:

| Hook | Trust-gate class (v1) | Trust-gate class (v2, post-30-day proven accuracy) | Rationale |
|---|---|---|---|
| `fmcsa_authority granted → authority_active=true` | **MED** (operator-DM) | **LOW** (auto-execute) | High-impact + high-confidence Tier 1 rule; operator wants visibility for first 30 days; promote to LOW after no false-positives |
| `fmcsa_authority revoked → pause auto_dispatch` | **HIGH** (operator-approval) | **MED** (operator-DM) | Pausing dispatch = revenue-affecting; even high-confidence false-positive would be costly. Operator-approve always at v1 |
| `mercury_deposit → invoice.status: paid` | **MED** | **LOW** | Financial state change; operator-DM during confidence-build period. Promote to LOW once amount-matching logic proven accurate |
| `factor_wire_confirmation (success)` | **MED** | **LOW** | Same pattern as Mercury |
| `factor_wire_confirmation (failure / BSA)` | **MED** + dispatcher | **MED** + dispatcher | Anomaly dispatch is the primary action; operator-DM is the human-in-loop surface. Stays MED indefinitely (high-stakes financial) |
| `plivo_billing spend update` | **LOW** (auto-execute) | **LOW** | Counter increment; no reversibility risk |
| `plivo_brand_campaign approved → iter 141.3 unblock` | **MED** | **MED** | Iter unblock is operator-decision; auto-execute would skip operator's "ready to start the next stage?" check |
| `dat_billing` | **LOW** | **LOW** | Counter increment + alert; no state change |
| `shipper_portal_credential → operator-triage queue` | **MED** | **LOW** | Queue addition is reversible; operator-DM during confidence-build to prevent triage-spam from false-positives |
| `customer_load_reply with BOL/POD attachment → load_completion_workflow` | **MED** | **MED** | Document-routing affects load lifecycle state; operator-DM stays at v1 + v2 (compliance + audit-trail concern; auto-execute risks misrouting POD to wrong load) |

**Most hooks start MED for first 30 days post-ship** to build classifier-confidence track record per dev's framing. Promotion to LOW happens via explicit operator decision after reviewing per-lane false-positive rates.

---

## 7. Iter Mapping + 4-Stage Outline

**Proposed iter: 143.1 — Email Ingestion Foundation** (or 143.x post Phase-5-POD-invoicing depending on scope sequencing).

### Stage 1a: IMAP Poll Cron + Raw Ingestion + Message-ID Dedup (~3.5h dev, ~10 min operator)

**Goal:** Stand up the IMAP poller + raw email ingestion + dedup. No classification yet — just the substrate.

**What operator does (~10 min):**
1. Generate Zoho app-specific password via Zoho Mail Admin Console → populate `ZOHO_IMAP_PASSWORD` in `backend/.env` (~5 min).
2. Review retention + quota policies in `memory/handoff_iter_143_1_stage_1a.md` (~5 min).
3. Sign-off.

**What dev does (~3.5h):**
1. `backend/email_poller.py` per §2 architecture; throttle-respecting; `@track_sla("inbound_email_poll", target_ms=30_000)`.
2. `db.inbound_emails` schema + indexes (unique on `message_id`).
3. `db.inbound_email_cursor` collection for monotonic UID tracking.
4. Attachment-disk write logic with quota enforcement + executable scrubbing.
5. Health-check registration.
6. Archive cron stub (nightly 02:00 local; full implementation deferred to a later iter).

**Smoke battery (5 tests):**
1. **REAL-DATA:** Connect to Zoho IMAP with valid app-password → returns mailbox metadata
2. Fetch 5 emails → 5 rows in `db.inbound_emails`, all with `message_id` populated
3. Re-poll same UIDs (cursor reset) → 0 duplicate rows (Message-ID dedup gate works)
4. Attachment with `.exe` extension → silently dropped, `attachments[].dropped: true`
5. Quota at 95% → poll returns early with operator-alert; intake hard-stops

**Estimated time:** 4h (3.5h dev + 0.5h operator)
**Handoff doc:** `memory/handoff_iter_143_1_stage_1a.md`

### Stage 1b: Tier 1 Rule-Based Classifier + First 5 High-Confidence Lanes (~3h dev, ~10 min operator)

**Goal:** Stand up Tier 1 classifier + ship 5 high-confidence lanes (fmcsa_authority, mercury_deposit, plivo_billing, plivo_brand_campaign, dat_billing).

**What operator does (~10 min):**
1. Review proposed Tier 1 rules in `memory/handoff_iter_143_1_stage_1b.md` — particularly the `from:` domain patterns (operator confirms which Mercury / factor / DAT addresses are real)
2. Sign-off

**What dev does (~3h):**
1. `backend/email_classifier_rules.py` — declarative rule list with the 10 Tier 1 rules from §3
2. `backend/email_classifier.py` — `classify_tier1(email_doc) -> {lane, confidence, requires_operator_review}` function
3. Wire into poll cron: classify on ingest; write `lane` + `classifier_*` fields to `db.inbound_emails`
4. `db.inbound_email_events` collection + index
5. SLA target `email_classify_tier1` with `target_ms=200`

**Smoke battery (6 tests, 3 real-data):**
1. **REAL-DATA:** Synthetic FMCSA email → classified `fmcsa_authority` confidence 0.95
2. **REAL-DATA:** Real Mercury deposit email (operator forwards a sample) → classified `mercury_deposit`
3. Generic email (no rule match) → `lane: null`, `classifier_tier: null` (Tier 1 fallthrough)
4. Multiple-rule-match edge case (e.g., FMCSA email mentioning DAT) → highest-confidence rule wins, deterministic
5. **REAL-DATA:** Backfill last 30 days of inbox → classification stats reasonable (≥60% Tier 1 hit rate expected)
6. Rule-list tunable: append new rule, restart cron, new email matches new rule

**Estimated time:** 3.5h
**Handoff doc:** `memory/handoff_iter_143_1_stage_1b.md`

### Stage 1c: Tier 2 LLM Classifier + Budget Cap + Tier 1 Fallthrough (~4h dev, ~10 min operator)

**Goal:** Stand up Tier 2 LLM classifier for Tier 1 fallthrough. Budget cap with hard-stop.

**What operator does (~10 min):**
1. Review Tier 2 prompt template + budget cap (~$2.50/day default) in `memory/handoff_iter_143_1_stage_1c.md`
2. Sign-off on budget; operator may want lower at v1 (e.g., 200/day = $1/day)

**What dev does (~4h):**
1. `backend/email_classifier_llm.py` — `classify_tier2(email_doc) -> {lane, confidence, ...}` using existing `AsyncOpenAI` wrapper from `load_completion_workflow.py` / `boot_briefing.py`
2. Daily budget counter `db.inbound_email_classifier_budget` with operator-alert at 80%, hard-stop at 100%
3. Wire into classifier flow: Tier 1 → fall-through → Tier 2 → fall-through → `unclassified`
4. Endpoint `/api/_inbound_emails/budget` (broker-JWT-protected)
5. SLA target `email_classify_tier2` with `target_ms=3_000` (LLM call latency)

**Smoke battery (5 tests):**
1. Tier-1-fallthrough email → Tier 2 dispatched → JSON response parsed correctly
2. Tier 2 returns invalid JSON → graceful fallback to `unclassified` (don't crash)
3. Budget at 99% → next call dispatched + counter increments to 100%; subsequent call falls through to `unclassified` with log
4. Budget reset at midnight local → counter resets to 0
5. Endpoint returns valid JSON budget snapshot

**Estimated time:** 4.5h
**Handoff doc:** `memory/handoff_iter_143_1_stage_1c.md`

### Stage 1d: Action-Surface Wiring + First 3 v1 Automation Hooks (~5h dev, ~15 min operator)

**Goal:** Stand up `/api/_inbound_emails/digest` + dashboard tile + 3 v1 automation hooks (fmcsa_authority + mercury_deposit + plivo_billing).

**What operator does (~15 min):**
1. Review the 3 v1 hooks in `memory/handoff_iter_143_1_stage_1d.md` — confirm trust-gate placement (MED for fmcsa-granted + mercury_deposit; LOW for plivo_billing)
2. Operator-DM channel confirmed (Plivo SMS post-141.3-cutover OR email pre-cutover)
3. Visual dashboard tile sign-off after dev demos
4. Sign-off

**What dev does (~5h):**
1. `db.inbound_email_events` event-write logic — every classified email creates an event with state_changes_proposed
2. Trust-gate routing per §6 matrix — v1 hooks default to MED operator-DM
3. `/api/_inbound_emails/digest?hours=24&lane=...` endpoint mirroring `client_error_digest_router` shape
4. Frontend dashboard tile: "Inbox Triage" + per-lane counters + recent events with one-click approve/reject for MED-tier
5. Hook 1: `fmcsa_authority granted` → MED-DM operator with proposed `authority_active=true` change; on approval flip flag
6. Hook 2: `mercury_deposit` → MED-DM operator with proposed invoice match (amount + memo to open AR row); on approval flip status
7. Hook 3: `plivo_billing` → LOW auto-execute (counter update); operator-alert if threshold exceeded
8. Outcome-feedback hook (per iter 145.1 Layer 5): each automated action writes to `db.evaluator_outcomes` for future classifier-accuracy re-tuning

**Smoke battery (7 tests, 2 real-data):**
1. **REAL-DATA:** Synthetic FMCSA-authority-granted email → event created, MED-DM fires to operator, no auto-state-change yet
2. **REAL-DATA:** Operator approves via endpoint → state flip applied, `db.business_settings.authority_active: true`
3. Mercury deposit email matching open AR row by amount + memo → MED-DM fires with proposed match
4. Operator rejects → no state change, `action_status: "operator_rejected"`
5. Plivo billing email → LOW auto-execute, counter incremented, no operator-DM
6. Digest endpoint returns valid JSON with per-lane counts + recent events
7. Dashboard tile renders with empty event list (cold-start case) without crash

**Estimated time:** 5.5h
**Handoff doc:** `memory/handoff_iter_143_1_stage_1d.md`

### Iter 143.1 close ceremony

- [ ] Verify smoke totals across stages 1a-1d (**23 tests** total: 5+6+5+7)
- [ ] Write `memory/handoff_iter_143_1_close.md`
- [ ] Run `scripts/backup_to_d.ps1 -IterId "iter_143_1_close"`
- [ ] Update `current_status.md` with email-ingestion live state
- [ ] Operator visual confirmation: real email arrives → poller → classifier → event → digest tile shows it
- [ ] Decide on iter 143.2 (additional v1 hooks: factor_wire + shipper_portal + customer_load_reply) or other priority

**Iter 143.1 milestone met when:** real email from operator's actual inbox flows end-to-end through poll → classify → event → digest tile, with at least 1 real automated action executed (Plivo billing counter update is the cleanest LOW-tier first action).

---

## Aggregate Stats (iter 143.1 proposal)

| Stage | Operator hands-on | Dev hours | Smoke tests | Real-data tests | North-star (R/O/S) | Parallel-shippable? |
|---|---|---|---|---|---|---|
| 1a | 10 min | 3.5h | 5 | 1 | M/H/M | YES (no external blocker beyond Zoho password) |
| 1b | 10 min | 3h | 6 | 3 | H/H/M | YES (post-1a) |
| 1c | 10 min | 4h | 5 | 0 | H/H/M | YES (post-1b; uses existing AsyncOpenAI wrapper) |
| 1d | 15 min | 5h | 7 | 2 | H/H/H | YES (post-1c) |
| **Totals** | **~45 min** | **~15.5h** | **23** | **6** | — | — |

---

## Cross-references

- **`chl-memory/research/phase_6_self_healing_roadmap.md`** (commit `7d658cc`) — Layer 1 Detection lists existing observation surfaces; this spec adds email as the 4th surface (alongside web/backend/data). Bidirectional cross-ref: when iter 143.x ships, add `inbound_email_lanes` to Layer 1 "Existing primitives" list.
- **`chl-memory/research/iter_142_1_stages_1c_1f_refresh.md`** (commit `0f9ccca`) — `db.evaluator_outcomes` schema pattern referenced for classifier-accuracy outcome tracking.
- **`chl-memory/capability_inventory.md`** — existing primitives. Reference for: AsyncOpenAI wrapper pattern (load_completion_workflow.py uses gpt-4o vision; boot_briefing.py uses Anthropic; same `AsyncOpenAI` initialization), failure_detectors pattern (event-write + alert flow), throttle-respecting cron pattern (lane_scoring_cron).
- **CHL backend `automations.py`** — Resend integration; pattern for outbound email (this spec is INBOUND only; no overlap risk).
- **CHL backend `boot_briefing.py`** — Anthropic 30-second narrative; example of throttle-respecting LLM call with fallback path. Mirror for Tier 2 LLM classifier graceful-degradation.
- **CHL backend `load_completion_workflow.py`** — gpt-4o vision OCR; example of AsyncOpenAI wrapper usage. Reuse pattern for Tier 2 text classification.
- **`chl-memory/research/iter_141_3_agenda_draft.md`** — Plivo migration; iter 141.3 stage 1c gate event = `plivo_brand_campaign approved` email arrival. Concrete v1 hook example.
- **`chl-memory/THROTTLE_PLAYBOOK.md`** — throttle integration pattern; email poller respects throttle (skip on not-GREEN).

---

## Sleeper-Flag Items for Operator (pre-kickoff)

1. **Zoho app-specific password is the single auth gate.** Operator must generate via Zoho Mail Admin Console + populate `ZOHO_IMAP_PASSWORD` in `backend/.env` BEFORE stage 1a kickoff. Standard Zoho user password won't work (Zoho enforces app-specific for IMAP).
2. **Tier 2 LLM budget cap is operator-tunable.** Default $2.50/day = 500 calls. Operator may want lower at v1 (e.g., 200 calls = $1/day) — easy to dial down if classifier-spend feels too high; harder to dial up if operator hits the cap and wants more.
3. **Forwarding emails from team / vendor accounts to operator's Zoho inbox.** If operator wants the platform to see emails sent to (e.g.) `accounting@continentalhaul.com` or `dispatch.team@`, those need to be forwarded to `dispatch@continentalhaul.com` (the polled mailbox). Multi-account ingestion is post-v1.
4. **POD/BOL via email reply** (last hook in §4) is a high-leverage pattern but **assumes carrier replies preserve a `References:` or `In-Reply-To:` header pointing to the rate-con Message-ID.** Some carrier email clients strip these headers; in that case fall back to subject-line load-id parsing. Worth pre-validating with one or two carriers' actual replies.
5. **Self-healing-loop guardrail.** Email-ingestion classifier IS itself a system that can drift (new email patterns, classifier-accuracy degradation). The iter 145.1 dispatcher should be wired to monitor unclassified-rate as a Detection-Layer signal (per §5 table). Without this guardrail, classifier-drift could go silently for weeks before operator notices.

---

**Status:** ✅ SPEC COMPLETE. Operator/dev review before iter 143.x opens. Frame: email is the **4th input surface** to the iter 145.1 self-healing dispatcher; with this spec shipped, the platform's input surfaces are complete (web frontend errors / backend health-SLA-throttle / data carriers-freshness / email).

**Source:** Drafted by @pm-lead 2026-05-07 EOD-3 per @dev-engineer task brief. North-star pillars: operator's "AI-embedded monitor + write code while running" + "platform should observe + respond" directives. Forward-looking; specific Zoho IMAP behavior + AsyncOpenAI wrapper signatures will adjust at dev-time per actual API responses.
