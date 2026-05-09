# Renewal calendar / expiration tracker -- feature design

**Iter 145.x Stream KK1 (2026-05-08, sub-agent KK1).** Design doc for
the platform-internal expiration tracker that ships in
`backend/renewals/`. Operator-mandated: see
`~/.claude/projects/c--CHL/memory/project_renewal_calendar_platform_feature.md`
for the verbatim directive.

> "Another thing we will need to do is identify all the expiration
> dates for everything we need to operate so we are never plagued by
> unpaid bills that we did not knw about. We need to build that intothe
> platform and setup reminders." -- operator 2026-05-08 EOD-16

---

## Why this exists

Solo-operator + multi-context = single point of failure on calendar
tracking. Three categories of risk:

1. **Tier 1 -- existential.** Missed BMC-84 bond renewal collapses
   broker authority. Missed FinCEN BOI report risks $500/day fines.
   Missed MO SoS annual report = administrative dissolution.
2. **Tier 2 -- service interruption.** Missed domain renewal kills the
   website. Missed Twilio number renewal releases the phone number.
   Missed Mongo Atlas billing degrades production DB.
3. **Tier 3 -- security drift.** API keys not rotated annually
   accumulate exposure. JWT_SECRET reused indefinitely lets stolen
   tokens stay valid forever.

Embedding this in the platform turns "operator must remember" into
"platform reminds operator at 60d / 30d / 14d / 7d / 3d / 1d."

---

## Module layout

```
backend/renewals/
    __init__.py                  -- module shape + cron registration ref
    renewals_calculator.py       -- pure-math helpers (no DB, no I/O)
    renewals_scanner.py          -- cron producer (writes reminder rows)
    renewals_router.py           -- operator surfaces

backend/tests/
    test_renewals_calculator.py  -- 4 tests
    test_renewals_scanner.py     -- 4 tests
    test_renewals_router.py      -- 4 tests
```

### Calculator -- pure helpers

```python
compute_days_until(expires_at, *, now=None) -> int
    Whole-days until expiry; clamps to 0 on past-due / unparseable.

which_thresholds_just_crossed(*, days_until, thresholds, previously_notified) -> list[int]
    Subset of thresholds where days_until <= T and T not yet stamped.
    Returned sorted descending so the broadest-window crossing fires first.

categorize_urgency(days_until) -> str
    'critical' (<=3) / 'urgent' (<=7) / 'soon' (<=30) / 'fine' (>30)

is_sms_eligible_band(band) -> bool
    True for critical + urgent only -- per design SMS reserved for
    high-stakes thresholds (we don't SMS for 60-day-out reminders).

default_thresholds() -> [60, 30, 14, 7, 3, 1]
```

### Scanner -- cron producer

```python
async def scan_renewals(*, db, now=None, dry_run=False) -> dict
```

For each non-archived row in `db.renewals`:

1. compute days_until
2. resolve thresholds (per-row override -> module default)
3. resolve previously-notified anchors from row.reminders_sent_at
4. compute newly-crossed thresholds
5. for each newly-crossed threshold T:
   - upsert composite-id row into `db.renewal_reminders` with
     `_id = f"{renewal_id}:{T}"` and `dispatch_status="pending"`
   - $addToSet T into the renewals row's reminders_sent_at array

Returns `{seen, fired, by_category, errors, ran_at, dry_run, status}`.

Honors `integrity.operational_state.is_read_only()` for kill-switch
defense-in-depth.

### Router -- operator surfaces

| Method | Path | Auth | Purpose |
|---|---|---|---|
| GET | `/api/_renewals/summary` | require_owner | List w/ days_until + urgency_band + band_counts header |
| GET | `/api/_renewals/{id}` | require_owner | Drill-down + reminder history |
| POST | `/api/_renewals` | require_owner | Create new row |
| POST | `/api/_renewals/{id}/mark-renewed` | require_owner | Bump expires_at by cadence; clear reminders_sent_at |
| DELETE | `/api/_renewals/{id}` | require_owner | Soft-delete (archived: true) |
| GET | `/api/_renewals/_health` | require_broker | Module health snapshot (broker-readable for monitoring) |

Filter params on `/summary`: `since` (updated_at >= ISO), `category`,
`urgency` (post-filter on enriched band), `include_archived`, `limit`.

---

## Schema -- `db.renewals`

```python
{
    "_id": uuid_hex_str,
    "category": "regulatory" | "vendor" | "security" | "tax" | "insurance",
    "name": "BMC-84 broker bond",
    "vendor_or_authority": "Roanoke Insurance Group" | None,
    "current_value_hash": str | None,    # sha256[:16] of secret if applicable
    "current_value_provisioned_at": iso_str | None,
    "expires_at": iso_str,                # required
    "last_renewed_at": iso_str | None,
    "renewal_cadence_days": int,          # required, 1..3650
    "renewal_url": str | None,
    "renewal_contact_email": str | None,
    "reminder_thresholds_days": [60, 30, 14, 7, 3, 1],   # default
    "reminders_sent_at": list[int],        # threshold-anchor stamps
    "auto_renew": bool,
    "operator_action_required": bool,
    "notes": str | None,
    "archived": bool,                      # soft-delete flag
    "created_at": iso_str,
    "updated_at": iso_str,
}
```

**Schema deviation from project memory spec:**

- `reminders_sent_at` stores **integer threshold anchors** (e.g.
  `[60, 30, 14]`) rather than ISO timestamps. Reason: the scanner needs
  to ask "have we already fired the 30-day threshold?" -- comparing
  against integer threshold values is direct; comparing against ISO
  timestamps requires the scanner to back-derive thresholds from row
  state, which is fragile. The `created_at` of each row in
  `db.renewal_reminders` carries the actual fire timestamp for audit.
  The scanner is forgiving of legacy ISO-string entries (skips them).
- `current_value` renamed to `current_value_hash` with explicit "never
  the value" comment. Operator never stores plaintext secrets in the
  renewals collection.
- Added `archived: bool` for soft-delete. Project memory spec didn't
  call this out; it's a standard pattern across other CHL modules.

---

## Schema -- `db.renewal_reminders`

```python
{
    "_id": f"{renewal_id}:{threshold}",   # composite -- idempotent
    "renewal_id": uuid_hex_str,
    "renewal_name": str,
    "category": str,
    "vendor_or_authority": str | None,
    "threshold_days": int,                 # 60 / 30 / 14 / 7 / 3 / 1
    "days_until_at_fire": int,             # captured at scan time
    "urgency_band": "critical" | "urgent" | "soon" | "fine",
    "sms_eligible": bool,
    "renewal_url": str | None,
    "renewal_contact_email": str | None,
    "operator_action_required": bool,
    "expires_at": iso_str,
    "scanner_version": "0.1.0",
    "created_at": iso_str,                 # when scanner fired it
    "dispatched_at": iso_str | None,        # set by future dispatcher
    "dispatch_status": "pending" | "sent" | "failed",
}
```

The composite-id pattern mirrors `detention_charges` -- a re-run of
the scanner on the same row + threshold collides on `_id` and the
$setOnInsert short-circuits. Defense-in-depth: `_addToSet` of T into
`reminders_sent_at` makes the *next* scan filter T out before even
attempting the upsert.

---

## Cron registration

```
backend/cron_scheduler.py:
    CRON 8 -- renewals_scanner_cron
    interval_sec = 21600  # 6 hours
    initial_delay_sec = 600  # 10-min cold-start delay
```

**Why 6 hours:** thresholds are day-granular (60 / 30 / 14 / 7 / 3 / 1).
Polling more often than 1/day is generous. We use 6h so a row that
lands inside the day's threshold band still fires the same UTC-day
rather than potentially next-day. 10-min initial delay lets DB
connection settle + other cron loops arm first.

The cron loop honors `integrity.operational_state.should_skip_tick()`
for read-only / kill-switch defense-in-depth (matches
detention_scanner_cron pattern). Defense-in-depth at TWO tiers: cron
loop checks `should_skip_tick()` before invoking the scanner; scanner
itself checks `is_read_only()` before hitting Mongo.

---

## Reminder channel design (deferred to next iter)

The scanner is a **producer**. It writes rows into
`db.renewal_reminders` with `dispatch_status="pending"` and never
actually sends email or SMS. A future iter ships a notification
dispatcher that:

1. Polls `db.renewal_reminders` for `dispatch_status="pending"` rows.
2. Per row:
   - Email to `renewal_contact_email` AND `dispatch@continentalhaul.com`
     (operator). Always.
   - SMS to operator cell IF `sms_eligible=true` (critical or urgent
     band).
3. Stamps `dispatched_at` + flips `dispatch_status` to `sent` or
   `failed`.
4. Retries failures up to N times with exponential backoff.

Splitting producer + consumer:

- **Decouples scanner failure from dispatcher failure.** A bad email
  template or SMS provider outage doesn't kill the scanner.
- **Lets operator inspect pending reminders** before they fire (manual
  override path).
- **Mirrors the email/SMS dispatch pattern already used elsewhere in
  CHL** (e.g. `agent_journal` -> `email_inbox` flow).

The dispatcher is a separate stream -- not in scope for KK1.

---

## Frontend (deferred to next iter)

A new owner-only "Renewals" tab in App.js. Layout similar to
PipelineWalkthroughView:

- **Header:** band counts (count of items in critical / urgent / soon /
  fine bands).
- **Main table:** sortable rows. Columns: name | category | days_until
  | urgency_band | auto_renew | operator_action_required.
- **Color coding:** red (critical), orange (urgent), yellow (soon),
  green (fine), gray (archived).
- **Click row** -> drill-down side panel showing renewal_url,
  last_renewed_at, contact, notes, full reminder history,
  "Mark renewed" button.
- **Add button** -> form to create a new row.

Frontend is NOT in scope for KK1. The endpoint design above is
frontend-ready (see `band_counts` rollup on `/summary` response).

---

## Mark-renewed behavior

When operator hits `POST /api/_renewals/{id}/mark-renewed`:

1. `last_renewed_at = renewed_at (or now)`
2. `expires_at = renewed_at + cadence_days`
3. `reminders_sent_at = []` (clear -- next cycle's ladder fires fresh)
4. `operator_action_required = False`
5. If body has `current_value_hash` -> stamp it +
   `current_value_provisioned_at = renewed_at`.
6. If body has `notes` -> append `[{renewed_at}] {notes}` to existing
   notes (audit trail).

Body fields all optional. `renewed_at` defaults to "now" -- backdating
allowed for "I forgot to mark this when it actually renewed last week."

---

## Initial seeding -- operator-driven, NOT automatic

Per project memory directive, operator drives initial population. The
`scan_renewals` cron does NOT seed `db.renewals` from any other source.

The initial seed list (Tier 1 / 2 / 3 catalogue with suggested
cadence_days for each item) lives at
`chl-memory/research/initial_renewals_seed_list.md`. Operator can:

- POST each item via `/api/_renewals` (recommended).
- Run a one-time seed script reading the list (future utility).
- Manually mongosh insert with the schema above.

Going forward, two natural integration points to auto-add rows:

1. When `secrets_cli migrate-from-env` runs (DD3), insert a renewals row
   for each migrated secret with cadence_days=365 default.
2. When new vendor signup happens (Stripe / Telnyx / Plivo / etc.),
   add a row at signup-time. Could be wired into the existing
   integration-credential setup flows.

Neither auto-add is in scope for KK1.

---

## Test coverage

12 tests, 12 passing:

- **Calculator (4):** clamp, threshold-crossing basic, threshold-crossing
  skip-already-notified, urgency band cutoffs.
- **Scanner (4):** finds due renewals (skips far-out + archived),
  appends per-threshold reminder row, idempotent re-run, read-only
  kill switch.
- **Router (4):** summary returns enriched rows + band_counts, summary
  filters by category + urgency, mark-renewed advances expires_at +
  clears reminders, all write paths require owner auth.

Preflight `scripts/preflight_server_smoke.py`: PASS (172 routers + 18
crons + 5 health-registers verified).

---

## Highest-priority follow-ups

1. **Notification dispatcher cron** -- polls `db.renewal_reminders` for
   pending rows; fires email + SMS per band rules; stamps
   `dispatched_at` + `dispatch_status`. Single 5-min cron loop.
   Estimated effort: 4-6 hours.

2. **Frontend Renewals tab** -- owner-only tab with band-counts header,
   sortable table, drill-down + mark-renewed flow. Estimated effort:
   3-4 hours.

3. **Initial seed script** -- one-time `scripts/seed_renewals.py` that
   reads `initial_renewals_seed_list.md` (or a CSV) and bulk-POSTs to
   `/api/_renewals`. Operator runs once after the feature lands.
   Estimated effort: 1 hour.

4. **Auto-add hooks** -- wire DD3 secrets_cli + vendor signup flows to
   insert renewals rows at provisioning time. Estimated effort: 1-2
   hours per integration.

5. **Per-row threshold-ladder presets** -- monthly-cadence rows (Twilio
   DIDs, Mongo Atlas billing) want a tighter ladder like `[14, 7, 3, 1]`
   not the default `[60, 30, 14, 7, 3, 1]`. Currently overrideable
   manually per row; could ship preset constants
   ("monthly", "quarterly", "annual") in calculator. Estimated effort:
   30 min.
