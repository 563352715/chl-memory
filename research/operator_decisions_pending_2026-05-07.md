# Operator Decisions Pending — Triage Sheet (as of 2026-05-07 EOD)

> **Purpose:** Single-pane decision surface for solo operator with multi-iter-in-flight cognitive load. Replaces the read-6+-docs cognitive load (PM Claude memory + dev memory + iter 142.1 1c-1f refresh + Phase 6 roadmap + email-ingestion spec + Phase 4 tracking sketch + messaging-stack scaling sketch + Phase 6 Factoring sketch + white-glove packet/checklists) with one ordered table. Every minute spent searching for "what was that decision about" is a minute not spent shipping.
> **Drafted by:** @pm-lead 2026-05-07 EOD-3 per @dev-engineer task brief.
> **Last refresh:** 2026-05-07 EOD-3 (added 7 iter 144.x Phase 6 Factoring decisions per `bfd918d`).

**Counts:** **29 open decisions** + **2 closed today** (resolved or default-applied with operator's tacit approval per autonomous-continue scope). Of the 29 open: **7 BLOCKING** (gate a specific iter/stage), **18 SHIP-AFFECTING** (defaults applied; operator can override), **4 OPTIONAL** (informs prioritization but no immediate ship gate).

**Recommended operator triage flow:** read §1 BLOCKING first (resolve before any new iter starts) → §2 SHIP-AFFECTING (skim defaults; flag any to override) → §3 OPTIONAL (read once, file for later). Quick-decide batches in §4 group decisions that share context.

---

## §1 — BLOCKING decisions (gate a specific iter/stage)

| # | Decision | Source doc | Gates | Default-if-no-action | Operator action | Recommended |
|---|---|---|---|---|---|---|
| 1 | **iter 141.3 PF1 — FMCSA registry cross-check on EIN + business address** | `iter_141_3_stage_1a_paste_ready_checklist.md` §0 | White-glove packet paste (iter 141.3 stage 1a sales-unblock day) | Packet paste with mismatched values → likely TCR rejection (matches Twilio 30909 failure mode) | ~5 min: pull EIN from W-9, business address from FMCSA SAFER MC-1817555 record + state SOS filing; verify packet §1 placeholders match exactly | Do at the moment Plivo sales unblocks (operator already has packet open) |
| 2 | **iter 141.3 PF2 — Plivo temp number capture into packet §6** | `iter_141_3_pf2_temp_number_capture.md` | White-glove packet paste | Packet pasted with `<temp>` unfilled → SE asks for it, ~24h SLA stall | ~1 min: after Plivo console step 10 (provision temp number), copy E.164 → paste into packet §6 row, tick bottom-of-doc checklist | Built into the paste-ready flow; no separate decision |
| 3 | **iter 141.3 M2 — Port `+14172193856` vs new Plivo number** | `iter_141_3_kickoff_checklist.md` item 2 + `plivo_white_glove_review.md` M2 | White-glove packet §6 + §8 content; whether to ship port path or new-number path | Packet ships port path (per packet default) — fine if external paperwork actually cites the existing number | Operator paperwork-audit per kickoff checklist item 2 (may auto-default to NEW NUMBER if paper/scattered per the fallback) | Audit if paperwork is in one searchable place; otherwise default NEW NUMBER (per kickoff fallback). Decide before paste. |
| 4 | **iter 142.1 Phase 3 — FMCSA QCMobile WebKey signup** | `iter_142_1_full.md` Nationwide-DB amendment + `current_status.md` | iter 142.1 Phase 3 insurance ingestion | Phase 3 stays GATED — module-ready but no insurance data flows | ~5 min one-time: Login.gov → FMCSA QCDev portal → "My WebKeys" → "Get a new WebKey" → populate env-var | Do whenever operator has 5 free min; unblocks Phase 3 immediately. Per `bmc84_watcher.fmcsa_webkey: false` audit. |
| 5 | **iter 143.1 — Zoho IMAP app-password provisioning** | `email_ingestion_architecture_spec.md` Sleeper #1 | iter 143.1 stage 1a (IMAP poll cron) | iter 143.1 cannot kick off | ~5 min: Zoho Mail Admin Console → app-specific password → populate `ZOHO_IMAP_PASSWORD` in `backend/.env` | Standard Zoho password won't work for IMAP; required step. |
| 6 | **iter 145.1 — Trust-gate boundary definition** | `phase_6_self_healing_roadmap.md` Sleeper #1 + §3 trust-gate matrix | iter 145.1 stage 1c (trust-gate framework + stub patch-proposer) | Default v1 LOW/MED/HIGH rules apply (LOW=auto-merge for docs/log-strings/threshold-tuning ±10% in non-auth/billing; MED=dev-DM; HIGH=operator-approval for `*auth*`/`*billing*`/`*payment*`/`*.env*`/business-logic core/schema migrations) | ~10 min: review the v1 matrix in stage 1c handoff; flag any files/domains operator wants escalated based on past failure modes | Take the v1 defaults at first; revisit after first 30 days production with self-healing. Override only if a specific file/domain has bitten you historically. |
| 7 | **iter 145.1 — Self-healing IS-NOT-self-deploying explicit acknowledgment** | `phase_6_self_healing_roadmap.md` Sleeper #2 | iter 145.1 build start | Default principle: HIGH-risk patches stop at "proposed + smoke-verified"; operator-in-loop required for production deploy | ~30 sec: explicit "yes, I understand and want this constraint" acknowledgment. (Mental model: self-healing handles boring stuff so operator can focus on hard stuff.) | Acknowledge. The constraint is by-design per pre-revenue-solo posture; loosening pre-revenue is dangerous. |

---

## §2 — SHIP-AFFECTING decisions (defaults applied; operator can override)

| # | Decision | Source doc | Affects | Default applied | Operator action | Recommended |
|---|---|---|---|---|---|---|
| 8 | **iter 1e ghoster-demotion factor** | `iter_142_1_stages_1c_1f_refresh.md` stage 1e Trust-Gate Inputs #2 | iter 142.1 stage 1e re-outreach logic | Scoring-coupled (ghoster's CarrierEvaluator reliability prediction dampened -0.5; demotion auto-flows through composite) | ~30 sec pre-1e ship: confirm scoring-coupled, OR override to "explicit operator-tuned blacklist promotion after N ghosting events" | Take scoring-coupled default. Explicit blacklist creates more operator-touch + slower learning; let the FractalEvaluator drive it. |
| 9 | **iter 142.1 Q5 — DAT vs Truckstop load-board posting integration** | `iter_142_1_full.md` amendment open Q5 | iter 142.1 stage 1d load-board fallback | DAT only at v1 (pre-existing CHL integration in iter 141.2 stage 1d) | ~30 sec pre-1d kickoff: confirm DAT-only OR add Truckstop if relationships exist | Take default DAT-only. Truckstop adds complexity for limited reach gain at v1. |
| 10 | **iter 142.1 Q6 — BCA template source** | `iter_142_1_full.md` amendment open Q6 | iter 142.1 stage 1b vetting workflow (BCA emit on vetting-pass) | Industry-standard (TIA-published template, ~$0; attorney review optional, not blocking) | ~5 min pre-1b kickoff: download TIA template OR substitute operator's preferred (operator-authored / attorney-drafted) | Take TIA default. Attorney review can happen async post-launch; not blocking. |
| 11 | **iter 142.1 stage 1c — CarrierEvaluator composite-score weights** | `iter_142_1_stages_1c_1f_refresh.md` stage 1c "What operator does" | iter 142.1 stage 1c scoring math | profitability 0.35, reliability 0.30, risk 0.20, capacity 0.15 | ~5 min pre-1c kickoff: confirm or tune (operator may want reliability-heavier given pre-revenue carrier-quality sensitivity) | Take default initially; iter 145.1 re-tuning cron will propose data-driven adjustments. |
| 12 | **iter 143.1 — Tier 2 LLM classifier budget cap** | `email_ingestion_architecture_spec.md` Sleeper #2 | iter 143.1 ongoing classifier cost | $2.50/day = 500 calls (operator-tunable lower; e.g., $1/day = 200 calls) | ~30 sec: confirm or dial down | Start lower at v1 (e.g., $1/day = 200 calls) — easier to dial up if hitting cap; harder to walk back if classifier-spend feels too high. |
| 13 | **iter 143.1 — Vendor account forwarding to single polled mailbox** | `email_ingestion_architecture_spec.md` Sleeper #3 | iter 143.1 hook coverage (multi-account ingestion is post-v1) | Polled mailbox = `dispatch@continentalhaul.com` only; no forwarding configured | ~10 min if needed: Zoho mail-routing rules to forward `accounting@`, `dispatch.team@`, etc. → `dispatch@` | Do if operator has multiple business email addresses receiving operationally-relevant mail. Skip if single-mailbox is sufficient. |
| 14 | **iter 145.1 — Sub-agent token budget per-day cap** | `phase_6_self_healing_roadmap.md` Sleeper #4 | iter 145.1 anomaly-dispatcher cost ceiling | 50 dispatches/day × ~$0.30 each = ~$15/day max anomaly-cost | ~30 sec: confirm $15/day, OR tighten to 25/day (~$7.50/day) at cost of slower autonomous response on incident-rich days | Confirm $15/day initially; revisit after first month. Hard cap somewhere is critical (defense against runaway loop). |
| 15 | **iter 142.2 Phase 4 — GPS provider primary source** | `iter_142_2_phase_4_tracking_sketch.md` open question 1 | iter 142.2 stage 1a GPS integration | Carrier-side mobile-app at v1 (lowest carrier-side adoption friction; defer fleet-ELD to v3+ when broker pricing supports per-truck cost) | ~10 min pre-iter-142.2 kickoff: confirm mobile-app default OR pick fleet-ELD vendor (Samsara/Geotab) | Take mobile-app default. Validate against actual carrier-relationship feedback before kickoff. |
| 16 | **iter 142.2 Phase 4 — Exception severity thresholds** | `iter_142_2_phase_4_tracking_sketch.md` open question 2 | iter 142.2 stage 1b rule engine | late >30min = warn / >2h = critical; stopped >2h = warn / >4h = critical; off-route >25mi = warn | ~5 min pre-iter-142.2: confirm or tune | Take defaults; iter 145.1 outcome-feedback re-tunes from operator-override patterns over time. |
| 17 | **iter 142.2 Phase 4 — Self-healing automation scope** | `iter_142_2_phase_4_tracking_sketch.md` open question 3 | iter 142.2 stage 1c shipper-notification + ETA-recalc behavior | Auto-notify shipper for ETA delays >2h; operator-in-loop for shorter delays | ~5 min pre-iter-142.2: confirm or tighten | Take default; loosen later if false-positive rate is acceptable. |
| 18 | **iter 1f packet storage — local-disk vs S3** | `iter_142_1_full.md` Q3 + `iter_142_1_stages_1c_1f_refresh.md` stage 1f | iter 142.1 stage 1f packet delivery | Local disk at v1 (~50 packets/day expected); S3 deferred | ~30 sec: confirm local-disk OR pre-decide S3 (only if multi-broker scale lands pre-iter-145) | Take local-disk default. S3 is +0.5 day dev for marginal v1 benefit. |
| 19 | **iter 144.x — Which 2-3 additional factor providers beyond RTS/OTR** | `iter_144_x_phase_6_factoring_sketch.md` Open Question #1 | iter 144.x stage 1a stub-factor completion | None — stage 1a needs operator to pick 2-3 from {TBS, HaulPay, Compass, Single Point, Riviera, Porter} | ~10 min pre-iter-144.x: audit top 20 carriers in db.carriers post-Phase-1-ingest for `factor_provider` distribution; pick the 2-3 most common | Audit-driven; cluster-by-carrier-segment heuristic (small owner-ops favor HaulPay/Compass; mid-size fleets favor TBS/Single Point) |
| 20 | **iter 144.x — Auto-submission trust-gate posture** | `iter_144_x_phase_6_factoring_sketch.md` Open Question #2 | iter 144.x stage 1b auto-submission pipeline | First-10-operator-approve, then auto-fire if no operator-rejections | ~30 sec: confirm or override to all-auto-fire (more aggressive) OR always-operator-approve (more conservative) | Take default first-10-mode. Pre-revenue-solo posture; promote to all-auto-fire after 30-day clean track record. |
| 21 | **iter 144.x — Dispute auto-respond boundary** | `iter_144_x_phase_6_factoring_sketch.md` Open Question #3 | iter 144.x stage 1d dispute classifier | Auto-respond ONLY for `pod_missing` + `duplicate_submission` Tier 1 cases; all others operator-queue | ~30 sec: confirm conservative default OR expand auto-respond set | Take default. Sleeper concern: getting this wrong = losing factor relationships. Conservative posture is correct for v1; expand post-30-day track record. |
| 22 | **iter 144.x — Multi-factor scenarios (carrier changes mid-haul)** | `iter_144_x_phase_6_factoring_sketch.md` Open Question #4 | iter 144.x stage 1b auto-submission routing | Queue-as-exception (operator decides per-load) | ~30 sec: confirm queue-as-exception OR override to detect-and-redirect (more aggressive) | Take queue-as-exception at v1 (low frequency, high stakes). Detect-and-redirect post-30-day-track-record. |
| 23 | **iter 144.x — Factor haircut visibility in dispatch packet pricing** | `iter_144_x_phase_6_factoring_sketch.md` Open Question #5 | iter 144.x stage 1f operator-internal margin reports | Hidden in dispatch packet (operator's net-net is what matters); visible in operator-internal margin reports | ~30 sec: confirm hidden OR override to surface-in-packet | Take default hidden. Surfacing creates noise; net-net is operator's signal. |
| 24 | **iter 144.x — Quick-pay opt-in default** | `iter_144_x_phase_6_factoring_sketch.md` Open Question #6 | iter 144.x stage 1b per-load submission flow | Opt-in for factors that support quick-pay (Apex, Triumph, RTS) — faster cash, ~1% extra haircut | ~30 sec: confirm opt-in OR override to opt-out per-carrier or globally | Take opt-in default at v1. Cash-flow > marginal cost at pre-revenue scale. Operator can override per-load or per-carrier. |
| 25 | **iter 144.x — Factor-portal poll cadence** | `iter_144_x_phase_6_factoring_sketch.md` Open Question #7 | iter 144.x stage 1c reconciliation polling cost | 6h cadence | ~30 sec: confirm or tighten/loosen | Take 6h default. Tighter (1h) adds API call cost; looser (24h) delays cash-confirmation. Revisit if specific factors charge per-API-call. |

---

## §3 — OPTIONAL decisions (informs prioritization, no immediate ship gate)

| # | Decision | Source doc | Why optional | Operator action | Recommended |
|---|---|---|---|---|---|
| 26 | **5 strategic-context calibration questions** (specific shippers / runway / no-list / Phase 6+ roadmap fuzzy / narrative) | dev posted to operator earlier today | Informs prioritization across all iters; no near-term ship gate | Operator answers when convenient; influences whether iter 145.x or iter 143.x or iter 144.x gets prioritized | Answer at next 10-min reflection window. |
| 27 | **BOL/POD email-reply header preservation pre-validation** | `email_ingestion_architecture_spec.md` Sleeper #4 | iter 143.1 hook §4 "customer_load_reply with BOL/POD" depends on `References:` header surviving carrier reply; doesn't gate ship but determines hook reliability | ~5 min: forward 1-2 sample carrier replies to dev for header inspection | Pre-validate with 1-2 actual carriers; if headers strip, fall-back to subject-line load-id parsing. Doesn't gate iter 143.1 ship. |
| 28 | **Outcome-data privacy at multi-broker scale** | `phase_6_self_healing_roadmap.md` Sleeper #3 | iter 145.1 v1 acceptable at single-broker (operator owns data + carrier consent via signed BCA); needed at multi-broker SaaS scale | Pre-iter-145.1 v2: add "carrier behavioral data sharing" clause to BCA template (Q6 default = TIA-published industry-standard) | PM-side can draft the clause as follow-up task when multi-broker roadmap activates. No action at v1. |
| 29 | **Classifier-drift monitoring guardrail** | `email_ingestion_architecture_spec.md` Sleeper #5 | iter 145.1 dispatcher should monitor `unclassified-rate` as Detection-Layer signal; without this, classifier-drift could go silently for weeks | Wire up post-iter-143.1 ship; no-op until iter 145.1 ships | Defer until iter 145.1 stage 1a (anomaly dispatcher) is built. |

---

## §4 — Quick-decide batches (related decisions, group review)

**Batch A — iter 141.3 white-glove paste day (decisions #1-3):** all surface at the moment Plivo sales unblocks signup. Operator has the paste-ready checklist (`iter_141_3_stage_1a_paste_ready_checklist.md`) which sequences these in order. **Estimated time: 5-10 min batched.**

**Batch B — iter 142.1 stages 1c-1f kickoff (decisions #8, #9, #10, #11, #18):** all relevant when operator authorizes 1c→1f sequence to start (recommend ship 1c first per `iter_142_1_stages_1c_1f_refresh.md` sequencing rec). **Estimated time: ~10 min batched** (mostly default-confirm).

**Batch C — iter 145.1 self-healing kickoff (decisions #6, #7, #14):** trust-gate matrix + self-healing-not-self-deploying acknowledgment + sub-agent budget cap. All needed before stage 1c (trust-gate framework). **Estimated time: ~15 min batched** (longest review on trust-gate matrix per #6 since operator may want to add files/domains).

**Batch D — iter 143.1 email-ingestion kickoff (decisions #5, #12, #13):** Zoho password + Tier 2 budget + multi-account forwarding. **Estimated time: ~15-20 min batched** (Zoho password gen + budget confirm + optional forwarding rules setup).

**Batch E — iter 142.2 Phase 4 tracking kickoff (decisions #15, #16, #17):** GPS provider + exception thresholds + self-healing automation scope. Probably a Q3-2026 conversation given iter 141.3/141.2/142.1/143.1/145.1 stack ahead. **Estimated time: ~20 min batched** when activated.

**Batch F — iter 144.x Phase 6 Factoring kickoff (decisions #19-25):** factor-provider selection + auto-submission posture + dispute-respond boundary + multi-factor handling + haircut visibility + quick-pay default + portal poll cadence. Most are default-confirms; #19 (which factors) is the one that needs operator-audit pre-decision. **Estimated time: ~15-20 min batched** (longest review on #19 audit; rest are ~30-sec defaults).

**Forward-looking only (no batch — read once, file):** decisions #26-29 are informational / pre-validation / multi-broker future. Worth one read; no action required for any near-term iter.

---

## §5 — Closed decisions log (today, 2026-05-07)

| # | Decision | Resolution | Commit | Notes |
|---|---|---|---|---|
| C1 | **Stale `localhost:8000` references in `agenda/stages/stage_1a.md`** | Port-correction callout added at top of doc per post-iter-141.1-close verification (port 8001) | `521e55c` | Matches close-ceremony runbook callout pattern from `9d6c5f5`; historical PowerShell recipes preserved for audit-trail. |
| C2 | **iter 141.3 Plivo signup decision tree** (original was role-based-email diagnostic) | Superseded by sales@plivo.com escalation pivot after vendor-side domain-block confirmed | (in `iter_141_3_stage_1a_signup_troubleshooting.md` §1 corrections, commit `925410a`) | Original alias-not-existing-yet hypothesis ruled out empirically (incognito + alias-verified); domain-level vendor block is the actual cause. |

---

## §6 — Next-update trigger

This doc gets refreshed when:
- **Any open decision is resolved** → strikethrough that row in §1/§2/§3 + add to §5 closed-decisions log with resolution + commit ref
- **A new iter ships docs that introduce decisions** → audit-pass + append to triage table
- **Operator surfaces a new decision** that didn't fit an existing source doc → append to triage table with `Source doc: AgentDM thread {message_id}` reference
- **Weekly stale-prune cadence** (every Friday): scan for decisions whose context has shifted (e.g., dependent iter shipped or deferred) + reclassify or close

**Recommended PM-side cadence:** refresh this doc as part of every substantive autonomous-continue cycle (don't let it drift more than 24h). Operator-facing reading of this doc is the highest-leverage friction-reducer for solo operator with multi-iter cognitive load.

---

**Status:** ✅ TRIAGE COMPLETE. **29 open** (7 BLOCKING + 18 SHIP-AFFECTING + 4 OPTIONAL); 2 closed today (`521e55c` + `925410a`). Operator-recommended triage flow: §1 first → §2 skim → §3 file. §4 batches help knock out groups of related decisions in one read.

**Source:** Drafted by @pm-lead 2026-05-07 EOD-3 per @dev-engineer task brief. Cross-references: every row in the table cites its source doc + section explicitly so operator can drill into full context if a one-line summary isn't enough.
