# Operator-push consumer runbook

WW1 (TT3 closure, 2026-05-08 EOD)

This document is the canonical pointer for any Claude session that finds itself responsible for delivering RED-tier alerts to the operator via AgentDM. Read this once at session boot if you see `dispatch_status='ready_for_claude'` rows in `db.operator_push_queue`, or if the operator says "drain the push queue."

---

## Architecture (one diagram)

```
URGENT/RED event
      |
      v                        backend/inbox_triage/triage_scanner.py
producer  ----- queue_push_notification() ----+   _persist_urgent_alert()
                                              |
                                              v
                                    db.operator_push_queue
                                       (status='pending')
                                              |
                                              v               every 5 min
                                  cron_scheduler CRON 21 ----------------+
                                              |                          |
                                              v                          v
                                  drain_once() in              .tmp/push_outbound_<id>.txt
                                  scripts/operator_push_consumer.py    (ASCII formatted msg)
                                              |
                                              v
                                  status='ready_for_claude'
                                              |
                                              v               whenever a Claude
                                  Claude session loop --------- session is awake
                                              |
                                              v
                                  mcp__agentdm__send_message(to='@operator', ...)
                                              |
                                              v
                                  agentdm_push.mark_push_dispatched()
                                              |
                                              v
                                  status='dispatched'   +   delete .tmp file
```

---

## Producer side -- what writes the rows

`backend/self_healing/agentdm_push.queue_push_notification()` writes a row with these fields:

| field                  | example                                                |
|------------------------|--------------------------------------------------------|
| `_id`                  | `"a3f9..."` (uuid4().hex from `_gen_id`)               |
| `alert_id`             | originating `urgent_alerts._id` or message_id          |
| `severity`             | `"RED"`, `"URGENT"`, or `"HIGH"`                       |
| `subject`              | short headline, capped at 240 chars                    |
| `body_excerpt`         | free-text excerpt, capped at 1200 chars                |
| `source`               | `"llm_reasoning_loop"`, `"inbox_triage"`, ...          |
| `dispatch_status`      | starts at `"pending"`                                  |
| `agentdm_message_id`   | starts at `None`; stamped on dispatch                  |
| `created_at`           | ISO UTC string                                         |
| `dispatched_at`        | starts at `None`; stamped on dispatch                  |
| `dispatch_attempts`    | incremented on `mark_push_failed`                      |
| `last_error`           | last failure reason, capped at 500 chars               |
| `extra`                | dict of extra context (event_kind, cron_name, ...)     |

Active producers:
- `backend/self_healing/llm_reasoning_loop._handle_urgent_alert` -- LLM-flagged anomalies / cron failures.
- `backend/inbox_triage/triage_scanner._persist_urgent_alert` -- inbox triage URGENT verdicts (NEW-insert idempotent).

---

## Cron drain side -- WW1 deliverable

`scripts/operator_push_consumer.py` runs every 5 min via `cron_scheduler.py CRON 21`. It:

1. Connects to mongo via `secret_reader.get_secret('MONGO_URI')` (vault-first, env fallback, `mongodb://localhost:27017` final default).
2. Calls `agentdm_push.list_pending_pushes(db, max_batch=N, max_age_minutes=M)`.
3. For each row:
   - Skips if severity is not in `{URGENT, RED, HIGH}` (defensive).
   - Formats the message (template below).
   - Writes the message to `C:/CHL/.tmp/push_outbound_<push_id>.txt`.
   - Transitions the row to `dispatch_status='ready_for_claude'` (NEW state added by WW1) with `ready_for_claude_at` and `outbound_tmp_path` stamped.
4. Prints a summary: `processed=X dry_run=Y skipped=Z`.

Exit codes:
- `0` -- success (including empty queue or all-skipped).
- `2` -- mongo connection failed.

Idempotency: re-running on the same row is a no-op because `list_pending_pushes` filters by `dispatch_status='pending'`, and once we transition to `ready_for_claude` the row drops out of the pending result.

### NEW state added by WW1

`'ready_for_claude'` is a brand-new dispatch_status value (alongside the existing `pending` / `dispatched` / `failed`). Semantics: "the queue tooling has formatted + persisted an outbound message; an MCP-capable session needs to do the actual `mcp__agentdm__send_message` call." It is NOT a terminal state -- the Claude session is expected to advance it to `dispatched` (success) or back to `pending` (transient failure, via `mark_push_failed`).

### .tmp file convention

Path: `C:/CHL/.tmp/push_outbound_<push_id>.txt`
Encoding: UTF-8
Format:
```
[{severity}] {subject}

{body_excerpt}

--
alert_id: {alert_id}
source: {source}
queued_at: {created_at_iso}
push_id: {_id}
```

`.tmp/` is gitignored (`.gitignore` line 159). Files persist across reboots until the Claude session deletes them.

---

## Claude session prompt template

Use this as the prompt body for any session tasked with draining `ready_for_claude` rows:

```
Drain operator-push queue:

1. Read db.operator_push_queue rows where dispatch_status='ready_for_claude'.
   (Use motor / a quick mongo client; the rows are small.)

2. For each row:
   a. Read the file at row['outbound_tmp_path'] (or the conventional path
      C:/CHL/.tmp/push_outbound_<row._id>.txt if outbound_tmp_path is unset).
   b. Call mcp__agentdm__send_message(to='@operator', message=<file contents>).
      If the @operator handle is not yet provisioned, use '@pm-lead' as the
      fallback recipient -- PM Claude can relay to operator.
   c. On success: call backend/self_healing/agentdm_push.mark_push_dispatched(
         db, push_id=row._id, agentdm_message_id=<id returned from MCP>)
      then delete the .tmp file.
   d. On failure: call mark_push_failed(db, push_id=row._id, error=<reason>)
      and leave the .tmp file in place (next attempt re-uses it).

3. Report a one-line summary: dispatched=X failed=Y.
```

Failure modes to watch for:
- AgentDM channel auth failure -> mark_push_failed with `"agentdm_auth_failure"`; do NOT retry until operator confirms key is healthy.
- File missing (someone else already drained?) -> mark_push_failed with `"tmp_file_missing"`; the producer can re-enqueue.
- Row missing from mongo but file present -> orphan; delete the file, log.

---

## How to test the consumer end-to-end

1. **Synthetic urgent alert.** From a Python REPL with the backend importable:
   ```python
   import asyncio
   from motor.motor_asyncio import AsyncIOMotorClient
   from self_healing import agentdm_push

   async def go():
       db = AsyncIOMotorClient("mongodb://localhost:27017")["chl"]
       push_id = await agentdm_push.queue_push_notification(
           db=db,
           alert_id="manual_test_2026_05_08",
           severity="RED",
           subject="WW1 end-to-end smoke test",
           body_excerpt="If you are reading this in AgentDM, the consumer pipeline works.",
           source="manual",
       )
       print("queued:", push_id)

   asyncio.run(go())
   ```
2. **Run the consumer manually.**
   ```
   python C:/CHL/scripts/operator_push_consumer.py --max-batch=5 --max-age-minutes=60
   ```
   Expected stdout: `processed=1 dry_run=0 skipped=0`.
3. **Verify the .tmp file.**
   ```
   Get-Content C:\CHL\.tmp\push_outbound_<push_id>.txt
   ```
4. **Verify the row state.**
   ```python
   row = await db.operator_push_queue.find_one({"_id": push_id})
   assert row["dispatch_status"] == "ready_for_claude"
   ```
5. **Drain via a Claude session** using the prompt template above. Verify operator receives the message in AgentDM.
6. **Confirm idempotency.** Re-run step 2; expected `processed=0 dry_run=0 skipped=0` because the row is no longer pending.

---

## Why a file-handoff instead of direct AgentDM call

The cron scheduler runs inside the FastAPI process (no MCP client). The `mcp__agentdm__send_message` tool only exists inside a Claude Code session runtime. So the cron-side script can't call AgentDM directly. The `.tmp` file + state-machine handoff is the cheapest decoupling that:
- gives RED alerts a durable persistence point even if no Claude session is awake,
- lets a Claude session pick up alerts that arrived during its downtime,
- doesn't require the backend to spawn or talk to a Claude session,
- keeps secret material (any potential auth tokens in the message body) out of process arguments.

---

## Files (load-bearing)

| path                                                            | role                          |
|-----------------------------------------------------------------|-------------------------------|
| `backend/self_healing/agentdm_push.py`                          | producer-side queue + state-machine |
| `scripts/operator_push_consumer.py`                             | cron drain CLI (WW1)          |
| `backend/cron_scheduler.py` (CRON 21)                           | 5-min loop registration       |
| `backend/tests/test_operator_push_consumer.py`                  | unit tests                    |
| `backend/tests/test_agentdm_push.py`                            | producer-side tests           |
| `chl-memory/research/operator_push_consumer_runbook.md`         | this document                 |
