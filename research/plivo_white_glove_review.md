# Plivo White-Glove Submission Packet — Review (Packet vs Spec)

> **Reviewed:** Emergent-built `frontend/public/plivo_white_glove_packet.md` on CHL branch `conflict_060526_1955` (read-only via `git show`; conflict branch NOT merged — touches 50+ files including .gitignore + memory/* that would revert overnight work).
> **Compared against:** `chl-memory/research/emergent_plivo_white_glove_packet_task.md` (spec).
> **Reviewer:** @pm-lead, 2026-05-07.
> **Verdict:** **High quality. Ready for operator pre-flight + paste with 3 minor additions and 2 spec-mismatches worth resolving.** All 8 spec sections delivered. All 5 operator-fillable fields cleanly placeholder-marked. Emergent went beyond spec in several places (3-path opt-in framing, explicit TCR brand-reuse ask, audit-ledger JSON example, dedicated operator-fillable checklist) which materially improves first-pass acceptance odds.

---

## Spec Coverage (8/8 sections delivered)

| Spec § | Packet § | Delivered? | Notes |
|---|---|---|---|
| 1. Brand Information | §1 | ✅ | Table format, includes spec fields + extras (DBA, Russell 3000 N/A, gov-entity flag, tax-exempt flag — all TCR-standard rows). |
| 2. Use Case Description | §2 | ✅ | Adds explicit TCR taxonomy ("Mixed Low Volume", sub-use-cases, embedded-links/phone/age-gate/loan flags) — these are exact TCR submission form fields. |
| 3. Recipients & Opt-In Flow | §3 | ✅+ | Packet expands spec's 1-path framing into **3 documented paths** (signed agreement, public web, keyword) — better than spec asked for. |
| 4. Sample Messages (5) | §4 | ✅ | All 5 examples, MC-cited, STOP/HELP keywords present. |
| 5. Volume Estimate | §5 | ✅ | Adds daily peak per phase to monthly volume. |
| 6. Phone Numbers to Link | §6 | ✅ | Adds TFV-rejection date + temp-number row. |
| 7. Compliance & Privacy | §7 | ✅ | All 5 sub-items present (privacy notice, data retention, HELP/STOP/START templates). |
| 8. Migration-Specific Notes | §8 | ✅+ | Adds explicit 5-item "Specific request to Plivo white-glove team" — rare and high-leverage. |

**Bonus section** (not in spec): "Operator-fillable checklist (before pasting)" at end — pre-flight catches the 5 placeholder fields before paste. Excellent addition.

---

## Operator-Fillable Fields Audit (5/5 placeholder-marked)

| # | Field | Marker in packet | Status |
|---|---|---|---|
| 1 | EIN | `<EIN>` *(operator fills in — see W-9 on file)* | ✅ |
| 2 | State of formation | `<state>` *(operator fills in)* | ✅ |
| 3 | Year formed | `<year>` *(operator fills in)* | ✅ |
| 4 | Authorized contact phone | `<contact phone>` *(operator fills in — may differ from SMS sender)* | ✅ |
| 5 | Registered business address | `<street, city, state, zip>` *(operator fills in — must match SOS filing)* | ✅ |
| (+1) | Plivo temp number | `<temp>` | ✅ (added by Emergent — appropriate) |

All 5 spec-required fields are present, marked with angle-bracket placeholder syntax, AND echoed in the standalone bottom checklist. Operator can't miss them.

---

## Where Emergent Exceeded Spec (Improvements)

1. **3-path opt-in framing (§3)** — spec implied 2 paths (signed agreement + planned web CTA); packet adds keyword opt-in (Path C) with full double-opt-in flow + audit-ledger JSON example. Significantly stronger TCR posture.
2. **TCR brand-reuse explicit ask (§8 #1)** — spec mentioned brand SID; packet promotes "re-use TCR Brand ID `B6384Q7`" to the #1 line of "Specific request to Plivo white-glove team". This was C5 of my prior spec-vs-requirements review — Emergent pre-handled it.
3. **CTA-build-status graceful punt (§3 Path B)** — explicit ⚠ note: "the `/sms-opt-in` landing page is in active build... If submission requires a live URL today, please flag and we will accelerate that build to T-0." This was C1 of my prior review — Emergent pre-handled it without committing to a path operator hadn't decided.
4. **Marketing exclusion declaration (§2 closing line)** — "CHL does not send marketing, promotional, or third-party content." Direct mitigation of the failure mode that killed Twilio's 30909. C4 of my prior review — pre-handled.
5. **HELP/STOP/START exact text (§7)** — fully drafted, brand-cited, MC-cited. C3 of my prior review — pre-handled.
6. **Keyword opt-out coverage (§3)** — spec said "STOP"; packet covers STOPALL/OPTOUT/CANCEL/END/QUIT/UNSUBSCRIBE/REVOKE/REMOVE. Comprehensive keyword surface.
7. **Twilio account closure timing (§8)** — explicitly states "Not yet — keep active until port + new campaign are live." Mitigates accidental-cancellation-during-port risk.

---

## Gaps / Mismatches vs Spec

### M1. Sample message length exceeds spec's "~140 chars" target
Spec §4 said "Each ~140 chars". Packet message #1 is ~178 chars (UTF-8; counts em-dash + arrow as multi-byte → forces UCS-2 encoding, max 70 chars/segment vs GSM-7's 160). Most messages run 1.5–2 segments at GSM-7 (~160 char boundary), or 2.5–3 segments at UCS-2.
**Impact:** Cost (each segment billed) — at v1 50–200 msg/mo this is ~$1–4/mo extra. Not a TCR-rejection risk, but the spec's spec-target is breached.
**Recommended action:** Either (a) accept the higher segment count (operator decision, marginal cost) or (b) ask Emergent to revise messages to ≤140 chars GSM-7 (replace `→` with "to", `—` with `-`, drop "Continental Haul:" prefix in favor of "CHL:"). Path (b) preserves spec intent.

### M2. New-number fallback path absent
Packet §6 hardcodes `+1 (417) 219-3856` as primary port-in target. iter_141_3_kickoff_checklist.md item 2 explicitly allows the **new-number path** (skip port if no paperwork cites the existing number). If operator's paperwork audit during stage 1a goes that way, the packet's §6 + §8 narrative becomes inaccurate — packet must be regenerated with new-number primary, no port narrative.
**Recommended action:** Operator confirms port-vs-new path **before** pasting packet to white-glove chat. If new-number path: regenerate packet (or hand-edit §6 + §8) before paste. Worth adding a one-line operator pre-flight check above §1 in the packet.

---

## Spec-Compliance Items Worth Pre-Flight Checking (not gaps in packet, but in operator hand-off)

These are completeness items the packet correctly defers to operator/runtime, not Emergent gaps — flagged so they're caught before paste:

- **PF1 — Operator FMCSA-registry cross-check:** EIN + registered business address in §1 must match CHL's FMCSA SAFER record exactly (mismatch is a known TCR rejection cause). Packet says "must match SOS filing" — extend to "and must match FMCSA SAFER record for MC-1817555".
- **PF2 — Plivo temp number filled in §6:** packet correctly leaves `<temp>` as placeholder; operator must fill after stage 1a step 10 (provision temp number) BEFORE pasting.
- **PF3 — `/sms-opt-in` page status decision:** Path B note asks Plivo to "flag and we will accelerate that build to T-0" — fine for first paste, but operator should be ready to follow through if Plivo requires URL live. Verify `emergent_sms_opt_in_page_task.md` has a fast-track switch ready.
- **PF4 — `/api/admin/sms-consent` audit endpoint actually exists at submission time:** §3 Path C references this endpoint with a sample row. If TCR (via Plivo) requests live audit access, the endpoint must be reachable — currently the consent ledger isn't in iter 141.3 scope (would need a quick stage 1d add or post-iter follow-up). Promise-vs-actual mismatch risk.

---

## Items NOT in Packet (residual TCR-level gaps from prior spec-vs-requirements review)

The packet handles 4 of the 6 critical gaps I flagged in the prior review (C1, C3, C4, C5). The remaining items below were not in the spec and so didn't make it to the packet — operator/spec-author can decide whether to add via §8 "Specific request" line items or address via separate ops note:

| Prior tag | Item | Severity | Worth adding to packet? |
|---|---|---|---|
| C2 | Suppression-list data location (`db.carriers.sms_opt_in`) | Critical | **Yes** — one-line addition to §3 opt-out box. |
| C6 | Operator-fillable FMCSA registry pre-flight | Critical | Yes — covered by PF1 above; operator-side check, not packet content. |
| R1 | Per-recipient frequency (msgs/recipient/month) | Recommended | Yes — single line in §5. Suggested: "Expected per-recipient frequency: 5–20 msgs/month (event-driven)." |
| R2 | TCPA quiet-hours posture | Recommended | Yes — one line in §7. |
| R4 | Number recycling policy | Recommended | Optional — TCR rarely asks; defer unless Plivo follow-ups. |
| O1 | DUNS number field | Optional | No — small-business exempt at v1. |
| O2 | Cross-state consent (CA/IL/NY) | Optional | No — B2B operational, federal TCPA + signed agreements sufficient. |

---

## Recommended Actions (in order)

1. **Operator decision (gates paste):** Confirm port path vs new-number path (M2). If new-number → regenerate packet or hand-edit §6 + §8.
2. **Operator pre-flight (~5 min):** PF1 (FMCSA registry cross-check on EIN + address), PF2 (fill `<temp>` after stage 1a step 10), PF4 (verify `/api/admin/sms-consent` endpoint reachable OR remove the JSON example from §3 Path C if not building consent-ledger this iter).
3. **Optional spec/packet additions before paste (3 minor edits, ~5 min):** Add C2 suppression-data-location line to §3, R1 per-recipient frequency line to §5, R2 TCPA quiet-hours line to §7. These materially improve TCR posture without lengthening paste much.
4. **Optional cost cleanup (~10 min):** M1 — ask Emergent (or hand-edit) to compress §4 sample messages to ≤140 chars GSM-7 to honor spec target and shave segment cost.
5. **Paste:** Operator pastes finalized packet to Plivo console chat per iter_141_3_kickoff_checklist.md step 8.

---

## Bottom Line

Emergent produced a **noticeably better artifact than the spec strictly required**. Of my 6 spec-vs-requirements critical gaps, 4 were pre-handled by Emergent's interpretation; only C2 (suppression-data-location) and C6/PF1 (operator FMCSA cross-check) remain — both quick fixes. The 2 packet-vs-spec mismatches (M1 length, M2 new-number fallback) are operator-decision-gated, not packet-quality issues.

**Net: ~10 min of operator pre-flight + 3 optional spec additions = packet ready for white-glove paste.**

---

**Source:** Drafted by @pm-lead 2026-05-07 reviewing Emergent-built packet on CHL branch `conflict_060526_1955`. Conflict branch NOT merged. Read-only inspection via `git show`.

**Cross-reference:**
- Source packet: `frontend/public/plivo_white_glove_packet.md` on CHL branch `conflict_060526_1955`
- Source spec: `chl-memory/research/emergent_plivo_white_glove_packet_task.md`
- Plivo process: `chl-memory/research/plivo_migration_research.md`
- Iter context: `chl-memory/research/iter_141_3_agenda_draft.md`, `iter_141_3_kickoff_checklist.md`
- Path B dependency: `chl-memory/research/emergent_sms_opt_in_page_task.md`
