# WIP — Andersons email re-linkage

**Updated**: 2026-05-30 (late session, found right before operator bedtime)

## State of play

Today's Andersons emails (3 inbound from trey_haden@, kurt_bartels@, InsuranceCertificates@) all got linked during Phase C backfill to `msh_b31f4e140340472e98f2` — the **Andersons Toledo OH** master that came from Yeti substrate. The four operator-curated active-prospect masters (Kearney, Kansas, Dunkirk, Bunge) were created AFTER Phase C ran, so they didn't exist to be matched against.

## DECIDED — do not relitigate

- The 4 curated masters are correct: msh_166ff4b528d54a3b9e8b (Kearney), msh_e40636813f754a89a921 (Kansas via Trey), msh_a47d7d6638204dc4a5be (Dunkirk), msh_d5464e47db9843009585 (Bunge Fairmont City).
- Toledo master `msh_b31f4e140340472e98f2` is also valid but represents the corporate parent location, not the active prospects.

## NEXT concrete action

Manual per-email re-link. The 3 emails to re-target:

| Email | Currently linked to | Should be |
|---|---|---|
| `kurt_bartels@andersonsinc.com` "RE: New hauler" | Toledo | **Kearney NE** (msh_166ff4b528d54a3b9e8b) |
| `trey_haden@andersonsinc.com` "Broker Carrier Setup" | Toledo | **Kansas** (msh_e40636813f754a89a921) — Trey is the Kansas/Dunkirk referral |
| `InsuranceCertificates@andersonsinc.com` "RE: New hauler - CHL" | Toledo | **Kearney NE** (msh_166ff4b528d54a3b9e8b) — Courtney's response on Kurt's thread |

After relink, also bump master counters: email_count + last_inbound_at on each target master, decrement on Toledo.

## BLOCKED ON

- Operator awake to confirm the per-email targeting (Trey's emails specifically may belong to either Kansas or Dunkirk depending on which one he's currently coordinating).

## Why this matters

When dev Claude or operator opens `/shipper/msh_166ff4b528d54a3b9e8b` (Andersons Kearney), today's broker-agreement-signed emails should appear in the timeline. Currently they appear in the Toledo timeline instead, making the active-prospect view incomplete.

Adjacent fix: add a generic "domains shared across multiple masters" tiebreaker rule to the resolver — when a domain matches >1 master, prefer the master with `operator_curated: true`. Not built tonight.
