# Inbox Triage Backend Cron -- Design

**Stream:** Iter 145.x LL2 (sub-agent LL2)
**Date:** 2026-05-08 EOD-16-late
**Operator directive:** verbatim, *"Read emails every hour"*
**Status:** code shipped (uncommitted per task scope); doc committed to chl-memory.

---

## 1. Why this exists

The Claude-driven CronCreate version (job id `6a737be9`, recurring `13 * * * *`)
fires a fresh Claude session every hour to triage `db.inbound_emails`. The
problem: CronCreate jobs are **session-bound** -- they die when the parent
Claude session exits. A solo operator running multiple parallel Claude
contexts cannot rely on any one of them being alive at any given hour.

This stream productionizes the same triage capability as a **backend cron
loop** that is part of the FastAPI server process: always-on, survives
session restarts, restartable via NSSM/systemd, observable via
`/api/health/system`.

Trade-off: the backend version uses pure-Python regex/sender/subject
heuristics (no LLM). It is less capable than the Claude version on
ambiguous cases, but it reliably catches the URGENT classes that are
pattern-detectable: port-rejected, payment-failed, regulatory-deadline,
factor chargeback, customer complaint, legal threat, prompt injection.

The two layers are complementary:

* **Backend triage** (this stream) -- always-on safety net; writes
  `db.email_triage_log` + `db.urgent_alerts` rows + appends to
  `chl-memory/operations/email_inbox_review.md`.
* **Claude-driven triage** (CronCreate, eventual replacement) -- when an
  alerter cron fires SMS/email, OR when the next Claude session boots,
  Claude can do deep summarization + nuanced reasoning on the log file.

---

## 2. Module layout

```
backend/inbox_triage/
  __init__.py              -- package exports
  triage_classifier.py     -- pure regex/sender/subject classifier
  triage_scanner.py        -- async sweep + DB writes + log file append
  triage_router.py         -- /api/_inbox_triage/recent + /_health
backend/cron_scheduler.py  -- inbox_triage_loop registered (CRON 10)
backend/server.py          -- include_router(_build_triage_router(...))
```

Tests:
```
backend/tests/test_inbox_triage_classifier.py  -- 6 classifier tests
backend/tests/test_inbox_triage_scanner.py     -- 4 scanner tests
```

10/10 tests pass + IMAP poller regression (10 tests) green.

---

## 3. Categorization decision tree

The classifier returns `{category, verdict, confidence, reasoning}` for
each email. Order of operations:

### 3.1 CATEGORY (source / who sent it)

Resolved by **sender domain** first (highest confidence), then by
subject/body heuristics for SHIPPER_CARRIER_REPLY / SPAM_NEWSLETTER:

| Category                  | Trigger                                              | Conf  |
|---------------------------|------------------------------------------------------|-------|
| TELNYX                    | sender domain in {telnyx.com}                        | 0.95  |
| TWILIO                    | sender domain in {twilio.com, twilio.io, sendgrid.com}| 0.95 |
| STRIPE                    | sender domain in {stripe.com}                        | 0.95  |
| REGULATORY                | sender domain in {fmcsa.dot.gov, dot.gov, irs.gov, treasury.gov, sos.mo.gov, mo.gov, ...} | 0.95 |
| FACTOR                    | sender domain in {rtsfinancial.com, otrcapital.com, apexcapitalcorp.com, triumphpay.com, mercury.com, haulpay.com, axlehire.com} | 0.95 |
| VENDOR                    | sender domain in {aws.amazon.com, anthropic.com, openai.com, github.com, vercel.com, cloudflare.com, datfreight.com, truckstop.com, 123loadboard.com, zoho.com, plivo.com, ...} | 0.90 |
| SPAM_NEWSLETTER           | newsletter ESP domain (mailchimp/substack/...) OR `unsubscribe`/`view in browser` body | 0.55-0.80 |
| SHIPPER_CARRIER_REPLY     | "Re:/Fwd:" subject + freight-vocab body (`load`, `BOL`, `MC#`, `dispatch`)| 0.55-0.65 |
| UNKNOWN                   | fallback                                             | 0.30  |

**Sub-domain match:** `billing.twilio.com` rolls up to `twilio.com` via
the parent-domain walk in `_domain_matches()`.

### 3.2 VERDICT

| Verdict          | When                                                                    |
|------------------|-------------------------------------------------------------------------|
| URGENT           | port-rejected / payment-failed-unrecovered / regulatory-deadline / factor-chargeback / customer-complaint / legal-threat / prompt-injection |
| OPERATOR_REVIEW  | low-confidence default (under 0.5) OR shipper/carrier reply OR payment-failed-but-recovered |
| AUTO_ACK_OK      | rate-sheet inquiry, routine rate confirmation, vendor invoice ready (LOG only -- this stream does NOT auto-reply) |
| INFORMATIONAL    | newsletter, payment receipt, Telnyx sign-in link, subscription confirmation |

### 3.3 URGENT trigger patterns (representative samples)

* **Telnyx:** `\bport[\s\-]?(?:in|out)?\b[^\n]{0,64}\b(?:rejected|failed|denied|cancell?ed|expired)\b` -- matches "Port In Status Update: Rejected" AND "porting order for +1... has been rejected".
* **Payment failure:** `\bpayment\s+failed\b`, `\b(?:invoice|charge)\s+(?:failed|declined|unpaid)\b`, `\binsufficient\s+funds\b`, `\baccount\s+(?:past\s+due|delinquent|overdue)\b`. Downgraded to OPERATOR_REVIEW if a recovery pattern (`payment\s+(?:succeeded|received|recovered|processed)`) follows in the same body.
* **Regulatory:** `\bsuspension\s+(?:notice|warning)\b`, `\b(?:authority|registration)\s+(?:revoked|suspended|terminated)\b`, `\bbiennial\s+update\s+(?:overdue|past\s+due|required)\b`, `\bform\s+2290\s+(?:overdue|deadline)\b`.
* **Factor:** `\bcharge\-?back\b`, `\bclaw\-?back\b`, `\binvoice\s+(?:rejected|disputed|kicked\s+back)\b`.
* **Complaint:** `\bcomplaint\b`, `\brefund\s+(?:request|demand|required)\b`, `\bdamaged\s+(?:freight|cargo|shipment)\b`, `\bcargo\s+claim\b`.
* **Legal threat (any sender):** `\bdemand\s+letter\b`, `\bcease\s+and\s+desist\b`, `\b(?:lawsuit|legal\s+action)\b`, `\bsubpoena\b`, `\battorney\s+(?:will|is|has)\s+(?:contact|file)\b`.
* **Prompt injection:** `\bignore\s+(?:all\s+)?previous\s+instructions?\b`, `\bdisregard\s+(?:all\s+)?(?:prior|previous)\s+instructions?\b`, `\bexfiltrate\b`. Treated as URGENT + the directive itself is **never** acted on (this scanner does not execute body content).

### 3.4 Defensive low-confidence downgrade

If `verdict != URGENT` and `confidence < LOW_CONFIDENCE_FLOOR (0.5)`, the
verdict is forced to `OPERATOR_REVIEW`. Rationale: false-positive
AUTO_ACK_OK on a low-confidence email could auto-reply something it
shouldn't. URGENT is **never** downgraded -- false-positive an URGENT
is annoying; false-negative is a missed regulatory deadline.

---

## 4. Scanner cadence + window

| Param                     | Value     | Reason                                              |
|---------------------------|-----------|-----------------------------------------------------|
| `interval_sec`            | 3600      | Operator-mandated "every hour"                      |
| `initial_delay_sec`       | 180       | Let IMAP poller arm first so triage has fresh data  |
| `since_minutes` (window)  | 75        | Hourly cron with 15-min overlap; defends a late tick |
| `BATCH_CAP`               | (none)    | The scanner reads N rows from the window; no cap is needed because the IMAP poller already caps ingestion at 50/folder/cycle (so worst-case 6 folders * 12 cycles/hour * 50 = 3600 rows/hour, which is far below any DB stress level). |

The **75-min window** with **60-min interval** means a row can be seen
at most twice (overlap region). The composite-id (`message_id`) upsert
on `db.email_triage_log` makes this idempotent: re-runs over the same
window cannot duplicate.

---

## 5. Persistence schema

### 5.1 `db.email_triage_log`

One row per (message_id, classifier_version). Composite-id =
`message_id` (so the same email never gets two log rows even if the
classifier version is bumped -- the upsert is `$setOnInsert` so newer
runs preserve the original classification). Future-proof: a
`scanner_version` field is recorded so an out-of-band reclassifier can
target old rows.

```jsonc
{
  "_id": "<message_id>",
  "message_id": "<message_id>",
  "email_id": "<inbound_emails._id>",
  "from_addr": "noreply@telnyx.com",
  "subject": "Port In Status: Rejected",
  "fetched_at": "2026-05-08T...",
  "source_folder": "INBOX",
  "category": "TELNYX",
  "verdict": "URGENT",
  "confidence": 1.0,
  "reasoning": ["sender_domain_match:telnyx=telnyx.com",
                "telnyx_urgent_pattern:..."],
  "summary": "from noreply@telnyx.com | subj: Port In Status: Rejected",
  "scanner_version": "0.1.0",
  "scanned_at": "2026-05-08T..."
}
```

### 5.2 `db.urgent_alerts`

Only written for `verdict == URGENT`. Composite-id = `message_id`
(same idempotency story). Consumed by a future alerter cron that
fires SMS/email/push (out of scope this stream).

```jsonc
{
  "_id": "<message_id>",
  "message_id": "<message_id>",
  "email_id": "...",
  "from_addr": "...",
  "subject": "...",
  "category": "TELNYX",
  "verdict": "URGENT",
  "confidence": 1.0,
  "reasoning": [...],
  "summary": "...",
  "scanner_version": "0.1.0",
  "created_at": "...",
  "dispatched_at": null,
  "dispatch_status": "pending"
}
```

### 5.3 `chl-memory/operations/email_inbox_review.md`

Markdown file appended to per scan run. Format:

```markdown
## Triage run -- 2026-05-08T20:13:00+00:00

**Window**: last 75 min (since 2026-05-08T18:58:00+00:00). **Seen**: 7. **Classified**: 7. **Urgent**: 1. **Skipped (dup)**: 0.
**By category**: TELNYX=2, STRIPE=1, VENDOR=2, SPAM_NEWSLETTER=2

### URGENT (1)
- [TELNYX] conf=1.00 from=port-status@telnyx.com subject='Port In Status: Rejected' message_id=<...>
    - sender_domain_match:telnyx=telnyx.com
    - telnyx_urgent_pattern:...

### OPERATOR_REVIEW (3)
- ...

### AUTO_ACK_OK (1)
- ...

### INFORMATIONAL (2)
- ...
```

This file is checked into chl-memory git so PM Claude + future Claude
sessions can read it. Operator can read it directly for human review.

---

## 6. Integration points

### 6.1 Upstream: IMAP poller

`backend/inbound_email/imap_poller.py` runs every 5 min, ingesting from
INBOX + Newsletter + Promotions + ... (per `CHL_IMAP_POLL_FOLDERS` env
var). Each row stamps `fetched_at` (ISO-8601 UTC) which the triage
scanner queries via `{"fetched_at": {"$gte": since_iso}}`.

### 6.2 Sibling: stream LL1 (Renewals notifier)

LL1 dispatches `db.renewal_reminders` rows produced by KK1's renewals
scanner. The triage scanner is its own producer-only loop -- it does NOT
share any code with LL1. A future merge could route URGENT
`db.urgent_alerts` rows through the same dispatcher, but that's out of
scope this stream.

### 6.3 Downstream: future alerter cron

A future cron (not in this stream) will scan `db.urgent_alerts` where
`dispatched_at == null` and fire SMS / email / Web push. The composite-id
+ pending status fields are designed for that consumer.

---

## 7. Defense-in-depth gates

1. **`should_skip_tick()`** -- the cron loop checks this each tick, AND
   the scanner checks again internally. Returns `skipped_kill_switch`
   summary on either fire.
2. **`is_read_only()`** -- the scanner checks this independently. Returns
   `skipped_read_only` summary; no writes occur.
3. **Per-row try/except** -- a single broken email row never aborts the
   scan; the error is recorded in `summary["errors"]` and the loop
   continues.
4. **Composite-id upsert** -- duplicate writes are impossible by design.
5. **Sub-floor confidence downgrade** -- defensive default to
   OPERATOR_REVIEW so a low-confidence AUTO_ACK never auto-replies
   something it shouldn't.

---

## 8. Health surface

| Endpoint                              | Auth          | Returns                                     |
|---------------------------------------|---------------|---------------------------------------------|
| `GET /api/_inbox_triage/_health`      | require_broker| `triage_scanner.health()` snapshot          |
| `GET /api/_inbox_triage/recent?hours=24` | require_owner | last N hours of triage log, grouped by verdict |
| `inbox_triage_cron` health check      | n/a           | registered with `health_monitor`; surfaces in `/api/health/system` |

Healthy when `loop_running=True` AND `last_tick_status` is in
`{ok, success, never_run, skipped_read_only, skipped_kill_switch}`.

---

## 9. CronCreate revoke steps

After this backend cron is shipped + verified in production, the
operator should revoke the in-memory CronCreate Claude job to avoid
double-triage:

```
CronList                          # confirm 6a737be9 still exists
CronDelete cron_id="6a737be9"     # revoke
```

This is the dev-engineer's call (not the sub-agent's). Sequence:

1. Confirm backend cron has fired at least 2x successfully in production
   (verify via `GET /api/_inbox_triage/_health`).
2. Confirm at least one URGENT classification has flowed through
   `db.urgent_alerts` (the future alerter consumes these).
3. Confirm `chl-memory/operations/email_inbox_review.md` has been
   appended to + git-committed by the operator's regular ceremony.
4. Then run `CronDelete cron_id="6a737be9"`.

---

## 10. Limits + future work

The pure-Python classifier will miss:

* Sentiment-laden complaints that don't use the explicit complaint
  vocabulary ("I am not happy with this delivery" instead of "I am
  filing a complaint").
* Multi-language emails (the regex is English-only; Spanish carrier
  replies will fall to UNKNOWN/OPERATOR_REVIEW).
* Indirect threats ("this is going to be a problem for our partnership"
  doesn't match the legal-threat patterns).
* Highly novel scams that don't use any keyword in the existing patterns.

These are explicit non-goals of this stream. The Claude-driven layer
covers them.

### 10.1 Highest-priority follow-ups

1. **LLM-augmented classifier for ambiguous cases.** When confidence is
   in the 0.4-0.6 band AND verdict is OPERATOR_REVIEW, optionally pass
   to gpt-4o for a tiebreaker. Gated by `integrity.budget_caps` so the
   cost stays bounded. Estimated <$0.30/day at current ingestion rates
   (~50-200 emails/day, ~10% in the ambiguous band).

2. **Web push notifications** for URGENT alerts. Operator's mobile
   browser registers a service worker; the alerter cron POSTs to the
   web-push endpoint. No carrier dependency, instant delivery, free.

3. **Mobile alerts via the `urgent_alerts` consumer.** Once the alerter
   cron exists, route URGENT to operator's phone via SMS (Telnyx) +
   email backup (Zoho). Operator-confirmed escalation tier: SMS first,
   email second, web-push for browsers that have it.

---

## 11. Acceptance summary

* Files created: 4 (1 init, 1 classifier, 1 scanner, 1 router) + 2
  test files + 1 cron block in cron_scheduler.py + 1 router include in
  server.py.
* Tests: 10/10 pass (6 classifier + 4 scanner). IMAP poller regression
  10/10 pass.
* Preflight: passes (`173 routers + 18 crons + 5 health-registers
  verified`).
* No LLM dependency. ASCII-only. Type hints throughout.
* No outbound email sent (this scanner WRITES rows; future alerter
  consumes).
* CronCreate Claude job `6a737be9` is **not** revoked by this stream;
  parent dev-engineer revokes after backend version is verified live.
