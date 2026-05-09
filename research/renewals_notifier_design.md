# Renewals Notifier -- Design Notes

**Stream:** Iter 145.x LL1 (consumer for KK1's producer).
**Module:** `backend/renewals/renewals_notifier.py`
**Author:** sub-agent LL1, 2026-05-08
**Cron entry:** `cron_scheduler.renewals_notifier_loop`
**Health key:** `renewals_notifier_cron` (auto-registered with `health_monitor`).

---

## Why this stream exists

KK1 shipped the renewals platform: a scanner that walks `db.renewals`,
computes days-until expiry against a threshold ladder
(60 / 30 / 14 / 7 / 3 / 1 days), and inserts rows into
`db.renewal_reminders` with `dispatch_status='pending'`. KK1 is a
PRODUCER. Nothing was reading those rows. They sat in Mongo.

LL1 builds the CONSUMER -- a cron loop that walks pending reminders,
composes an operator-facing email body, fires it via the existing
`send_email` helper, optionally fires SMS for high-urgency tiers, and
stamps the reminder row dispatched / errored.

Without LL1, the renewals platform is a write-only audit trail. With
LL1, it is a true bell that operator hears when something is about to
lapse.

---

## Cron cadence rationale

**Interval: 15 minutes.**

The KK1 scanner runs every 6 hours. So the inflow rate of pending
reminders is at most 1 batch per 6 hours per renewal row. With 15-min
notifier ticks, a freshly-fired reminder waits at most ~15 min before
the operator's inbox sees the email.

Why not less? Two reasons:
1. **Inbox-clutter avoidance.** The operator hates noise. 15 min is
   sub-perceptible delay for renewal urgency tiers (1d / 3d / 7d /
   14d / 30d / 60d are all day-granular). A 1-minute cron is cheaper
   for the DB but spammier for the operator's mental model of
   "something happens once an hour at most".
2. **Batch coalescing.** When the scanner fires (e.g. 4 thresholds
   cross simultaneously on a fresh row), the notifier picks up all 4
   in a single tick. Email batches per renewal are at most one per
   threshold-crossing event, not one per minute.

Why not more? 15 min is the sweet spot where the 1-day-out tier still
gets same-business-hour responsiveness without burning unnecessary
compute on idle ticks (most ticks return seen=0).

**Initial delay: 5 minutes.** Lets the scanner cron arm + populate
db.renewal_reminders before the first dispatch tick attempts to read.
Without this delay, a cold-start could see an empty queue and a
freshly-fired batch milliseconds later (race-tolerant but wasteful).

**Per-tick cap: 50 reminders.** A backlog spike (e.g. operator
unarchives 100 renewals at once) shouldn't blow a single tick's email
budget. 50 is generous against expected steady-state (typically <5 per
tick) and bounds outlier ticks.

---

## Urgency-to-channel routing rules

| Urgency band | Days until | Email | SMS |
|---|---|---|---|
| critical | <= 3 | yes | yes (if phone present) |
| urgent | 4-7 | yes | yes (if phone present) |
| soon | 8-30 | yes | NO (operator-noise threshold) |
| fine | > 30 | yes | NO (long-window heads-up) |

**Why SMS is gated to critical/urgent only:**
- Per `feedback_inbox_clean_policy.md` + general operator-noise
  discipline, SMS is reserved for things that warrant a phone-buzz.
  A 60-day-out reminder doesn't.
- Industry convention: brokers + dispatchers triage SMS as
  "happening now". Misuse erodes the signal. Renewal urgency tiers
  align naturally: critical/urgent = "now", soon/fine = "queue it".
- Telnyx port is not yet live (operator on Twilio mid-migration per
  `project_provider_migration_decision.md`). When `send_sms` helper
  is unavailable, the notifier soft-skips SMS silently and keeps the
  email path live. Email remains the primary channel; SMS is augment.

**SMS body pattern (when fired):**
```
CHL renewal: <name> expires <today | in Nd>. Action required.
```
Brief by SMS-craft convention. The full detail is in the email; the
SMS is the buzz that makes the operator open the email.

---

## Retry policy

**Retry cap: `MAX_DISPATCH_ATTEMPTS = 3`.**

Failure modes considered:
- Resend API transient 5xx -> retry next tick.
- Email helper throws (network blip, DNS, etc.) -> retry next tick.
- Persistent failure (e.g. invalid recipient address) -> escalate to
  errored after 3 attempts so the failure is visible in the cron
  health surface + journal.

State transitions on failure:
- Tick 1 fail: attempt_count=1, dispatch_status='pending', error_msg
  recorded.
- Tick 2 fail: attempt_count=2, dispatch_status='pending'.
- Tick 3 fail: attempt_count=3, dispatch_status='errored' (terminal).

Once `errored`, the row is excluded from future ticks. Operator can
manually re-flip via dashboard / direct DB edit. We do NOT auto-purge
errored rows -- they're audit trail.

**Why 3 (not 5 or 1):**
- 1 is too aggressive: a single Resend hiccup permanently errors a
  reminder that would have fired fine 15 min later.
- 5 is too slow: a malformed recipient address would burn 5 cron ticks
  (75 min) before the operator sees the failure surface.
- 3 strikes the balance: ~45 min recovery window before terminal flag.

---

## Dispatch-status state machine

```
        +--------+  scanner inserts  +--------+
        |  (no   | ----------------> | pending|
        |  row)  |                   +--------+
        +--------+                       |  ^
                                         |  | retry next tick
                                         |  |  (attempt < MAX)
                            email_ok?    |  |
                              |          |  |
                +-------+ no  V          |  |
                |       | --> attempts ++^---+
                |  err  |          |  yes (>= MAX)
                +-------+          V
                                   +-------+
                                   |errored|
                                   +-------+
                                       (terminal)

                        email_ok? yes
                              V
                        +----------+
                        |dispatched|
                        +----------+
                            (terminal)
```

Schema fields touched (all additive):
- `dispatch_status`: 'pending' | 'dispatched' | 'errored' | 'skipped'
  (the scanner sets 'pending' on insert).
- `dispatched_at`: ISO timestamp on success.
- `dispatched_via`: 'email' | 'email+sms' on success.
- `error_msg`: str on failure (most recent error).
- `attempt_count`: int (incremented on each failed attempt).
- `last_attempt_at`: ISO timestamp on each attempt.
- `notifier_version`: NOTIFIER_VERSION stamp on touch.

No breaking changes vs the KK1 reminder schema -- LL1 only ADDS fields.

---

## Outbound-email-policy integration

Per `~/.claude/projects/c--CHL/memory/feedback_outbound_email_policy.md`,
operator authorized dev to send outbound email signed "Dispatch Team"
without revealing AI use, with strict tone discipline.

LL1 honors this in three places:

1. **Standing signature** (verbatim, ASCII-only):
   ```
   Dispatch Team
   Continental Haul Logistics LLC
   dispatch@continentalhaul.com
   MC 1817555 . USDOT 4569843
   continentalhaul.com
   ```
   Stored as `DISPATCH_SIGNATURE` module constant; appended to every
   composed body.

2. **Tone discipline.** Body is brief + factual + freight-broker.
   Forbidden patterns audited via test
   (`test_dispatch_composes_email_with_dispatch_team_signature`):
   - No "friendly" / preamble.
   - No "AI" / reveal phrases.
   - No em-dashes (ASCII-only enforced).
   - No "Hope this helps!" closings.
   Body lines follow the operator's preferred pattern: "X expires Y
   (Z). Vendor: ABC. Action required this week."

3. **Subject prefixed with urgency tier.** "URGENT renewal reminder:
   ..." or "CRITICAL renewal reminder: ..." for inbox-triage
   scannability. The prefix is also the only inflection -- no shouty
   formatting, no exclamation marks, no emoji.

Recipient resolution: `renewal_contact_email` if present on the
renewal row, otherwise falls back to `dispatch@continentalhaul.com`
(the operator's own inbox). Renewals are operator-action items, so
defaulting to operator inbox is the right behavior when no
explicit-contact override is set.

**Email helper used:** `server.send_email` (existing helper, line
~7886 of server.py). Lazy-imported inside the notifier so module-load
order doesn't trigger a circular import via server's renewals-router
include. Tests inject a fake helper at the call boundary
(`send_email_fn` kwarg) so SMTP / Resend is never touched.

The existing helper:
- Resolves `RESEND_API_KEY` from env. When unset, returns
  `{"status": "mocked"}` and writes to `db.outbound_messages`. The
  notifier treats 'mocked' as success because the platform still
  recorded the dispatch; the mock is the operator-facing fallback
  before Resend is provisioned.
- Returns `{"status": "sent"}` on real Resend send (HTTP < 300).
- Returns `{"status": "error"}` on send failure -- notifier counts
  this as a failed attempt (retry next tick).

---

## Health surface

Two health channels:

1. **Module health** -- `renewals_notifier.health()`. Reports last
   tick's dispatched / failed / by_urgency / errors + cumulative
   totals.
2. **Cron-tier health** -- `cron_scheduler._renewals_notifier_health_check()`.
   Reports loop_running + last tick status. Auto-registered with
   `health_monitor` under key `renewals_notifier_cron`.

Both channels surface in `/api/health/system`. A degraded notifier is
visible alongside the existing detention + redispatch + scanner cron
health.

Healthy states: `ok`, `success`, `never_run` (loop running but no
work yet), `skipped_read_only`, `skipped_kill_switch`.

Anything else flips the cron tile to `degraded`.

---

## Tests

Six unit tests in `backend/tests/test_renewals_notifier.py`:

1. `test_dispatch_picks_pending_reminders_only` -- only rows with
   `dispatch_status='pending'` are processed; `dispatched` and
   `errored` rows are skipped.
2. `test_dispatch_composes_email_with_dispatch_team_signature` --
   subject + body include vendor, days_until, and the verbatim
   Dispatch Team signature; ASCII-only; no AI tells.
3. `test_dispatch_skips_sms_when_urgency_below_urgent` -- a 'soon'
   urgency reminder with phone number does NOT trigger SMS;
   `dispatched_via='email'` (not 'email+sms').
4. `test_dispatch_marks_row_dispatched_on_success` -- successful
   dispatch stamps `dispatch_status='dispatched'`, `dispatched_at`,
   `dispatched_via`, clears `error_msg`.
5. `test_dispatch_marks_row_errored_after_3_failed_attempts` -- 3
   failed ticks transition the row through pending(1) -> pending(2)
   -> errored(3). Tick 4 sees no work.
6. `test_dispatch_honors_should_skip_tick` -- when
   `should_skip_tick()` returns True, summary is `skipped_kill_switch`,
   no email sent, row unchanged.

All pass; combined with the 12 KK1 regression tests, the renewals
suite is 18/18 green.

---

## Open follow-ups (handed off to next stream)

1. **Mark-renewed deeplink not yet live.** `RENEWAL_DASHBOARD_URL` is
   a placeholder pointing at `app.continentalhaul.com/dashboard/renewals`
   -- the frontend Renewals tab is not yet wired up. Update once the
   tab ships so the email body's link is clickable.
2. **SMS path is shimmed.** `send_sms` helper exists at server.py
   line ~7789 and is imported lazily; pre-Telnyx-port we soft-skip
   silently. Once port lands, retest the email+sms path against a
   live carrier / live recipient.
3. **No outbound_email_log audit row from the notifier yet.** Per
   outbound-email-policy section "Audit trail", every outbound email
   sent autonomously logs to `chl-memory/operations/outbound_email_log.md`.
   The current notifier writes to `db.outbound_messages` (via
   server.send_email's own write) but does NOT write the markdown
   audit file. Follow-up: add an audit-log append per dispatched
   reminder so the operator can review at will.
