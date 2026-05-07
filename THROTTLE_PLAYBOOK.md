# THROTTLE PLAYBOOK

**Iter 140.1 — Operator's Guide to the Autonomous Throttle System**

Last updated: 2026-05-06 (scaffold draft, refine after stage 1d failure-sim run)

This playbook is for the **on-call operator** (Jason) when CHL's autonomous
throttle changes state. The throttle exists for a single purpose: **prevent a
single overloaded broker from being buried by 100 simultaneously-failing
loads when an upstream service breaks**.

---

## TL;DR — What you need to know in 30 seconds

| State | Intake | Operator action |
|---|---|---|
| 🟢 GREEN | 100% | None. System is healthy. |
| 🟡 YELLOW | 50% | Read the email, glance at root cause, no action required unless sustained >1h. |
| 🟠 ORANGE | 10% | Read the email immediately. Plan to investigate within ~30 min. |
| 🔴 RED | 0% | **SMS just hit your phone.** Manual mode only. Drop everything else. |

Auto-recovery: **15 min of stable metrics** = the throttle walks back one level toward GREEN. It never skips levels — you'll see RED → ORANGE → YELLOW → GREEN, each with a 15-min stability gate.

---

## State definitions in detail

### 🟢 GREEN — normal operation
- All health checks pass.
- Error rate < 5%, p95 latency < 5s, all critical modules `healthy`.
- Auto-accept and auto-dispatch run at full speed.
- No alerts sent.

### 🟡 YELLOW — mild degradation
- One of: error rate ≥ 5%, p95 latency ≥ 5s, ≥ 1 module `unhealthy`, or any SLA missing on > 20% of recent calls.
- **Email** sent to `jasonandpugee@gmail.com` (no SMS — not urgent).
- 50% sampled intake. Half of incoming loads enter manual queue.
- Goal: detect deterioration BEFORE it becomes ORANGE.

### 🟠 ORANGE — significant degradation
- One of: error rate ≥ 10%, p95 latency ≥ 10s, ≥ 3 modules `unhealthy`.
- **Email** sent. Subject: `[CHL Throttle] YELLOW → ORANGE`.
- 10% sampled intake. Most loads go manual.
- Action threshold: if it doesn't auto-recover within 30 min, log into `/api/throttle/status`, identify the failing module, and decide.

### 🔴 RED — unsafe / hands-on-wheel only
- Triggered by ANY of:
  - Error rate ≥ 20%
  - ≥ 5 modules `unhealthy`
  - A **critical module** (`database`, `auth`) reports `unhealthy`
- **SMS** sent to operator phone (env: `THROTTLE_ALERT_SMS`).
- 0% intake. ALL inbound loads route to manual queue.
- This is "safety brake fully engaged." Auto-pilot is off.

---

## What triggers state changes (specific thresholds)

These are the exact thresholds in `throttle_system.py`. They are env-overridable:

| Trigger | YELLOW | ORANGE | RED |
|---|---|---|---|
| `error_rate` (1h rolling) | ≥ 5% | ≥ 10% | ≥ 20% |
| `unhealthy_module_count` | ≥ 1 | ≥ 3 | ≥ 5 |
| `p95_latency_ms` | ≥ 5,000 | ≥ 10,000 | — |
| `sla_miss_rate_max` | ≥ 20% | — | — |
| Critical module unhealthy | — | — | **immediate** |

Critical modules (any one unhealthy → instant RED):
- `database` — Mongo connection
- `auth` — login/token layer

Error rate is computed over a rolling 1-hour window of operations
recorded by `throttle_integration.report_operation_result()`. If
< 20 ops have run in the last hour, all rates are reported as 0.0
to avoid noise in low-traffic periods.

---

## Expected alerts (when, what, how)

### Email alerts (Resend)
- **From:** `dispatch@continentalhaul.com`
- **To:** `jasonandpugee@gmail.com` (env: `THROTTLE_ALERT_EMAIL`)
- **Subject:** `[CHL Throttle] <FROM_STATE> → <TO_STATE>`
- **Body:** transition reason, intake percentage, top-3 unhealthy modules, link to `/api/throttle/status?force=true`
- **Sent on:** any transition INTO YELLOW, ORANGE, or RED (worsening only)
- **NOT sent on:** auto-recovery transitions toward GREEN
- **Daily digest** (sustained non-GREEN > 24h): single summary email

### SMS alerts (Twilio)
- **To:** operator phone (env: `THROTTLE_ALERT_SMS`)
- **When:** ONLY on transitions INTO RED
- **Body:** ≤ 160 chars; format `CHL THROTTLE → RED. Intake=0%. Reason: <one-line reason>`
- **Rate limit:** 1 SMS per 30 min (avoid storm if state flaps)

If you don't see the email/SMS but the throttle state shows degraded:
1. Check Resend/Twilio dashboards for delivery failure
2. Check `db.throttle_state_log` for the transition record
3. The transition still happened; alert delivery is best-effort

---

## How to diagnose a degradation

When you see the alert, do this in order:

```bash
# 1. Snapshot current state
curl -s "$BACKEND/api/throttle/status" -b cookies.txt | jq .

# 2. Find the unhealthy module(s)
curl -s "$BACKEND/api/health/system" -b cookies.txt \
  | jq '.modules | to_entries | map(select(.value.status != "healthy"))'

# 3. Recent transitions (what reasons are recurring?)
curl -s "$BACKEND/api/throttle/history?limit=20" -b cookies.txt | jq '.transitions'

# 4. Worst SLA offender
curl -s "$BACKEND/api/sla/summary" -b cookies.txt \
  | jq '{worst: .worst_operation, rate: .worst_miss_rate}'
```

The combination of those four reads tells you:
- WHERE: which module + which SLA
- WHEN: when did it start (timestamp on first transition)
- WHY: the reason string from the transition
- HOW BAD: percentile latencies, error rates

---

## When to intervene manually vs trust auto-recovery

**Trust auto-recovery if** any of these are true:
- It's an outage on a 3rd-party API (OpenAI 529, Anthropic overload,
  Twilio degraded). They typically recover within 15–60 min.
- It's a SLA miss spike during a known traffic surge.
- You see the throttle history showing it's already stepping down
  toward GREEN (auto-recovery is working).

**Intervene manually if** any of these are true:
- 🔴 RED state > 30 min with no progress.
- Critical module (`database`, `auth`) unhealthy and not self-healing.
- Multiple non-GREEN transitions within 1 hour (flapping = root cause unclear).
- The same module is causing degradation on consecutive days (latent issue).

---

## Manual override — `POST /api/throttle/override`

Force the throttle to a specific state. Auth: owner only.

```bash
# Force GREEN (re-enable full intake) — emergency unblock
curl -X POST "$BACKEND/api/throttle/override" \
  -b cookies.txt -H "Content-Type: application/json" \
  -d '{"target_state":"GREEN","reason":"manual unblock after fix","duration_min":60}'

# Force RED (manual-only mode) — operator stepping away
curl -X POST "$BACKEND/api/throttle/override" \
  -b cookies.txt -H "Content-Type: application/json" \
  -d '{"target_state":"RED","reason":"operator off-shift","duration_min":480}'

# Clear override (return to auto-mode)
curl -X DELETE "$BACKEND/api/throttle/override" -b cookies.txt
```

`duration_min` (1–1440) auto-expires the override and returns control
to the auto-evaluator. **Always set a duration.** A forgotten override
is the worst kind of bug — the throttle silently stops doing its job.

---

## Recovery SOP

### Normal case (3rd-party outage, transient spike)
1. Check `/api/throttle/status`. Confirm it's degrading auto-correctly.
2. **Wait 15 min.** Do other work. Don't override.
3. Re-check. If state has stepped one level toward GREEN, system is recovering. Walk away.
4. If still degraded after 30 min, escalate to "Emergency case."

### Emergency case (critical module down, persistent failure)
1. Identify the failing module from `/api/health/system`.
2. Fix the root cause:
   - Auth broken → check `seed_admin()` env sync
   - DB unreachable → check Mongo, restart if needed
   - LLM key invalid → rotate via Universal Key dashboard
3. Restart backend if needed: `Restart-Service CHL-Backend` (admin shell).
4. Wait for `/api/health/system` to return `overall_status=healthy`.
5. Force GREEN with a short-duration override:
   ```
   {"target_state":"GREEN","reason":"post-fix manual recovery","duration_min":15}
   ```
6. Watch `/api/throttle/status` for 15 min — if it doesn't bounce back to non-GREEN, root cause was successful.

---

## Dashboard interpretation — `/api/throttle/status`

```jsonc
{
  "current_state": "YELLOW",          // GREEN | YELLOW | ORANGE | RED
  "intake_percentage": 50,             // 100 | 50 | 10 | 0
  "state_since": "2026-05-06T18:42Z",  // when this state was entered
  "last_evaluation_at": "...",         // most recent eval tick
  "metrics": {
    "error_rate": 0.07,                // 7% — over YELLOW threshold (5%)
    "p50_latency_ms": 480,
    "p95_latency_ms": 5200,            // over YELLOW threshold (5000)
    "p99_latency_ms": 9100,
    "unhealthy_module_count": 0,
    "degraded_module_count": 1,        // soft warning
    "total_operations_1h": 142,
    "sla_miss_rate_max": 0.08
  },
  "recent_transitions": [...],         // up to 100 entries
  "auto_recovery_eligible_at": "2026-05-06T18:57Z",
                                       // when stable-window completes for next step-down
  "manual_override_active": false      // is /override locking the state?
}
```

---

## Historical analysis — `/api/throttle/history`

Patterns to look for in `recent_transitions`:

- **Repeated same-cause transitions** ("err=8.2% unhealthy=1 p95=5400ms" 5 times in 24h):
  the same root cause keeps tripping us. Worth a code-level fix.

- **Flap pattern** (GREEN→YELLOW→GREEN→YELLOW within 1 hour): noisy
  thresholds. Either traffic is bursty or a margin needs widening.

- **Climb-and-stay** (GREEN → YELLOW → ORANGE in 30 min, no recovery
  for 2h): real outage. Auto-recovery won't help; investigate.

---

## Common scenarios (live runbook)

### "OpenAI 529 errors spiking"
**Symptom:** YELLOW state, `rfq_parse` SLA miss rate > 20%, error rate ~8%
**Expected behavior:** auto-recovers in 15–60 min as OpenAI capacity returns
**Action:** none. If still YELLOW after 1h, manually verify on
[status.openai.com](https://status.openai.com) and escalate.

### "Database maintenance window"
**Symptom:** ORANGE state, `database` health check `unhealthy`, then RED
**Expected behavior:** instant ORANGE → RED (database is critical)
**Action:**
1. Confirm maintenance window is in progress
2. Force RED override with `duration_min` matching maintenance window
3. After maintenance ends, force GREEN override `duration_min=15`

### "Deployment introduced bug"
**Symptom:** RED within minutes of deploy, `unhealthy_module_count > 5`
**Expected behavior:** RED stays
**Action:**
1. **Roll back deploy first.**
2. Wait for `/api/health/system` to recover.
3. Force GREEN override.

### "Twilio TFV gets rejected mid-day"
**Symptom:** YELLOW, `automations` module `degraded`
**Expected behavior:** YELLOW persists until TFV is resubmitted
**Action:** SMS path is degraded but not failing — auto-quote drafts
still send via email. Resubmit TFV at your convenience; no immediate
override needed.

### "Driver-OOS storm"
**Symptom:** ORANGE state, multiple `failure_detectors` flags fire
**Expected behavior:** correct! The throttle is throttling because
the DOWNSTREAM operator workload would otherwise spike.
**Action:** work the manual queue. The throttle protects you.

---

## Troubleshooting checklist

When throttle is stuck in non-GREEN and you don't know why:

- [ ] Is `/api/health/system` reporting `healthy` overall?
- [ ] Is `/api/sla/summary.worst_miss_rate` < 5%?
- [ ] Is `/api/throttle/status.metrics.error_rate` < 5%?
- [ ] Is `/api/throttle/status.metrics.p95_latency_ms` < 5000?
- [ ] Is `/api/throttle/status.manual_override_active` `false`?
- [ ] Has the recovery window timer (`auto_recovery_eligible_at`) actually passed?

If all six are YES and state is still non-GREEN, there's a real bug
in the throttle. File it under iter `140.x` follow-on.

---

## Integration with existing failure_detectors

The 12 failure detectors (`failure_detectors.py` + `_part2.py`) feed
the throttle two ways:

1. **Critical events** (`level=critical` agent_journal rows):
   When `cron_failure_detectors_part2._tick_authority_lost` finds the
   FMCSA authority revoked, it calls
   `throttle_integration.trigger_throttle_check("authority_lost")`.
   This wakes the throttle loop NOW (instead of waiting up to 60s).

2. **Module health**: each detector loop reports its `last_tick_at` to
   `health_monitor.register_health_check()`. A loop that hasn't ticked
   in > 1.5× its expected cadence reports `degraded`. > 3× reports
   `unhealthy`. This feeds the throttle's `unhealthy_module_count`.

You don't have to manage that wiring; it lives inside each detector.

---

## What this playbook does NOT cover

- The detailed bcrypt/auth flow (see `memory/AGENT_HANDOFF.md`)
- Day-1 gates list (see `memory/PRD.md`)
- Synthetic load replay (see `memory/handoff_iter_139_38_*`)
- Mercury banking (deferred to iter 141.x)

If the throttle goes into a state this playbook doesn't describe, do
the safest thing: **force GREEN override with `duration_min=15`,
fix the root cause, then clear the override**. Defensive intervention
is cheap; production outage is expensive.

---

## Change log

| Date | Iter | Change |
|---|---|---|
| 2026-05-06 | 140.1 | Initial playbook scaffolded for stage 1d |
| 2026-05-06 | 141.1 | Mirrored from `C:\CHL\memory\` to `chl-memory/` for PM Claude reference (per P4 doc audit gap) |
