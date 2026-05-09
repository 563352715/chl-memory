# NORTH STAR BUILD PLAN — CHL platform autonomy roadmap

**Operator-mandated 2026-05-09:** This is the load-bearing build plan until autonomous functionality is proven. New features are out-of-scope until we close the autonomy gap. After autonomy is proven, this doc gets retired and we move to "improvement mode."

**Source documents (read these for full detail):**
- `chl-memory/research/broker_workflow_canonical_reference_operator_2026_05_09.md` — operator's canonical 5-phase reference. Gold-standard for "what CHL must do."
- `chl-memory/research/chl_capability_map_2026_05_09.md` — 15-stage capability audit.
- `chl-memory/research/broker_operational_workflow_research_2026_05_09.md` — supporting industry research.
- `~/.claude/projects/c--CHL/memory/project_may_14_platform_readiness_deadline.md` — May 14 milestone.

---

## Where we are: ~55-60% autonomous

A load CAN technically traverse RFQ → cash through our code, but operator must touch it at 3-4 checkpoints. The operator's canonical reference exposed 10 gaps the prior audit missed (NOA detection, auth-age check, phone-FMCSA cross-check, schedule-of-accounts batching, factor reserve-release tracking, etc.).

## Where we're going: ~95% autonomous (asymptote)

The remaining ~5% is structurally non-automatable (see end of doc). Closing 0-95% is software work.

---

## Three sequential phases

### PHASE A — Platform Readiness (deadline: 2026-05-14)

**Goal:** what's already shipped actually runs in production.

- Restart-verify the 27-commit chain ZZ2/AAA1/XX1-3/CRON 21-26 startup hooks.
- Run end-to-end critical-path smoke against synthetic load.
- Operator-side: re-upload v2 toll-free LoA + provision TELNYX_FROM_NUMBER + TELNYX_TOLLFREE_NUMBER + run seed_renewals + prune/rotate scripts.
- Platform must not be the bottleneck on May 14 when phone numbers go live.

**No new code work in this phase except critical fixes.** Runbook: `chl-memory/operations/may_14_platform_readiness_runbook.md` (CCC1, 430 lines).

---

### PHASE B — Close the autonomy gap (May 15 → ~July 1)

Three sub-phases by effort, prioritized by **fraud-risk + cash-impact × ease**:

#### B.1 — Quick-win fixes (Week 1 post-readiness, ~3-5 days, jumps to ~75%)

All small effort. Each closes a daily operator chore or a fraud exposure.

| # | Fix | Effort | Closes |
|---|---|---|---|
| 1 | **Authority-age check** (<90 days red flag) | Small | Phase 2 fraud filter |
| 2 | **Phone-FMCSA cross-check** (carrier-supplied vs FMCSA-listed) | Small | Phase 2 fraud filter |
| 3 | **NOA detection + payment routing** | Small/Medium | Phase 4 — pay-the-wrong-party loss prevention (#1 broker-fraud loss) |
| 4 | **Auto-send dispatch packet on booking** | Small | Phase 3 daily chore |
| 5 | **Cron settlement trigger 24h post-funding** | Small | Phase 5 cash flow |
| 6 | **AR dunning email cron** (Day 15/22/30 escalation) | Small | Phase 5 revenue recovery |

After B.1: 4 of the 4 daily operator chores collapse. Pay-wrong-party fraud closes. Estimated ~75% autonomous.

#### B.2 — Medium-effort fixes (Weeks 2-3 post-readiness, ~14 days, jumps to ~85%)

| # | Fix | Effort | Closes |
|---|---|---|---|
| 7 | **Pre-quote shipper credit gate** | Medium | Phase 1 — don't quote shippers our factor won't fund |
| 8 | **Recent-inspections lookup** (no inspections = shell-company red flag) | Medium | Phase 2 fraud filter |
| 9 | **Auto-quote-send gate** (configurable margin threshold + shipper_vetted flag) | Medium | Phase 1 — biggest manual checkpoint removed |
| 10 | **SMS-to-carrier-interest bridge** (Telnyx webhook + intent classifier) | Medium | Phase 4 — carriers SMS-reply YES, instant book |
| 11 | **Email-to-carrier-interest bridge** | Medium | Phase 4 — captures cold-outreach replies |
| 12 | **Schedule of Accounts batch submission** | Medium | Phase 5 — factor-portal workflow alignment |
| 13 | **Detention/layover auto-detect** (geofence + stationary timer) | Medium | Phase 3 — detention revenue capture |
| 14 | **Insurance call-the-agent verification** (auto-email or scheduled call) | Medium | Phase 2 — counter to PDF-trust fraud |
| 15 | **SMS POD submission** (MMS webhook listener) | Medium | Phase 4 — driver UX + faster cash |
| 16 | **Counter-offer workflow** (LLM evaluator on shipper counters) | Medium | Phase 1 — auto-respond to negotiation |

After B.2: vetted-shipper × vetted-carrier × margin>20% load flows RFQ → cash without operator touch. Estimated ~85% autonomous.

#### B.3 — Large-effort fixes (Month 2-3, jumps to ~95%)

| # | Fix | Effort | Closes |
|---|---|---|---|
| 17 | **DAT/Truckstop external load-board scraper + auto-rank** | Large | Phase 2 — load discovery is currently absent |
| 18 | **Factor API completion** (Apex/RTS/Triumph/OTR/Mercury real endpoints) | Large per factor | Phase 5 — adapters are stubs today |
| 19 | **Bank API listener** (Mercury/SVB ACH-wire receipt) | Large | Phase 5 — close the cash-cycle visibility loop |
| 20 | **Factor reserve-release / rebate tracking** | Medium/Large | Phase 5 — 3% fee + reserve completeness |
| 21 | **Carrier load-board double-broker monitor** | Large | Fraud — DAT scrape post-book |
| 22 | **CSA / SaferDB inspection integration** | Large | Phase 2 vetting upgrade |
| 23 | **Shipper portal (self-serve RFQ + status)** | Large | Phase 1 — operator-relay-only today |
| 24 | **Carrier portal (claim loads + upload POD)** | Large | Phase 4 — already partially exists in QROnboard.jsx |
| 25 | **Multi-broker offer dedup + shipper identity aggregation** | Large | Phase 1 — anti-shopping |

After B.3: ~95% autonomous. The remaining 5% is structurally human.

---

### PHASE C — Improvement mode (post-95%)

After autonomy is proven, retire this doc. New features welcome. Until then, every dispatch decision asks: "Does this close an autonomy gap from the table above?" If no, defer.

---

## Activities we CANNOT automate (the "so be it" list)

These are structurally human regardless of code. Don't waste effort here.

1. **Sales relationships** — building shipper book of business, attending industry events, partnership negotiations. Human-touch is the product here.
2. **Accessorial dispute resolution** — when shipper claims damage, you need a human on the phone with their claims adjuster. AI can draft, can't argue.
3. **Bond/insurance lapse remediation** — our own BMC-84 bond renewal, MCS-150 biennial filing, FinCEN BOI: requires human signature + state/federal attestation.
4. **Litigation / regulatory enforcement response** — DOT compliance audit, FMCSA inquiry, IRS audit. Lawyer territory.
5. **Phone calls to insurance agents** (Phase 2 verification) — operator's reference explicitly says "CALL the agent." Could partially automate via AI dial + conversation in future, but currently human + structurally edge case.
6. **Bond claim defense** — if a carrier files claim against our $75K BMC-84, we need legal response.
7. **High-judgment carrier disputes** — driver no-show with high-value freight, carrier going dark mid-load. AI escalates, human decides response.
8. **Hardware install / vendor signups** — ELD provider integration setup, factor onboarding (signing API agreements), SAFER WebKey provisioning. One-time human-touch.
9. **Crisis / reputation events** — fraud incident PR, customer escalation to broker authority, social-media negative reviews.

These are ~5% of operator-time at scale; that 5% is the operator's actual job. Everything else, we automate.

---

## Cadence + measurement

- **Weekly progress check:** every Monday, count fixes shipped from this list. Report: "X of 25 closed; estimated autonomy %; next 3 priorities."
- **Autonomy proof bar:** a load runs RFQ → cash without operator touch + audit trail shows zero `actor=operator` events on the critical path. When 10 consecutive loads achieve this for "happy-path" criteria (margin>20%, vetted-shipper, vetted-carrier, no exceptions), declare B.1+B.2 complete.
- **Halting condition:** when autonomy_score ≥ 85% AND 10-consecutive-load test passes AND no 30-day fraud incidents, retire this doc. Move to PHASE C.

---

## What this supersedes / sharpens

- `feedback_autonomous_continue_until_build_complete.md` — "complete the build" now means: close the autonomy gap defined here. NOT add net-new features.
- `project_may_14_platform_readiness_deadline.md` — May 14 is PHASE A only. PHASE B starts May 15.
- The build tree in `chl-memory/progress/current_status.md` — that's a feature-axis view; this is a capability-axis view. Both stay live.

---

## VALIDATION TIER (operator-mandated 2026-05-09)

Per `~/.claude/projects/c--CHL/memory/feedback_validate_findings_before_acting.md`: only act on items where operator's canonical reference AND at least one agent report agree. Defer single-source items until tomorrow's deeper research. Critical fraud-prevention items (NOA, authority-age, phone-FMCSA) are exempt from "wait" rule because cost-of-not-doing >> cost-of-doing.

### TIER 1 — VALIDATED (overlap; act now)

These appear in BOTH operator's canonical reference AND at least one agent report. Proceed.

| # | Fix | Operator ref location | Agent confirmation |
|---|---|---|---|
| 1 | Auto-send dispatch packet on booking | Phase 3 "send specific pickup address + numbers to driver" | Capability audit gap #1 |
| 2 | Cron settlement trigger 24h post-factor-funding | Phase 5 "use these funds to pay carrier's invoice" | Capability audit gap #2 |
| 3 | NOA detection + payment routing | Phase 4 "if carrier factors, NOA tells you to pay their bank not them" | Research agent fraud playbook + capability audit gap |
| 4 | Detention/layover auto-detect | Phase 3 "verify driver arrived, loaded, departed" | Research agent #1 + capability audit gap #4 |
| 5 | POD red-flag auto-escalation | Phase 4 POD collection | Research agent #2 + capability audit gap #8 |
| 6 | Schedule-of-Accounts batch submission | Phase 5 "Submit Schedule of Accounts" | Research agent #4 |
| 7 | Factor decline auto-reroute (test + production-wire) | Phase 5 implied (factor verification) | Research agent #4 + capability audit gap #8 |
| 8 | Per-load carrier re-vetting (auth-age + phone-FMCSA + recent-inspections combined) | Phase 2 "Vetting Checklist" red flags | Research agent #3 (fraud surge $725M, +60% YoY) |

### TIER 2 — CRITICAL-FRAUD EXEMPT (single-source but proceed anyway)

Cost-of-not-doing >> cost-of-doing. Even though only operator-source, fraud exposure is real.

| # | Fix | Operator ref location | Why exempt |
|---|---|---|---|
| 9 | Authority-age check (<90 days) | Phase 2 red flag | Specific filter recommended by canonical source |
| 10 | Phone-FMCSA cross-check | Phase 2 red flag | Specific filter recommended by canonical source |

(These are the components of #8 above — listed separately for engineering tractability.)

### TIER 3 — UNVALIDATED (defer until tomorrow's deeper research)

Single-source items. Hold pending validation. **Don't dispatch yet.**

#### Operator-only (need agent confirmation)

| Item | Reason for hold |
|---|---|
| Shipper anonymization on board posts | Need to confirm anti-back-solicit is actually how brokers post (or if board hides shipper natively) |
| Insurance call-the-agent automation | Hardest to automate; need to validate alternative (auto-email confirmation may suffice) |
| Recent-inspections lookup as standalone | May be redundant if SAFER CSA data covers it |
| Pre-quote shipper credit gate | `factor_credit_lookup.py` may already partially do this — needs audit |

#### Agent-only (need operator confirmation in tomorrow's review)

| Item | Reason for hold |
|---|---|
| SMS-to-carrier-interest bridge | Operator's reference shows web/email; SMS may not match operator's actual carrier-comms preference |
| Email-to-carrier-interest bridge | Same as above |
| SMS POD submission | Operator may prefer driver web/email POD as today |
| Counter-offer workflow | Operator may want all counters to escalate to human; not autonomous |
| DAT/Truckstop external scraper | Large effort; operator may prefer to keep load-source = inbound-only initially |
| Carrier load-board fraud monitor | Same as above — depends on whether DAT integration goes live |
| CSA / SaferDB integration | Large effort; may overlap with operator's "recent-inspections" item |
| Bank API listener for shipper payment | **REMOVED — `we factor our invoices` rule says factor handles collection. Not our concern.** |
| Shipper portal | Operator may want operator-relay-only by design |
| Carrier portal | QROnboard.jsx exists; scope unclear |
| Multi-broker offer dedup | Requires multi-broker network — may not be in CHL solo-broker scope |

### What changed because of "we factor our invoices"

- **Bank API listener removed** from the build plan. Factor collects from shipper; we don't track shipper-side direct payment.
- **AR dunning re-scoped** — targets factor-declined invoices and direct-pay edge cases, NOT default-state shipper invoices.
- **Settlement cron clarified** — "24h post-factor-funding," not "24h post-shipper-payment."
- **Schedule-of-Accounts elevated** — this is the canonical broker→factor handoff. TIER 1.
- **Stripe stays wired but idle** for pivot-ability. Phase 5 = factoring. Period.

---

## Updated PHASE B.1 (TIER-1 + TIER-2 only — what we ship May 15-19)

8 fixes, all VALIDATED or fraud-exempt. Estimated 60% → 80% autonomous after this batch.

1. Auto-send dispatch packet on booking (Small)
2. Cron settlement trigger 24h post-factor-funding (Small)
3. NOA detection + payment routing (Small/Medium)
4. Authority-age check (<90 days) (Small)
5. Phone-FMCSA cross-check (Small)
6. POD red-flag auto-escalation (Small)
7. Schedule-of-Accounts batch submission (Medium)
8. Factor decline auto-reroute production-wire (Medium)

Detention/layover auto-detect (TIER-1) and per-load re-vetting harness (TIER-1) move to B.2 because they're medium effort.

---

## What gets reviewed tomorrow

Operator + dev walk through TIER-3 list. For each item, decide:
- **Validate** (proceed in B.2/B.3)
- **Defer** (out of scope for this build; revisit later)
- **Reframe** (operator clarifies the real need; rewrite the item)
