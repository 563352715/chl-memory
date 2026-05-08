# Platform Integrity Protocols + Failover / Redundancy Plan

**Status:** Captured 2026-05-08 EOD-10. Operator-mandated. FINAL PHASE work — execute when platform is ~feature-complete (~2-4 sessions out).

**Operator framing 2026-05-08 EOD-10 (verbatim):**

> "Also. When we are near completion of the platform, we need to consider protocols that protect the platforms integrity. We have outsourced much of the work that went into the code writign for the platform. Is ther a way you can go through the entire build at completion point and check it for bug or other problems that might cause it to crash later while in use? Also I want you to perform measures that mitigate damages with such possible occurances. Can we make enough duplicates and wire them up for immediate use if the main system we are uing crashes? Does that make sense?"

---

## What this addresses

Operator's three concerns:

1. **Sub-agent code-quality risk** — much of the codebase was written by sub-agents (parallel batch-orchestration); even with verifier-gate catching 9+ HIGH bugs across the run, latent issues could surface in production after hours/days of real load.
2. **Crash mitigation** — when (not if) something goes wrong in production, what limits the blast radius?
3. **Failover / redundancy** — can we run a parallel hot-spare instance that takes over within seconds if the primary crashes?

---

## Component A — Whole-build code audit at completion

**Goal:** before declaring "feature complete," run a comprehensive bug/quality sweep across the entire codebase. Catches the latent issues that individual-batch verifier-gates might have missed (cross-iter integration drift, deprecated patterns, dead code, missing test coverage).

**Sweeps to run:**

1. **Static analysis pass** — pylint / flake8 / mypy across `backend/`. Fix anything ERROR-tier; document anything WARN-tier as accepted-debt.
2. **Dependency audit** — `pip-audit` for known CVEs in installed packages; npm-audit for the React side.
3. **Dead-code sweep** — `vulture` or similar; flag functions/classes/imports that no live code path references. Many sub-agent runs may have left orphaned helpers.
4. **Test coverage audit** — `pytest --cov=backend` to see actual coverage; identify modules <60% coverage and add regression tests.
5. **Cross-module contract sweep** — for each db.<collection>, find all writers + readers + verify schema agreement. Sub-agents working in isolation can drift on field names (we caught one in EOD-7 with body_ref).
6. **Sub-agent verifier replay** — dispatch a code-reviewer sub-agent over each iter's batch retroactively (the EOD-{1..N} batches each got a verifier at ship time, but a fresh review with full-codebase context may find new issues).
7. **Integration smoke** — run the synthetic_load_walkthrough.py 21-stage end-to-end test (Component 1 of `final_phase_acceptance_test_framework.md`). This is the "did we actually ship?" gate.
8. **Manual operator walkthrough** — operator runs every feature via the Pipeline Walkthrough tab + acceptance checklist (Components 3 + 2 of the same framework doc). Surfaces any "looks-correct-in-code-but-feels-wrong-in-UI" issue.

**Effort estimate:** ~1-2 days of dev work for a pre-launch audit; ~2-3h per quarter thereafter.

---

## Component B — Damage mitigation (in-production crash blast-radius limits)

**Already in place (foundation):**
- **observability triad** (client_error_analyzer + system_health_monitor + carriers_freshness_router) detects crashes in code + data
- **anomaly_dispatcher + context_bundler + sub_agent_dispatcher** queue novel anomalies for self-proposed patches (iter 145.1 stages 1a-1c live)
- **patch_proposal trust-gate** ensures sub-agent dispatcher CANNOT auto-mutate carrier-comm creds, server.py, App.js, factor/Mercury/Stripe/billing/payment/ach/invoice paths (verifier-fix C HIGH-1 EOD-6 extended this list to Plivo/Twilio/outreach/voice/text/tollfree/credential/secret/.token)
- **factor_clawback_on_funded** alert-only path in dispute_handler — never auto-reverses funded state (AR-ledger integrity)
- **carrier-comm cooldown via auto_assigner ghost detector** — prevents spam-loops if outreach orchestrator misfires
- **carrier_doc auto-DM tier ladder is non-backfilling** — never sends 3 SMS in 60s if cron paused
- **set_env_var.ps1 atomic-write** — prevents corrupt .env from a partial save

**To build at completion (Component B specific):**

1. **Circuit breakers per integration:**
   - Stripe / Mercury / Plivo / DAT / FMCSA / OpenAI / Anthropic — each gets a per-vendor circuit breaker. After N consecutive failures, vendor is marked degraded; automated calls skip + queue for retry; operator surface alert. Already partial in some modules (Tier-2 LLM has $15/day budget cap); needs unified pattern.

2. **Budget caps + rate-limits everywhere:**
   - LLM total per day: $50 cap across all sub-agent dispatch + Tier-2 classifier + doc_extractor + future analytics
   - SMS / voice per carrier per day: 10 cap (prevents accidental spam)
   - Refresh endpoints already have 60s cooldown (verifier-fix HIGH-2 EOD-10) — extend pattern to all operator-trigger endpoints

3. **Read-only mode toggle:**
   - Single env var `CHL_READ_ONLY=true` puts the platform in read-only mode (UI works, no writes to db, no outbound vendor calls). For panic-stop scenarios.

4. **Database snapshot before risky operations:**
   - Migrations / schema changes / mass updates: take a Mongo dump first to D-drive snapshot. Already have `backup_to_d.ps1` for iter-close ceremony; extend pattern to "any write touching >1000 rows."

5. **Cron pause toggle:**
   - Master-switch to pause ALL crons via env var. For when something is misbehaving and operator needs to stop the world while debugging.

6. **Audit trail completeness:**
   - Every write to db.factor_submissions / db.invoices / db.carriers (assignment_blocked) / db.patch_proposals must have `audit_trail` array of `{actor, action, timestamp, before, after}`. Foundation exists in some modules; sweep + harmonize.

---

## Component C — Failover / redundancy / hot-spare

**Operator's question:** "Can we make enough duplicates and wire them up for immediate use if the main system we are uing crashes?"

**Yes — three tiers of redundancy:**

### Tier 1 — Process-level (fastest recovery, ~seconds)

**Pattern:** NSSM-managed services with auto-restart. Already in place via `CHL-Backend` + `CHL-Frontend` services. NSSM restarts the service automatically when the process crashes. Recovery time: ~3-10 seconds.

**Enhancements:**
- Add a `health-check sidecar` that hits `/api/health/system` every 30s; if status="degraded" persists >5 min, NSSM force-restart.
- Configure NSSM `AppExit` to restart on any non-zero exit code with exponential backoff (1s → 5s → 30s → 5min).

**Effort:** ~1h to wire NSSM config + sidecar.

### Tier 2 — Host-level (~minutes recovery)

**Pattern:** secondary host with mirrored state. When primary fails, DNS/load-balancer flips to secondary.

**Architecture options:**

| Option | RTO | Cost | Complexity |
|--------|-----|------|------------|
| A: Hot-spare on same machine (different port, same disk) | seconds | $0 | low — but loses if disk dies |
| B: Hot-spare on a second physical box (LAN) | minutes | hardware cost | medium — needs Mongo replication |
| C: Cloud hot-spare (AWS / Azure / GCP) | ~15 min DNS | $50-200/mo | high — needs infra |
| D: Cold backup (D-drive snapshots; manual restore) | hours | $0 | low — already have via snapshot_to_d.ps1 |

**Recommendation:** start with A (zero-cost, fast); when revenue justifies, add B; only consider C when scale demands.

**Pattern A implementation:**
- Run a SECOND instance of CHL-Backend on port 8002 in read-only mode (env `CHL_READ_ONLY=true`).
- Backend has a per-request feature flag: when read-only mode is on, all write paths return 503 except a special `/api/_failover/promote` endpoint.
- Operator runs `.\scripts\failover_promote.ps1 -To 8002` which: (a) flips primary to read-only, (b) flips standby to read-write, (c) updates the frontend's API base URL.
- RTO: ~30-60 seconds operator-driven, ~5 seconds if scripted with health-check trigger.

**Effort:** ~1 day. Requires the read-only mode toggle from Component B-3.

### Tier 3 — Data-level (always-on)

**Pattern:** the data is what matters; processes are replaceable.

**Already in place:**
- **C-drive primary** (live working files)
- **D-drive rolling snapshots** via `scripts/snapshot_to_d.ps1` (after every git push)
- **GitHub cloud** mirror for code (CHL repo + chl-memory repo)
- **Mongo dumps** at iter-close ceremony

**Enhancements:**
- **MongoDB replica set:** convert from standalone to a 3-node replica set on the same machine (Mongo handles this natively). If primary node crashes, secondary becomes primary in ~10 seconds. Zero data loss for the last commit.
- **S3 / Backblaze B2 cold backup:** nightly Mongo dump → cloud cold storage. Cheap (~$1-5/month). Recovery from full-disk-loss scenario.
- **Off-site D-drive sibling:** the rolling D-drive snapshots are local; one weekly snapshot to an external HDD or NAS. Off-machine = survives on-machine catastrophic failure.

**Effort:** ~half-day for Mongo replica set; ~2h for S3 nightly backup; ~30min for off-site weekly snapshot.

---

## Sequenced rollout (final-phase order)

When the platform is feature-complete, execute in this order:

1. **Component A.1-A.5** (static analysis + dependency audit + dead-code + coverage + cross-module schema). ~1 day.
2. **Component A.7** (synthetic_load_walkthrough.py). ~half day to inline the body + run.
3. **Component B.1-B.6** (circuit breakers, budgets, read-only toggle, snapshot-before-risky, cron pause, audit trails). ~2 days.
4. **Component C.1** (NSSM hardening + sidecar). ~1h.
5. **Component C.3** (Mongo replica set + S3 nightly + weekly off-site). ~half day.
6. **Component A.6** (sub-agent verifier replay over the whole codebase). ~half day.
7. **Component A.8** (operator manual walkthrough + acceptance checklist). ~1 day with operator.
8. **Component C.2 Pattern A** (in-process hot-spare on port 8002 + failover_promote script). ~1 day.
9. **OPTIONAL:** Pattern B (second physical box) if revenue + scale justify.
10. **OPTIONAL:** Pattern C (cloud hot-spare) — only if Tier 1-2 are not enough.

**Total effort estimate for full Components A + B + C through Pattern A:** ~6-8 dev days. ~1-2 calendar weeks of focused work.

---

## Yes, this all makes sense

Operator's instincts here are sound:
- The "sub-agent code wrote much of this" observation is real — verifier-gate catches a lot but not everything; a whole-build sweep at completion is the right move.
- "Mitigate damages" is well-defined: circuit breakers + budgets + read-only mode + audit trails. Standard SRE patterns.
- "Duplicates wired up for immediate use" is achievable in 3 tiers with increasing cost; Tier 1 (NSSM) is already there; Tier 2 Pattern A (in-process hot-spare) is ~1 day of work and gives sub-minute RTO.

**The only pushback:** don't build all three tiers at v1. Tier 1 + Tier 3 (Mongo replica set + cloud cold backup) cover ~95% of crash scenarios. Tier 2 hot-spare is a "when revenue justifies" upgrade.

---

## Why this doc exists

Operator-mandated 2026-05-08 EOD-10 — this is FINAL PHASE work that without persistent capture risks being "we discussed but never built." Captured per the standing capture-every-feature directive (`feedback_capture_every_feature_to_build_doc.md`).

For the agent picking this up at final-phase: read this doc, the `final_phase_acceptance_test_framework.md`, and the `phase_10_statistical_analytics_and_hardware_plan.md` together. They're the three load-bearing roadmap docs for the post-feature-complete transition to production-hardened.
