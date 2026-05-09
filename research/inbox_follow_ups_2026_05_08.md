# Inbox follow-ups 2026-05-08 (PP3 stream landing)

> **Source:** PP3 sub-agent stream landed 2026-05-08 EOD-16 late, post-NN1 InboxView ship.
> **Status:** Backend code shipped + tested (10/10 inbox tests passing); frontend search box wired with debounce; doc-only commit to chl-memory per task scope (parent commits the code).

---

## Why these follow-ups exist

NN1 shipped a working operator-facing InboxView at iter EOD-16-late
(`backend/inbound_email/inbox_router.py` + `frontend/src/App.js` InboxView ~302
lines). Two operator-meaningful gaps surfaced immediately on first walk-through:

1. **Mark Handled didn't sync to Zoho IMAP.** Clicking "Mark Handled" flipped
   `db.inbound_email_events.sender_type="neutral"` but left the message
   unread on the IMAP server. Operator's Zoho web inbox kept showing it as
   unread until manually marked read in two places — the exact
   "operator-touch" overhead the inbox surface was built to remove.
2. **No search.** Once a couple weeks of messages accumulated the folder pills
   + classifier pills weren't enough to find a specific sender or thread.

PP3 stream addresses both.

---

## A. IMAP `\Seen` sync helper (backend)

### Public seam

```python
# backend/inbound_email/inbox_router.py
async def _mark_imap_seen(
    *, db: Any, message_id: str, folder: str = "INBOX",
) -> bool:
    """Set the \\Seen flag on the IMAP message corresponding to message_id.
    Best-effort; logs failures + returns False, never raises. Re-uses the
    IMAP connect-once-select-store pattern from spam_blocker.

    Honors CHL_IMAP_MARK_SEEN_DISABLED env var (kill switch). When the
    kill switch is on or no password is provisioned, this is a no-op
    that returns False without touching the network."""
```

### Behavior matrix

| Condition | Behavior |
|---|---|
| `CHL_IMAP_MARK_SEEN_DISABLED=true` | log + return False; no network |
| `ZOHO_IMAP_PASSWORD` unset | log + return False; no network |
| `message_id` empty | return False |
| `db.inbound_emails` row missing | log + return False |
| Row exists but no `uid` field | log + return False |
| Connect/login/select/STORE raises | log + return False |
| All preconditions met | login -> SELECT folder readonly=False -> UID STORE +FLAGS (\\Seen) -> logout -> return True |

Folder resolution: `folder` arg wins; otherwise `db.inbound_emails.source_folder`;
otherwise `"INBOX"`.

The IMAP4_SSL constructor is exposed as a module-level seam
(`inbox_router._open_imap_connection`) so tests can monkey-patch with a
mock client — same pattern as `imap_poller._open_imap_connection` and
`spam_blocker._open_imap_connection`.

### Wire-up site

`backend/inbound_email/sender_router.py::override_sender_type` now calls
`_mark_imap_seen(db=db, message_id=event_id)` after the DB update, regardless
of which sender_type the operator picks. The frontend "Mark Handled" button
already POSTs `sender_type=neutral`, so the IMAP-seen flip happens
automatically. Other sender_type overrides (partner/prospect/spam) ALSO get
the seen-flip, which is the right behavior — the operator has triaged the
message, so it's no longer "new" from a mailbox-attention standpoint.

The endpoint response now includes `imap_seen_synced: bool` so the frontend
could surface a follow-up toast "marked read on Zoho" if it ever wants to.
At first ship the frontend ignores it (the existing "Marked handled."
toast is sufficient).

### Why best-effort, not load-bearing

The DB sender_type override is the source of truth for our classification
state. The IMAP-seen flip is a UX nicety that keeps Zoho web in sync. If
Zoho IMAP is down or the message has been server-side purged, the operator
should still get the override stamped; the kill switch + silent-False
fallback ensure that.

---

## B. Search filter (`q` query param)

### API signature

```
GET /api/_inbox/list?folder=&since_hours=&limit=&classified=&q=
```

| Param | Type | Cap | Description |
|---|---|---|---|
| `q` | str | 100 chars | Case-insensitive substring across `from_addr` + `from_display` + `subject`. FastAPI `Query(max_length=100)` rejects oversize inputs at 422. |

### Server-side implementation

```python
if q:
    escaped = re.escape(q.strip())
    query["$or"] = [
        {"from_addr":    {"$regex": escaped, "$options": "i"}},
        {"from_display": {"$regex": escaped, "$options": "i"}},
        {"subject":      {"$regex": escaped, "$options": "i"}},
    ]
```

`re.escape` ensures operator input never reaches the regex engine
unescaped, so `q="."` matches a literal dot rather than every character.

The response echoes `q` back at the top level for round-trip debug.

### Frontend integration

`InboxView` adds a search input next to the Refresh button with:
- 250 ms debounce (raw `searchInput` state -> `searchQuery` state via
  `setTimeout`, cleanup on unmount/keystroke).
- `maxLength={100}` HTML attribute matches the backend cap.
- Clear-x button when input non-empty.
- `searchQuery` flows into `refresh()`'s `params.q`; `useCallback`
  dependency list updated so the effect re-runs on debounce settle.

Existing 60-second auto-refresh continues to apply current search filter.

---

## C. Tests (5/5)

`backend/tests/test_inbox_search_and_seen.py` — uses an extended `_FakeDB`
mock that supports Mongo `$or` + `$regex` clauses. Mock IMAP client records
login/select/uid(STORE)/logout calls so the success path can verify exact
flag + uid values.

| Test | Coverage |
|---|---|
| `test_list_filters_by_q_substring_in_subject` | q matches against subject |
| `test_list_filters_by_q_substring_in_from_addr` | q matches against from_addr + from_display |
| `test_list_q_is_case_insensitive` | TWILIO == twilio == TwIlIo |
| `test_list_q_max_length_enforced` | 422 on >100 chars; exact-100 allowed |
| `test_mark_imap_seen_falls_back_silently_when_disabled` | Five sub-paths: kill-switch on, no password, missing row, success, connect-raises. Last asserts STORE was issued with `+FLAGS (\\Seen)` against the right uid. |

Result: **10 / 10 inbox tests pass** (5 PP3 new + 5 NN1 regression in
`test_inbox_endpoint.py`). Sender-classifier (9/9) + IMAP poller multi-folder
(15/15) regressions all green. Frontend `npm run build` succeeded
(+176B gzipped main bundle).

Preflight: 20 pre-existing module-not-installed failures
(reportlab/openai/twilio/cryptography/psutil/email-validator). Zero new
failures introduced by PP3.

---

## D. Future follow-ups (not in PP3 scope)

1. **Bulk Mark-as-read for entire folders.** The current helper handles one
   message at a time. A "mark all visible as read" button on the InboxView
   would issue `_mark_imap_seen` for each row. Watch out for IMAP login
   throttling — bulk version should reuse a single connection across
   messages, similar to `imap_poller`'s connect-once-select-many pattern.
2. **`\Seen` undo path.** If the operator un-handles a message (overrides
   neutral back to partner/prospect/spam), we currently leave the seen flag
   set. Could add a `_mark_imap_unseen` helper that issues
   `UID STORE -FLAGS (\Seen)`. Probably not worth shipping until the
   operator surfaces it as a real problem; a re-classified message is
   typically still "read" from a mailbox-attention standpoint.
3. **Server-side full-text body search.** `q` only matches headers
   (from/subject). A future `qb` (body-search) param could regex against
   `body_text`. Cost: bodies can be 100KB each * thousands of rows; a
   Mongo text index on `body_text` would make this affordable. Filed
   for later — operator hasn't asked for body search yet.

---

## E. Files touched

| File | Delta | Status |
|---|---|---|
| `backend/inbound_email/inbox_router.py` | +~190 lines (helper + q param) | PP3 changed (parent commits) |
| `backend/inbound_email/sender_router.py` | +~15 lines (wire-up + response field) | PP3 changed (parent commits) |
| `frontend/src/App.js` | +~30 lines (search input + debounce + state) | PP3 changed (parent commits) |
| `backend/tests/test_inbox_search_and_seen.py` | +~430 lines new | PP3 changed (parent commits) |
| `chl-memory/research/inbox_follow_ups_2026_05_08.md` | new doc | PP3 commits this file |
