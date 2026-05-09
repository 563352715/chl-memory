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
