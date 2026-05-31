# WIP — shipper_master forward-write integration

**Updated**: 2026-05-30 (late session)
**Per**: [[project-shipper-master-unified-record]]

## State of play

`db.shipper_master` shipped tonight: 633,327 canonical records joining all touches across collections. Search at `http://127.0.0.1:8765/shipper/search`; per-shipper view at `/shipper/<id>`. 4 active prospects (Andersons Kearney/Kansas/Dunkirk + Bunge Fairmont City) manually curated.

Forward-write hooks landed in three write paths plus a 5-min periodic linker for the scattered .send_*.py inserts. **The data layer is live.**

## DECIDED — do not relitigate

- Schema: `_id = msh_<uuid20>`, indexes on phones.digits, emails.email, domains, parent_company, status+priority, geo (2dsphere), simplified_name+city+state, sources.source_id, text-search on canonical_name+short_name+parent_company.
- Resolver precedence: phone-digit > email-exact > domain+name > simplified-name+city+state.
- Personal-email domains (gmail/outlook/yahoo/etc) and CHL's own + provider domains (adobesign/docusign/etc) are NEVER used for matching.
- Sub-numbered exhibits like ex211 ARE Exhibit 21.1 (kept). ex311 is Exhibit 31.1 (excluded as certifications). Same logic across all parsers.
- Backfill Phase D (masters from call_notes) has weak inference; operator-curated overrides are preferred for active prospects.
- Per-shipper view shows: phones+emails+urls in header, equipment+commodity tags, expand-all timeline of calls/emails/outbound, source provenance footer.
- Search filters: state, commodity, equipment, status, has_phone, has_email.

## DECIDED hooks (each verified or live)

- ✅ **call_notes POST /save** — verified end-to-end 2026-05-30 via test POST → master_id auto-attached, call_count incremented.
- ✅ **inbound_emails IMAP poller** — committed; fires on next email ingest (5-min poll cycle). No live email since hook landed.
- ✅ **vault.py uploads** — best-effort fuzzy match via filename+description.
- ✅ **periodic linker** — wired to background_loops_runner, fires every 5 min. First manual run drained 7,051 unlinked rows.

## NEXT concrete actions

1. Verify inbound_emails hook on next real shipper email — confirm `master_id` populated + `last_inbound_at` updated on master.
2. Update `enrich_*.py` scripts to also set master_id on inserts (currently they only write to shipper_targets; downstream backfill catches them).
3. Sender-side hook: pick a chokepoint for outbound emails. The Resend wrapper is probably the right spot — every send goes through it.
4. Build a "Today's masters with activity" Desktop widget that pulls from `db.shipper_master.find({last_inbound_at: {$gte: 24h}})` — operator's morning-brief view of the day.

## BLOCKED ON

- Nothing technical. The data layer is feature-complete for tonight's scope.
- Operator decisions still open: whether to expand the manual curation beyond the 4 prospects.

## How dev Claude should use this

When operator mentions a shipper name, FIRST query `db.shipper_master` by simplified_name or short_name. Don't grep across 8 collections. The master is the join table.

When ingesting new data sources, set `master_id` at write time using `resolver.resolve_master_id()`. Don't rely on the periodic linker — it's a fallback, not a primary path.
