# CHL Market-Dominance Strategy — Phased Survive → Undercut → Take the Market

**Operator-stated 2026-05-09 EOD-17.** Captured for future strategic-design discussion. **NOT a build directive yet** — operator specifically said: *"Be sure we add this to a future discussion."*

## Operator's exact words

> "We only need to construct the different rate calculation behaviors to suit the nuances of the lanes we are operating in. We also need to understand when to undercut everyone to keep our overall outcome driven operation more functional. We need to discuss what this means. Be sure we add this to a future discussion. We intend to take the whole market. Not at first, but eventually. Undercutting where it can provide us with an unbeatable long term advantage is a strategy I want to employ. At first it will be more about survival and generating net profit, but when we make the platform stable and run faster, I want to get to 100% capacity, and I want every carrier wanting to work with us."

## Why this matters

Different lanes have structurally different economics (capacity, deadhead probability, seasonality, shipper type, regional carrier scarcity). The rate calculator must adapt its **behavior** to lane state, not just plug numbers into a single formula. AND the platform's long-term strategic intent is to use sustained undercutting on chosen lanes — enabled by the autonomous-platform cost advantage — to take market share that competitors can't match without going below their own cost floor.

## Two-phase operating intent

| Phase | "Winning" looks like | Pricing behavior |
|---|---|---|
| **Phase 1 — Survival (NOW)** | Net profit > 0/month, no blowups, platform reaches stability | Floor-protected (1.08 minimum), no undercutting, every load must clear ops cost + factor fee + reserves |
| **Phase 2 — Dominance (FUTURE)** | 100% capacity utilization, every carrier wants us, target lanes locked in | Selective sustained undercutting where lock-in moat > short-term margin sacrifice |

## Phase 1 → Phase 2 transition gate

Requires ALL of:
- May-14 platform readiness met (RFQ → cash autonomous, no operator-touch on critical path)
- 30 consecutive days no fraud / payment-failure incident
- Working-capital cushion ≥ 90 days runway at 50% utilization
- Autonomy score ≥ 85% (per `NORTH_STAR_BUILD_PLAN.md` halting condition)
- Operator-explicit GO (this is a strategic decision, not an automated trigger)

Until ALL conditions met: every lane runs in Phase 1 / **survival** mode.

## The mode table (the rate calc's "behaviors")

| Mode | When applied | Premium behavior | Carrier-rate behavior | Phase |
|---|---|---|---|---|
| **survival** | All lanes by default | Floor-protected, never below 1.08, never undercut | Standard multipliers | Phase 1 default |
| **bid-aggressive** | Phase 2; lanes where we want share growth | 1.08-1.10 (undercut market 3-5%) | Carrier-favorable | Phase 2 |
| **defensive** | Phase 2; lanes where we have pricing power | 1.13-1.18 within band | Standard | Phase 2 |
| **carrier-attract** | Phase 2; lanes where carrier base is thin | 1.10-1.12 | +5-10% over market on carrier side | Phase 2 |
| **volume-grab** | Phase 2; target lane / target shipper | 1.05-1.08 (aggressive, may breach floor with sovereign override) | Standard | Phase 2 |
| **outcome-protect** | Phase 2; loss-leader allowed | Below floor permitted for tagged shipper / lane | Standard | Phase 2 |

Mode set per lane (or per shipper-lane pair). Default = survival.

## The cost-advantage thesis

Sustained undercutting is only viable if our cost-per-load is structurally lower than competitors. The autonomous platform IS that cost advantage:

| Operator | Per-load ops cost (rough) |
|---|---|
| Manual broker (humans at every checkpoint) | $80-150 |
| CHL with autonomous platform at scale | $10-20 |

That ~$60-130 per-load cost gap is the structural moat. We can run at 8% margin where they need 12% to break even, and still be more profitable per load. Over years, that compounds into:
- Market share growth
- Shipper lock-in (sticky relationships once we deliver consistently below market)
- Carrier loyalty (better pay than DAT spot, paid faster than industry net-30)
- Data flywheel (more loads = better lane intelligence = better pricing decisions)
- Wider moat (more shippers + carriers + data = harder for competitors to catch up)

## "Every carrier wanting to work with us" — the carrier-side mirror

Carrier loyalty drivers, all already-wired or in-flight:
- **Competitive carrier rates** — don't squeeze; pay at or above DAT spot
- **Fast pay** — 1-3 days via factor (vs 30-day market) — already structurally true
- **Easy paperwork** — PWA, auto-rate-con, auto-POD, auto-NOA — substantially built
- **Consistent load flow** — sustained dispatch volume — requires us holding the shipper side
- **No drama** — automated dispute resolution, instant detention auto-pay — platform handles this

The platform's autonomous nature directly enables every carrier-loyalty driver. More carriers want us → more capacity → more shippers → more lanes → wider moat.

## "Outcome-driven" measurement

Outcome ≠ per-load margin. Outcome metrics:
- **Capacity utilization %** — carrier-hours moving CHL freight ÷ available carrier-hours
- **Shipper retention** — month-over-month repeat-shipper rate
- **Carrier retention** — % carriers running ≥3 loads/mo with us
- **Lane lock-in** — # lanes where CHL is shipper's primary broker
- **Net profit run-rate** — gross over op-cost, smoothed 30/90-day
- **Cost-per-load delta vs competitor** — proxy for moat width

A load priced at break-even that wins a 50-load/year shipper is +$50K future revenue. The calc should be able to recognize that explicitly when a load is tagged `loss_leader_acceptable` (operator-set OR autonomously after Phase 2 transition).

## What's NOT in scope yet

- The mode-setting UI / API — needs design discussion first
- The competitor undercut reference point (DAT spot? Truckstop avg? Highway median?) — depends on which load board API we have
- The loss-leader budget mechanic (per-month %, per-shipper cap, audit requirements) — needs operator design
- The phase-retreat trigger (what kicks us back from Phase 2 → Phase 1 on incident)
- Outcome dashboard wiring

## Cross-references

- `~/.claude/projects/c--CHL/memory/project_business_model_decision_2026_05_09.md` — Model C generalist mandate
- `~/.claude/projects/c--CHL/memory/project_platform_north_star.md` — autonomous-platform-as-moat thesis
- `chl-memory/progress/NORTH_STAR_BUILD_PLAN.md` — Phase A/B/C structure (this strategy adds Phase D mode-aware pricing on top of B)
- `C:/CHL/memory/CAPABILITIES.md` section 0 — current rate calc + lane intel state

## When discussion resumes

1. Surface this doc's mode table — design starting point
2. Walk through Phase 1 → Phase 2 trigger conditions — confirm or adjust
3. Decide on `db.lane_modes` schema + default behavior (survival)
4. Decide whether mode is operator-set, classifier-set, or hybrid
5. Decide on loss-leader budget mechanics
6. Define the competitor reference point for "undercut by N%"
7. Then build (LLL19+ candidate)

**Risk of building before design is locked is higher than risk of waiting.** This is a strategic-design discussion, not an implementation rush.
