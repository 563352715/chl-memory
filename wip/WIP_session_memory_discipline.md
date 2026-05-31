# WIP — Session memory discipline (the saved≠remembered fix)

**Updated**: 2026-05-30 (late session)
**Per**: [[feedback-saved-not-remembered-fix]] + [[feedback-continuous-self-improvement-load]] + [[feedback-operator-should-not-have-to-prompt-engineer]]

## State of play

Operator caught dev Claude tonight not knowing two committed-prospect packets arrived (the Andersons Trey + Andersons Kurt/Courtney exchanges). Diagnosis: "saved conversation backups" ≠ "remembered." Each Claude session starts fresh; the JSONL backup pipeline is for disaster recovery, not continuity.

Tonight's fixes layered three independent recall mechanisms:
1. **Memory files** (this dir + ~/.claude/projects/c--CHL/memory/) — durable rules + facts.
2. **Session boot brief** — `backend/scripts/session_boot_brief.py` queries last-48h DB state + writes a markdown brief to Desktop. CLAUDE.md step 11c mandates running it at session boot.
3. **shipper_master** — per-shipper canonical record with all touches joined; queryable directly.

## DECIDED — do not relitigate

- "Saved ≠ remembered" is the lens, not the rule. The rule: DB queries > memory recall for live state.
- CLAUDE.md step 11c is mandatory, not optional. Run the boot brief script first thing in every session.
- The discipline that failed tonight was: dev Claude didn't grep BOOT_BIOS.md before talking about "the new SSD" — would have found MongoDB already on N: drive. Same defect class as the packet-arrival miss. Memory file `feedback_read_system_blueprints_before_speaking_about_infra.md` captures the rule.
- Operator-side prompt engineering is a defect class on dev Claude's side, not operator's. Memory file `feedback_operator_should_not_have_to_prompt_engineer.md` captures it.
- Calibration failure ≠ memory failure. Dev Claude states wrong conclusions with the same confidence as right ones. Memory file `feedback_check_economics_before_recommending_delay.md` is the first instance.
- Mannerism mode (polite-prudent-advisor voice) substitutes tone for substance. Memory file `feedback_verbosity_creates_false_belief_reinforcement.md` captures it.

## NEXT concrete actions

1. **Operator's discretion: do they want me to start every response with an explicit "queried X to verify Y" trace?** Would make calibration visible. Decision deferred until operator weighs the verbosity cost.
2. **Auto-summarize each session at compaction** into a structured memory entry. Currently depends on Claude noticing what's important. ~2 hours to build.
3. **Vector-search over saved conversation JSONLs** so I can find "the time we discussed X" without depending on memory files capturing it. ~1 day, ~$5/mo embedding cost.

## BLOCKED ON

- Both #2 and #3 are operator decisions on whether the spend (in time / money) is worth the closing of the remaining ~10-20% memory gap.

## How dev Claude should use this

Read this file at every session boot per CLAUDE.md step 11b. Before forming any conclusion involving infrastructure, decisions made earlier, or what a shipper said, query the DB first. Quote the trace if asked.
