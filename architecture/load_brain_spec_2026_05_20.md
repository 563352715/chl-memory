# load_brain.py — SMART TMS Phase 1 Spec
**Status:** approved 2026-05-20 by operator (Jason Meyer) — begin scaffolding immediately.
**Author:** dev-Claude (Opus 4.7, 1M ctx).
**Lives at:** `backend/brain/load_brain.py` + `backend/brain/load_brain_state.py` + `backend/brain/watcher_loop.py`.
**Supersedes:** nothing — this is the foundation Phases 2-6 of the SMART TMS plan all subscribe to.

---

## 0. Why this exists

We are building toward fully autonomous brokerage at 1,000 shippers × 1,000 drivers scale. Every individual load needs a **brain** — a state-machine + anomaly detector + decision proposer — running continuously from quote to settlement.

Without it:
- `milestones.py` logs facts but never interprets them
- `carrier_outreach.py` blasts messages but doesn't know if a load is on-track or burning
- `pnl_monitor.py` checks money but only at the gate, not under-execution
- No single source of truth for "is this load OK right now?"

With it:
- Every active load has `db.loads.<id>.brain` showing current state + confidence + recommended action
- `operator_decisions_queue.py` (Phase 6) reads this and surfaces only exceptions
- Phases 2-5 (trust score, lane memory, pricing brain, customer brain) all consume brain events as their input stream

**Anti-drift rule:** future sessions opening this doc must extend, not rewrite. Bump the date suffix on the filename and link [[load_brain_spec_2026_05_20]] from the new doc.

---

## 1. Architectural position

```
┌─────────────────────────────────────────────────────────────┐
│  Facts (existing layers — do not modify)                    │
│  ─ db.milestones        ← carrier check-calls, BOL, POD     │
│  ─ db.geofence_events   ← truck enters/exits zones          │
│  ─ db.inbound_emails    ← shipper + carrier replies         │
│  ─ db.sms_events        ← inbound texts                     │
│  ─ db.pnl_snapshots     ← per-load cost vs revenue          │
└───────────────────────────┬─────────────────────────────────┘
                            │ (read-only)
                            ▼
┌─────────────────────────────────────────────────────────────┐
│  load_brain.py  (NEW)                                       │
│                                                             │
│  ┌───────────────────┐   ┌──────────────────────────────┐   │
│  │ State machine     │   │ Anomaly watcher              │   │
│  │ — deterministic   │   │ — Ollama llama3.1:8b         │   │
│  │ — milestone-driven│   │ — runs every 60s on active   │   │
│  └─────────┬─────────┘   └──────────────┬───────────────┘   │
│            └───────┬─────────────────────┘                  │
│                    ▼                                        │
│         db.loads.<id>.brain  (opinion)                      │
│         db.load_brain_history (audit trail)                 │
└───────────────────────────┬─────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│  Consumers (Phases 2-6, future)                             │
│  ─ carrier_trust_score (P2)   reads brain transitions       │
│  ─ lane_memory         (P3)   reads delivered states        │
│  ─ pricing_brain       (P4)   reads quote→book conversion   │
│  ─ customer_brain      (P5)   uses brain state for ETA reply│
│  ─ operator_decisions  (P6)   subscribes to exception flags │
└─────────────────────────────────────────────────────────────┘
```

**Layering principle:** `milestones.py` writes **facts**. `load_brain.py` writes **opinions**. Never blur the two. Brain reads from milestones; milestones never reads from brain.

---

## 2. The state machine

### 2.1 Happy-path states (16)

```
QUOTE_REQUESTED → QUOTED → BOOKED → CARRIER_SEARCHING → CARRIER_VETTED
   → DISPATCHED → AT_PICKUP → LOADED → IN_TRANSIT → AT_DELIVERY
   → DELIVERED → INVOICED → FACTORED → ADVANCE_RECEIVED
   → CARRIER_PAID → COLLECTED → CLOSED
```

### 2.2 Exception states (8)

| State | Trigger | Resolution path |
|---|---|---|
| `CARRIER_NOSHOW` | Pickup window +60min, no AT_PICKUP geofence ping AND no carrier check-call | Re-cover via dutch_auction; mark prior carrier on trust score |
| `CARRIER_GHOSTED` | DISPATCHED → 4h with no comms response, no movement | Re-cover; flag carrier |
| `WRONG_TRUCK_AT_PICKUP` | AT_PICKUP geofence pings from MC ≠ assigned MC | Operator decision (double-broker check) |
| `HOS_VIOLATION_RISK` | Predicted ETA exceeds driver HOS clock | Reroute or split team |
| `CLAIM_OPEN` | Shipper or carrier opens damage/shortage claim | Hold settlement; route to claims |
| `DETENTION_PENDING` | AT_PICKUP or AT_DELIVERY > 2h | Auto-bill detention; check shipper contract terms |
| `SHORT_PAID` | COLLECTED amount < INVOICED amount | Reconcile + collections workflow |
| `LOST_LOAD` | DISPATCHED but no movement for 24h, no comms | Highest escalation — operator paged |

### 2.3 Terminal states

- `CLOSED` (happy) — money settled, audit complete
- `CANCELLED` (pre-pickup, shipper or our side cancelled)
- `WRITTEN_OFF` (claim or short-pay accepted as loss)

### 2.4 Transition matrix

Each transition has a **trigger** (event type), a **guard** (predicate that must be true), and an **effect** (what brain writes). Full table in `backend/brain/load_brain_state.py::TRANSITIONS`. Sample:

```python
TRANSITIONS = [
    ("QUOTED", "BOOKED", trigger="shipper_accepted_quote",
        guard=lambda load: load["pnl"]["margin_pct"] >= MIN_MARGIN_PCT,
        effect="set_dispatch_target_carrier=None, kick carrier_outreach"),
    ("DISPATCHED", "AT_PICKUP", trigger="geofence_enter_pickup",
        guard=lambda load: load["carrier"]["mc"] == load["assigned_carrier"]["mc"],
        effect="start_detention_clock"),
    ("DISPATCHED", "WRONG_TRUCK_AT_PICKUP", trigger="geofence_enter_pickup",
        guard=lambda load: load["carrier"]["mc"] != load["assigned_carrier"]["mc"],
        effect="page_operator_immediately"),
    # ... ~80 total transitions
]
```

---

## 3. The schema

### 3.1 db.loads.<id>.brain (the opinion subdoc)

```python
{
    "current_state": "IN_TRANSIT",                    # one of 24 states
    "previous_state": "LOADED",
    "entered_at": "2026-05-20T14:32:00Z",
    "confidence": 0.92,                                # 0..1, brain's certainty
    "recommended_action": None,                        # str | None — set if action needed
    "operator_attention_required": False,              # if True, queued for Phase 6
    "exception_flags": [],                             # ["detention_pending", ...]
    "predictions": {
        "eta_delivery": "2026-05-21T08:00:00Z",
        "eta_confidence": 0.88,
        "predicted_margin_at_delivery": 287.50,
        "predicted_settlement_date": "2026-06-04",
    },
    "last_anomaly_check": "2026-05-20T14:31:00Z",
    "last_anomaly_findings": [],                       # short list of strings
    "brain_version": "0.1.0",                          # this spec's version
    "history_ref": "<load_brain_history _id>",         # pointer to full audit
}
```

### 3.2 db.load_brain_history (audit trail, append-only)

```python
{
    "_id": ObjectId,
    "load_id": "...",
    "ts": "2026-05-20T14:32:00Z",
    "from_state": "LOADED",
    "to_state": "IN_TRANSIT",
    "trigger_event_type": "geofence_exit_pickup",
    "trigger_event_id": "...",                         # ref into facts collection
    "guard_evaluations": [{"name": "is_correct_mc", "result": True}],
    "brain_reasoning": "Geofence exit at 14:30 matches assigned carrier MC. " +
                       "ETA 18h from pickup tracks at 92% confidence based on " +
                       "lane history (n=14 prior loads, mean 17.4h).",
    "ollama_model": "llama3.1:8b",
    "ollama_latency_ms": 1247,
    "operator_notified": False,
}
```

**Why history:** every brain decision is replayable. If a load goes bad, we can `git log`-equivalent the brain's reasoning at every step. Required for: claims defense, model improvement, regulatory audit.

### 3.3 db.operator_decisions_queue (Phase 6, stubbed in Phase 1)

```python
{
    "_id": ObjectId,
    "created_at": "...",
    "load_id": "...",
    "brain_state": "WRONG_TRUCK_AT_PICKUP",
    "severity": "HIGH",                                # LOW | MEDIUM | HIGH | URGENT
    "summary": "MC mismatch at pickup — assigned 1817555, observed 9988776",
    "suggested_actions": [
        {"label": "Confirm sub-carrier auth", "endpoint": "/api/brain/confirm_sub"},
        {"label": "Cancel load", "endpoint": "/api/loads/<id>/cancel"},
        {"label": "Call dispatcher", "phone": "..."}
    ],
    "claimed_by": None,
    "resolved_at": None,
    "resolution_action": None,
}
```

---

## 4. The anomaly watcher

### 4.1 Cadence

Cron every **60 seconds**. Scans active loads (states between BOOKED and DELIVERED).

### 4.2 What it checks (deterministic first, then AI)

**Deterministic checks (cheap, run every cycle):**
- Has the load been in current state past expected duration? (e.g., DISPATCHED > 4h with no AT_PICKUP)
- Is `eta_delivery` slipping vs pickup_appointment?
- Has any monitored field gone null/missing?
- Is detention clock past free time?

**AI checks (run when deterministic flags something OR every 5 min):**
- Pass load context + recent events + current state + lane history into llama3.1:8b
- Prompt: "Given this load's current state and recent events, is anything wrong? What should we do?"
- Brain compares AI suggestion against allowed actions in current state
- If AI suggests an action not in allowlist, log + escalate to operator (never auto-execute outside allowlist)

### 4.3 Action allowlist (per state)

Brain CAN autonomously:
- Send a check-call SMS template to a carrier
- Update ETA based on geofence movement
- Start/stop detention clock
- Re-cover a load via dutch_auction (if carrier ghosted past threshold)
- Update PnL projection
- Send templated shipper update (ETA, delivered, invoiced)

Brain MUST escalate to operator:
- ANY action that moves money > $20 (carrier QuickPay, invoice adjustment, claim approval)
- ANY action that changes load assignment after DISPATCHED
- ANY action that voids/cancels
- ANY contact with shipper outside of templated status updates
- ANY exception state entry (the 8 from §2.2)

### 4.4 Ollama prompt template

Stored at `backend/brain/prompts/anomaly_check_v1.txt`. Versioned. Changes bump suffix.

```
You are watching freight load {load_id} which is currently in state {state}.

Recent events (last 4h):
{events_summary}

Lane history for {origin}→{dest}:
- n={n} prior loads
- mean transit time: {mean_hours}h
- on-time rate: {ot_pct}%

Carrier history ({carrier_mc}):
- prior loads with us: {prior_count}
- trust_score: {trust_score}
- last_communication: {last_comm}

Question: Is anything wrong with this load right now? If so, what is the recommended action?

Respond ONLY with valid JSON:
{
  "status": "ok" | "warning" | "exception",
  "confidence": 0.0..1.0,
  "findings": ["string", ...],
  "recommended_action": null | "string from allowed actions list",
  "reasoning": "one paragraph"
}
```

---

## 5. Event-driven hooks (not just polling)

Some events demand immediate brain recomputation:
- Inbound SMS from carrier ⇒ `recompute_brain(load_id)` within 5s
- Inbound email reply ⇒ same
- Geofence ping ⇒ same
- Operator manual override ⇒ same

Existing modules (`carrier_outreach.py`, `milestones.py`, `inbound_email_router.py`, `geofence_handler.py`) get a one-line addition at their event-emit sites:

```python
from brain.load_brain import recompute_brain
recompute_brain(load_id, triggered_by=event_type, event_id=event_id)
```

No refactor of existing modules — just hook calls.

---

## 6. Confidence scoring

Brain attaches a confidence to every state assertion.

```python
confidence = (
    0.5 * deterministic_score      # did all guards pass cleanly?
  + 0.3 * ai_score                  # Ollama returned status=ok with high conf
  + 0.2 * historical_score          # has this lane/carrier behaved this way before?
)
```

Thresholds:
- `confidence >= 0.85` ⇒ autonomous action allowed (within allowlist)
- `0.6 <= confidence < 0.85` ⇒ autonomous but operator notified (low priority queue)
- `confidence < 0.6` ⇒ ESCALATE to operator_decisions_queue, do NOT auto-act

---

## 7. Dry-run mode

Brain runs in **dry-run** for first 30 days:
- Computes state + recommendations + confidence
- Writes to `db.loads.<id>.brain` AND `db.load_brain_history`
- Does NOT execute autonomous actions (carrier_outreach.py + dutch_auction.py + sms sender remain operator-driven)
- Operator reviews brain's would-have-done actions vs what operator actually did
- After 30 days of soak with > 90% agreement, flip to live mode per load_state (gradually, not all at once)

Config flag: `db.brain_config.global.mode in {"dry_run", "shadow", "live"}`.
- `dry_run`: no execution
- `shadow`: brain executes only LOW-severity allowlist actions; HIGH stays operator
- `live`: full allowlist enabled

---

## 8. File layout

```
backend/
└── brain/
    ├── __init__.py                  # exports: recompute_brain, get_brain_state
    ├── load_brain.py                # main orchestrator + cron entry
    ├── load_brain_state.py          # STATES enum, TRANSITIONS table, guards
    ├── watcher_loop.py              # 60-sec cron loop + anomaly detection
    ├── ollama_anomaly.py            # llama3.1:8b call wrapper
    ├── confidence.py                # scoring math
    ├── action_allowlist.py          # per-state allowed autonomous actions
    ├── escalation.py                # writes to operator_decisions_queue
    ├── prompts/
    │   ├── anomaly_check_v1.txt
    │   └── README.md
    └── tests/
        ├── test_state_machine.py
        ├── test_transitions.py
        ├── test_anomaly_dry.py
        └── test_replay_backfill.py
```

---

## 9. Build sequence + estimated effort

| Phase | Deliverable | Effort | Gate |
|---|---|---|---|
| 1a | `load_brain_state.py` — enum + transition table + guards | 4h | Unit tests pass on every transition |
| 1b | `load_brain.py` orchestrator + `recompute_brain()` | 6h | Can compute state for a real load from db.milestones |
| 1c | `watcher_loop.py` cron at 60s | 4h | Logs anomaly check on all active loads |
| 1d | `ollama_anomaly.py` + prompt v1 + JSON parse | 6h | Returns valid status dict on 10 sample loads |
| 1e | Event hooks added to 4 existing modules | 4h | Brain recomputes within 5s of event |
| 1f | `escalation.py` + db.operator_decisions_queue stub | 3h | Exception writes a queue row |
| 1g | Backfill replay on 200 historical loads | 4h | Brain infers correct state for >90% of them |
| 1h | Dry-run config + 24h burn-in observation | 24h | Operator reviews findings |
| 1i | Integration tests + dry-run mode validated | 6h | All tests green |
| 1j | Commit + push + memory file written | 1h | Future sessions can resume |

**Total: ~38 hours of dev work + 24h burn-in observation = ~5 days calendar, ~3 days actual coding.**

---

## 10. What this does NOT do (anti-scope-creep)

- Does NOT replace `milestones.py`, `carrier_outreach.py`, `pnl_monitor.py`, or `dutch_auction_dispatcher.py`. Brain READS from them, writes opinions, hooks back via `recompute_brain()`.
- Does NOT do pricing. That's pricing_brain (Phase 4).
- Does NOT do per-carrier trust math. That's carrier_trust_score (Phase 2).
- Does NOT do shipper conversation. That's customer_brain (Phase 5).
- Does NOT have a UI. Frontend integration is Phase 6 (operator_decisions_queue endpoint).

---

## 11. Dependencies

**Pre-existing (verified 2026-05-20):**
- `backend/milestones.py` ✓
- `backend/carrier_outreach.py` ✓
- `backend/dutch_auction_dispatcher.py` ✓
- `backend/pnl_monitor.py` ✓
- `backend/carrier_vetting.py` ✓
- Ollama with `llama3.1:8b` warm ✓ (see [[feedback-ollama-model-swap-bug-2026-05-15]])
- MongoDB `db.loads` collection ✓

**New collections to create:**
- `db.load_brain_history` (append-only audit)
- `db.operator_decisions_queue` (Phase 6 stub)
- `db.brain_config` (single global doc with mode)

**Cron registration:**
- `brain.watcher_loop` — every 60s, must be added to the scheduler

---

## 12. Risk register

| Risk | Likelihood | Mitigation |
|---|---|---|
| Brain misfires on edge case → wrong autonomous action | HIGH initially | Dry-run mode 30 days; action allowlist; confidence threshold |
| Ollama hallucinated "exception" → noisy operator queue | MEDIUM | Severity filter; require deterministic flag OR ai_score > 0.8 |
| Polling 60s × N active loads → CPU pressure | MEDIUM | Batch in 100s; offload to llama3.1:8b not 70B for routine checks |
| State machine missing a transition → load stuck in dead state | MEDIUM | Backfill replay catches; nightly orphan-detector cron |
| Brain version drift → old loads have stale brain shape | LOW | brain_version stamped; migration helper for older docs |
| Conflict with operator manual override | MEDIUM | Operator override sets `brain.frozen=true` on that load until cleared |

---

## 13. Cross-references

- Builds on: [[load_brain_spec_2026_05_20]] (this doc — root)
- Consumed by:
  - `carrier_trust_score_spec_TBD` (Phase 2)
  - `lane_memory_spec_TBD` (Phase 3)
  - `pricing_brain_spec_TBD` (Phase 4)
  - `customer_brain_spec_TBD` (Phase 5)
  - `operator_decisions_queue_spec_TBD` (Phase 6)
- Reads from (no modification):
  - `backend/milestones.py`
  - `backend/carrier_outreach.py` (event emit only)
  - `backend/dutch_auction_dispatcher.py` (event emit only)
- Respects:
  - [[no_load_harvesting_double_brokering]] — brain NEVER ingests external load-board loads as our own
  - [[risk_mitigation_umbrella]] — commit each sub-phase as it lands
  - [[always_go_june1_deadline]] — burn-mode build, no permission asking on in-scope items
  - [[local_ai_first_no_claude_api_default]] — Ollama for brain reasoning, NOT Claude API
  - SYSTEM_MATRIX L8 — secret values never in brain prompts or logs
  - SYSTEM_MATRIX L9 — pnl_monitor floor is absolute, brain cannot override

---

## 14. Anti-drift checklist for future sessions

Before modifying brain, future-Claude MUST:
1. Read this doc fully.
2. Read `backend/brain/__init__.py` to confirm current public surface.
3. Check `db.brain_config.global.mode` — if `live`, changes have blast radius. Bump to `shadow` for testing.
4. Bump `brain_version` if schema changes.
5. Write the new spec doc with bumped date suffix.
6. Update MEMORY.md index entry.

**If you find yourself rewriting parts of this spec rather than extending: STOP. Either you're missing context, or the spec is wrong. Ask operator before scrapping.**

---

## 15. Operator approval log

- 2026-05-20 — Jason Meyer approved the design + said "Do it. Get it done please" — burn-mode build authorized.

---

End of spec. Status: **ready to build.**
