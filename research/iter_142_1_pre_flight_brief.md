# Iter 142.1 Pre-Flight Brief — Operator Gotchas Before Kickoff

> **Purpose:** Surface things operator should know BEFORE iter 142.1 kicks off, so day-1 isn't a series of small surprises. Generated autonomously by @dev-engineer 2026-05-07 per `feedback_autonomous_continue_mode.md` (backlog item 4 — pre-flight prep).
> **Source agenda:** `chl-memory/agenda/iter_142_1_full.md` (commit `f7fb39b` + `227272f` GSM-7 alignment).
> **Read time:** ~3 min.

---

## TL;DR — what to know before kickoff

1. **Iter 142.1 dev can START NOW** (post-iter-141.1 close), in parallel with FMCSA wait + Plivo wait. Don't wait for 141.2 / 141.3 to ship.
2. **Operator hands-on: ~70 min total** across the iter — but **30 min is concentrated in stage 1b** (FMCSA API key + 20-carrier seed batch CSV). Block dedicated time.
3. **Stage 1d depends on SMS provider being live** (Twilio or Plivo). Currently blocked behind iter 141.3 sales response. **Stage 1d ships behind 141.3.**
4. **Stage 1f end-to-end smoke depends on iter 141.2 auto-bid + FMCSA authority.** Pre-FMCSA, 1f smoke 5 runs in mocked mode.
5. **Three v1-default decisions** auto-applied — operator can override before kickoff (see "Open Questions Resolved" section below).

---

## Critical-path gotchas

### Gotcha 1: Stage 1b is the operator-time bottleneck (~30 min concentrated)

Stage 1b requires operator to:
- Sign up at safer.fmcsa.dot.gov developer portal for API key (~5 min)
- Hand-curate first batch CSV of ~20 trusted carriers (~20 min — researching MC numbers + lanes for each)
- Run import + spot-check (~5 min)

**Why it matters:** This is the only stage with 30+ min of operator hands-on. The other 5 stages average ~5-10 min each. Don't squeeze 1b into a busy day — block dedicated time.

**Mitigation:** If operator has a pre-existing carrier list (Quickbooks export, spreadsheet, paper rolodex), consult `iter_142_1_full.md` "Stage 1b — sleeper question 1" — agenda asks operator about format. Pre-stage the data BEFORE 1b kickoff to compress the 30 min to ~10.

### Gotcha 2: Stage 1d SMS provider dependency on iter 141.3

Stage 1d's outreach module uses `notification_service.py`. Whichever SMS provider is active at the time:
- **If iter 141.3 done (Plivo live):** stage 1d ships clean
- **If iter 141.3 still blocked (current 2026-05-07 state):** stage 1d either:
  - Ships on degraded Twilio (10DLC rejected, but voice path works) — voice-only outreach, no SMS, lower carrier acceptance rate
  - Waits for Plivo unblock — adds wall-clock to iter 142.1

**Recommendation:** Track Plivo sales response (~1 business day ETA per 141.3 status). If response lands by stage 1d kickoff (week 2+ of 142.1), no impact. If still pending, decide voice-only vs wait. Operator decision worth pre-flagging.

### Gotcha 3: Stage 1f end-to-end smoke is FMCSA-AND-141.2-gated

Stage 1f has 5 smoke tests:
- Tests 1-4: PDF rendering, attachment delivery, db.dispatch_packets row creation — **FMCSA-INDEPENDENT**, can run pre-May 13
- Test 5: real auto-bid → outreach → assign → confirm → packet → operator visual confirm — **FMCSA-GATED + 141.2-gated**

**Implication:** Stage 1f can SHIP with 4/5 smoke pre-FMCSA. Test 5 deferred to post-141.2-live. Iter 142.1 close ceremony can either:
- Wait for test 5 to flip green (clean iter close, but adds wall-clock)
- Close iter 142.1 with 32/33 smoke + test 5 marked CARRIED-FORWARD (similar to iter 141.1 stage 1d which deferred to 141.2)

Operator should decide ceremony posture when stage 1f opens.

### Gotcha 4: PDF library introduction (reportlab) — new dep

Stage 1f adds `reportlab` to backend requirements (~2MB, mature lib, MIT license). Standard PDF generation. **No concerns**, but worth noting:
- New dependency = new attack surface (review CVE history before pin)
- Pin version in requirements.txt (don't float)
- One-time `pip install` step in venv

**Effort:** ~5 min add to Stage 1f dev. Mentioned here for awareness only.

### Gotcha 5: Local disk PDF storage at v1 (sleeper question)

Stage 1f stores PDFs to local disk (`C:\CHL\dispatch_packets\` or similar). Sketch-level open question 3 from `iter_142_1_full.md` resolved to local disk for v1.

**Implication:**
- ~50 packets/day expected at v1 carrier-network scale
- Local disk fine until ~5,000 packets accumulated (then ~5GB, manageable)
- Phase 5 (POD + invoicing, iter 143.x+) will likely move to S3-equivalent
- D: backup ceremony will pick up packets via codebase snapshot

**Concern:** if operator's machine fails before D: backup, ~1 day of dispatch packets could be lost. PDF regeneration is deterministic (db.dispatch_packets row has all source data), so recovery is mechanical — not catastrophic.

---

## Open Questions Resolved — operator pre-kickoff sign-off list

The agenda auto-applies these 4 defaults. Operator can override before stage 1a opens:

| # | Question | Default applied | Override path |
|---|---|---|---|
| 1 | Paid carrier data sources | FMCSA SAFER only at v1 | Tell @dev-engineer pre-1b: "add Highway/RMIS at v1" → cost +$200-500/mo, exceeds threshold |
| 2 | Self-serve carrier registration portal | Broker-controlled vetting only | Defer until post-revenue |
| 3 | Rate negotiation | Fixed offer, no counter-offer rounds | Override pre-1d: "build counter-offer state machine" → +1-2 day dev |
| 4 | Carrier blacklist/whitelist scope | Global blacklist only | Override pre-1c: "per-shipper whitelist" → +0.5 day dev |

**Recommendation:** Don't override unless you have a specific reason. Defaults are pre-revenue-friendly (save dev time, defer compliance complexity, match v1 scale).

---

## Three sleeper questions (operator decisions during iter, not before kickoff)

From `iter_142_1_full.md` (these surface inside specific stages, not pre-kickoff):

1. **Stage 1b carrier list format:** paper / Quickbooks export / spreadsheet / DAT export / fresh build? (~30 min impact on 1b operator time)
2. **Stage 1c weight defaults:** profitability 0.35, reliability 0.30, risk 0.20, capacity 0.15 — sign-off or override (no time impact, just judgment)
3. **Stage 1f packet storage:** local disk OK at v1, S3-equivalent later (already-resolved default; resurface only if operator wants S3 earlier)

These DON'T need pre-kickoff answers. They surface inside the relevant stage handoff doc. Mentioned here so operator isn't surprised when they come up.

---

## Cross-iter parallelism — when to start dev

**Iter 142.1 dev work proceeds IMMEDIATELY after iter 141.3 ships, in parallel with FMCSA wait.**

Concrete schedule (worst-case, assuming Plivo 1-2 day response + FMCSA on schedule):

| Week | Dev work | FMCSA / Plivo state |
|---|---|---|
| Week 1 (now → 2026-05-14) | Iter 142.1 stage 1a + 1b dev (FMCSA-blocked for go-live but not dev); iter 141.3 white-glove wait | FMCSA TBD; Plivo white-glove submitted |
| Week 2 (~2026-05-14 → 2026-05-21) | Iter 142.1 stage 1c + 1d dev; iter 141.2 dispatch (FMCSA gates clear ~May 13+) | FMCSA likely landed; Plivo likely provisioned |
| Week 3 (~2026-05-21 → 2026-05-28) | Iter 142.1 stage 1e + 1f dev | All gates clear; iter 141.3 cutover possible |
| Week 4 (~2026-05-28 → 2026-06-04) | Iter 142.1 close ceremony; iter 142.2 (Phase 4) kickoff | All upstream cleared |

This compresses 142.1's 3-week dev timeline to actually be ~3 weeks wall-clock, with no idle time waiting on upstream.

---

## Memo to operator — items requiring action BEFORE iter 142.1 kickoff

- [ ] **Decide:** override any of the 4 v1 defaults? (Default: no override.)
- [ ] **Pre-stage:** existing carrier list (Quickbooks/spreadsheet/paper)? Sharing format with @dev-engineer compresses stage 1b operator time from 30 min → 10 min.
- [ ] **Schedule:** block ~30 min during stage 1b for FMCSA API key + CSV curation. Don't squeeze into a busy day.
- [ ] **Confirm:** stage 1d SMS provider posture (wait for Plivo vs voice-only on Twilio) — surfaces pre-stage-1d, not pre-kickoff. Just be aware.

---

## Cross-references

- `chl-memory/agenda/iter_142_1_full.md` — the full agenda (this brief is a digest)
- `chl-memory/research/iter_142_1_phase_3_sketch.md` — original 4-stage sketch (SUPERSEDED, kept for history)
- `chl-memory/research/iter_141_3_stage_1b_prep_brief.md` — analogous prep brief for the iter 141.3 white-glove wait (for pattern reference)
- `chl-memory/research/messaging_stack_scaling_sketch.md` — forward-looking Phase 4+ scaling concerns (relevant when 142.1 hits stage 1d outreach)

---

**Source:** Drafted by @dev-engineer 2026-05-07 (autonomous-continue mode, backlog item 4) per `feedback_autonomous_continue_mode.md`. Reviewer: Claude Code Opus 4.7.
