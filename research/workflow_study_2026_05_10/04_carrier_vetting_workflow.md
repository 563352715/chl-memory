# Carrier Vetting Workflow — Definitive Reference

**Authored:** 2026-05-10 | **Author:** dev-engineer Claude | **Context:** CHL pre-launch (May-14 platform-readiness deadline)
**Question answered:** "How will we vet the carriers, and where to put that information?"

This document synthesizes the carrier-vetting workflow as published by Highway, MyCarrierPortal (Descartes), RMIS (Truckstop), Carrier Assure, Carrier411, the TIA, FMCSA, and the broker-defense legal bar. It distinguishes **regulatory minimum** from **industry best practice**, separates **onboarding-time** checks from **per-load** checks, and ends with a concrete schema for `db.carriers` and `db.carrier_vet_events`.

---

## 1. The Two-Stage Vetting Model

Industry consensus (Highway, MCP, RMIS, TIA framework): carrier vetting is **NOT** a single event. It is a two-stage continuous process.

| Stage | Trigger | Purpose | Cadence |
|---|---|---|---|
| **Onboarding vetting** | First contact with a new carrier (before they appear in your selectable carrier pool) | Verify legal/financial fitness, sign agreements, capture tax + payment data | One-time + monthly re-monitoring |
| **Per-load vetting** | Every load dispatch (especially first 1-3 loads, and any load > certain $$/cargo value) | Confirm authority + insurance + safety status are STILL active TODAY; confirm the entity quoting is the same legal entity that onboarded; detect double-broker / chameleon / identity-theft signals | Every single load |

The most-failed pattern in 2025–2026 fraud cases: a broker onboarded the carrier 6 months ago, never re-checked, and the carrier's authority lapsed / insurance was cancelled / the MC was hijacked by a fraudster two weeks before the load was booked. Highway's Q1 2026 data shows identity-theft on previously-clean MC numbers up 89.6% YoY and change-of-ownership fraud up 169.6% YoY — making per-load re-verification non-optional.

---

## 2. Regulatory Minimum (49 CFR + case law on negligent selection)

Per the FMCSA Small Entity Compliance Guide for Broker Operations and the broker-defense bar (Lewis Brisbois, Taylor Nelson Slattery Bernard, CCJ), the *legal floor* a broker must clear to defeat a negligent-selection claim:

1. **Active operating authority** — confirm Common or Contract authority is ACTIVE on FMCSA L&I (`li-public.fmcsa.dot.gov`). Not pending revocation.
2. **Not "Unsatisfactory" safety rating** — Conditional or Unsatisfactory ratings are prima facie evidence of negligence if you book anyway. Satisfactory or Unrated is the safe set.
3. **Proof of financial responsibility** — active BMC-91 / BMC-91X auto-liability filing (min $750k general freight, $5M hazmat) confirmed via L&I.
4. **Signed Broker-Carrier Agreement** — written contract on file with each carrier before tender.

That's it for the legal floor. Brokers who stop here lose negligent-selection cases routinely because the standard of care has risen with the tools available.

---

## 3. Industry Best-Practice Maximum (Highway / MCP / RMIS / Carrier Assure / TIA composite)

### 3.1 — Onboarding-time checks (one-time, then continuously monitored)

**Identity & legal entity**
1. USDOT # + MC # cross-validation on FMCSA SAFER and L&I. Match legal name, DBA, principal address.
2. **Authority age > 180 days** (RMIS CRS+ standard). Fresh authority is the #1 shell-carrier signal.
3. Drug & Alcohol Clearinghouse query authorization (carrier itself does this for drivers per 49 CFR 382; the broker confirms the carrier has a registered Clearinghouse account).
4. **BOC-3 (process agent)** filing active — proves they can be legally served. Brokers carry their own BOC-3 too.
5. **Insurance certificate (COI)** received directly from the insurance agent's verified email/phone (not forwarded by carrier) — best practice per PFA Protects & TIA. Confirm:
   - Auto liability ≥ $1M (industry standard, above $750k regulatory floor)
   - Cargo coverage ≥ $100k (or matched to actual cargo value on covered loads)
   - Broker named as Certificate Holder; some shippers require Additional Insured
   - Policy expiration > expected load completion date
6. **W-9 + TIN matching** — verify legal name matches IRS records (RMIS and MCP both run real-time TIN match). Required for 1099 issuance.
7. **OFAC sanctions screening** — RMIS runs automatically; broker must too. Prevents inadvertent dealing with sanctioned entities.
8. **Signed Master Carrier Service Agreement (MCSA / CBA)** — broker-carrier contract covering rates, claims procedures, double-broker prohibition, indemnity.
9. **Carrier profile data** — fleet size, equipment types (dry van / reefer / flatbed / step-deck / power-only / hazmat-certified), preferred lanes, dispatcher contact, accounting/AP contact.
10. **Banking / payment routing** — ACH details, factoring company NOA (notice of assignment) if factored. Critical: factor-company verification prevents one of the top-3 fraud vectors (rerouted payments).

**Safety & performance**
11. **SMS BASIC scores** (7 categories): Unsafe Driving, Crash Indicator, HOS Compliance, Vehicle Maintenance, Controlled Substances/Alcohol, Driver Fitness, Hazmat. FMCSA flags alerts at **65th percentile** for Unsafe Driving / HOS / Crash Indicator, **80th percentile** for the rest. Don't onboard carriers above threshold without a written justification.
12. **Inspection / violation history (24-month)** — pulled from SMS. Trend matters more than absolute count.
13. **Crash history** — preventable vs non-preventable; police-reportable in last 24 months.

**Reputation**
14. **TIA Watchdog reports** — member-submitted complaints + carrier responses. Single negative report is not disqualifying; pattern is.
15. **Carrier411 FreightGuard reports** — same model. Note: OOIDA has documented due-process complaints about Carrier411 (carriers can't respond, reports made permanent after 72h). Treat as one data point, not gospel.
16. **Internal blacklist + claims history** — if your own org has prior loss/claim with this MC, flag immediately.
17. **Carrier Assure A–F score** (or equivalent) — composite of business stability, DOT performance, inspection trend, insurance/authority status, tracking reliability, community feedback. Useful as a single-number gate (e.g., reject D/F automatically; manual review for C).

### 3.2 — Per-load checks (every dispatch)

1. **Authority still active** as of today (cache 24h max).
2. **Insurance still in force** as of today, expiration > load delivery date.
3. **Safety rating unchanged** (no downgrade since last check).
4. **No new SMS alerts** crossing intervention thresholds.
5. **Identity match on the dispatcher** — phone number, email domain, voice (Highway IDV-style). The voice/face on this load must match the entity that was onboarded.
6. **Equipment match** — the truck type they're offering matches what they onboarded with and the load requires.
7. **No active TIA Watchdog / FreightGuard report posted since last load**.
8. **No double-broker prior** in our own platform (same MC posting our prior loads on a load board).
9. **Anti-double-broker tender controls** — rate confirmation includes "no re-brokering" clause; driver name + truck # + trailer # collected at dispatch and validated at pickup.

---

## 4. Modern (Highway-era) vs Legacy (Carrier411-era)

| Dimension | Legacy (Carrier411, early RMIS) | Modern (Highway, MCP, Carrier Assure, RMIS-Truckstop) |
|---|---|---|
| **Identity verification** | Document collection (MC certificate, COI PDFs). Forge-able. | Active IDV — driver-license photo, selfie liveness, IP allowlist, email verification code, dispatcher-phone validation |
| **Fraud detection** | Manual user-submitted complaints (FreightGuard) | ML/algorithmic — Carrier Assure analyzes 2M+ DOT #s daily; Highway tracks 527k+ fraudulent inbound emails per quarter |
| **Update cadence** | Pull on demand | Real-time webhook push when FMCSA data changes |
| **Monitoring scope** | Authority + insurance + safety rating | + identity continuity, dispatcher behavior, load-board posting patterns, change-of-ownership events, tracking-history correlation |
| **Coverage** | "Most" carriers | RMIS claims ~98% of NA carriers; Highway has direct relationships with the carriers themselves |
| **Carrier voice** | Carrier locked out of disputing | Carrier-side login (Highway, MCP) — carriers manage own profile, reducing data-staleness |

For CHL build: target modern model. Highway-style identity verification is the floor, not the ceiling.

---

## 5. Red-Flag Detection Rules (auto-block or auto-flag)

Per Highway, Denim, TransCredit, OTR Solutions, FMCSA fraud advisory:

| Signal | Severity | Auto-action |
|---|---|---|
| Authority age < 90 days | HIGH | Manual review; require deposit/escrow |
| Authority age 90-180 days | MED | Manual review |
| Carrier email is `@gmail.com` / `@yahoo.com` / `@outlook.com` (no business domain) | HIGH | Block until business email verified |
| Phone is VOIP / prepaid cell | MED | Verify with second-channel callback |
| MC address shared with multiple unrelated MCs | HIGH | Block — chameleon indicator |
| Recent change of ownership (FMCSA Form MCS-150 update < 60 days) | HIGH | Re-run full onboarding; do not auto-renew |
| Carrier has BOTH motor carrier AND broker authority | MED | Flag — double-broker risk; require written commitment |
| Reactivated authority after revocation | HIGH | Block — reincarnated/chameleon per 49 CFR 385.1005 |
| SMS Unsafe Driving / HOS / Crash above 65th percentile | HIGH | Block by default; manual override w/ documented reason |
| Conditional safety rating | HIGH | Block — prima facie negligence |
| Active TIA Watchdog report in last 90 days | MED | Manual review |
| Inbound email mismatch (claims to be Carrier X but email domain is Carrier Y) | CRITICAL | Block + report to FMCSA |
| Dispatcher voice claims fleet of 1-2 trucks but call-center background noise | MED | Verify with onsite check or video call |
| Rate confirmation forwarded to a different MC | CRITICAL | Block — active double-broker |
| Payment routing changes mid-relationship | CRITICAL | Block all pending payments; verify with original signatory |

---

## 6. Recommended Storage Schema (for CHL `db.carriers` + companion collections)

### `db.carriers` — one document per legal carrier entity

```
{
  _id, mc_number, usdot_number, legal_name, dba_name, physical_address, mailing_address,
  authority_status: { common: bool, contract: bool, household_goods: bool, granted_date, last_checked },
  authority_age_days,                                  # derived
  safety_rating: { rating, rating_date, source: "FMCSA" },
  sms_basics: { unsafe_driving_pct, hos_pct, crash_pct, maint_pct, drugs_alcohol_pct, driver_fitness_pct, hazmat_pct, last_pulled },
  insurance: {
    auto_liability: { carrier_name, policy_number, limit, effective_date, expiration_date, agent_name, agent_phone, agent_email, source_verified: bool },
    cargo: { ... same fields ... },
    bmc_91_on_file: bool, bmc_91_filed_date,
    additional_insured_filed: bool,
    coi_source: "agent_direct" | "carrier_forward" | "unverified"
  },
  filings: { boc3_on_file, boc3_process_agent, w9_on_file, w9_tin_matched, ofac_cleared, ofac_checked_date },
  agreement: { mcsa_signed_date, mcsa_version, signer_name, ip_address },
  clearinghouse: { account_registered: bool, query_consent_on_file: bool },
  payment: { routing_type: "ach"|"check"|"factor", factor_company_name, factor_noa_on_file, ach_account_last4, last_changed },
  identity_verification: {
    method: "highway"|"manual"|"none",
    verified_date, verified_by, dispatcher_phone_verified, business_email_verified,
    liveness_checked: bool
  },
  fleet: { power_units, drivers, equipment_types: [...], preferred_lanes: [...] },
  reputation: {
    tia_watchdog_reports: [{ date, reporter_id, category, resolved }],
    freightguard_reports: [...],
    internal_claims: [...],
    carrier_assure_score: "A"|"B"|"C"|"D"|"F",
    internal_score: 0-100                              # CHL composite
  },
  red_flags: [ "authority_under_180d", "voip_phone", ... ],   # active flags
  status: "onboarded" | "monitoring" | "suspended" | "blocked",
  blocked_reason, blocked_date,
  monitoring: { next_check_due, last_full_check, webhook_subscriptions },
  contacts: [{ role: "dispatch"|"safety"|"accounting"|"owner", name, phone, email, verified }],
  created_at, updated_at, onboarded_by
}
```

### `db.carrier_vet_events` — append-only audit trail

Every check (onboarding, per-load re-check, monitoring webhook, manual review) writes an event:

```
{ _id, carrier_mc, event_type, triggered_by, load_id_optional, checks_performed: [...],
  pass_fail, anomalies: [...], raw_responses: {...}, timestamp, ttl_for_validity }
```

Per-load checks reference this collection — if a recent (< 24h) `passed` event covers the required checks, we skip; otherwise we re-pull.

### `db.carrier_documents` — file references (Vault-backed)

COI PDFs, W-9 PDFs, signed MCSA, authority certs, IDV selfies. Store in CHL Vault (`vault_files`) per the standing rule that filesystem is not the destination for any operator-readable file; metadata row here references the vault UUID.

---

## 7. CHL-specific build implications

1. **Onboarding flow** lives in `backend/carrier_onboarding.py` (build new) wired to the existing carriers tab. Drives `db.carriers` writes + Vault doc storage.
2. **Per-load gating** wires into the rate-confirmation / dispatch path. The book-load endpoint MUST call a `vet_for_load(mc, load)` function that returns `(allow, blocked_reasons[])`. Hard fail on any CRITICAL or HIGH red flag.
3. **Continuous monitoring**: nightly cron pulls FMCSA L&I + SAFER for every `monitoring`-status carrier; webhook subscriptions where vendors support it (Highway, MCP, RMIS).
4. **Source-of-truth precedence**: FMCSA L&I > insurance-agent-direct > carrier-self-report. Never trust load-board cached insurance fields.
5. **Vendor selection deferred**: RMIS, MCP, Highway, Carrier Assure all charge per-MC fees. For pre-launch we implement the FMCSA-direct path (free, regulatory floor + much of best practice) and stub the vendor-integration hooks for post-revenue add-on. Highway is the strongest single add given Q1 2026 fraud trend.
6. **Per-load checks must be sub-second** — cache 24h, refresh on cache miss. Don't make a dispatcher wait 30s for FMCSA scrape on every tender.

---

## Sources

- Highway — [Carrier Identity & Freight Fraud Prevention](https://highway.com/posts/the-future-of-carrier-identity-combating-fraud-and-double-brokering-in-the-freight-industry), [Q1 2026 fraud release](https://www.globenewswire.com/news-release/2026/05/05/3288141/0/en/Freight-Fraud-Hits-Record-High-in-Q1-2026-Half-of-All-Incidents-Tied-to-Carriers-With-Clean-Records.html)
- Descartes MyCarrierPortal — [Identify & Vetting](https://www.mycarrierportal.com/features/carrier-identify-vetting/), [Carrier Onboarding](https://www.mycarrierportal.com/features/carrier-onboarding/)
- RMIS / Truckstop — [Strengthening Freight Operations](https://truckstop.com/blog/rmis-helps-brokers-combat-fraud/), [TAI RMIS Monitoring](https://learn.tai-software.com/knowledge/rmis-carrier-monitoring)
- Carrier Assure — [How It Works](https://www.carrierassure.com/how-it-works), [Carrier Performance Data](https://www.carrierassure.com/blog/how-to-utilize-carrier-performance-data)
- Carrier411 — [Due Diligence](https://www.carrier411.com/duediligence.cfm), [Negligent Hiring](https://www.carrier411.com/negligent-hiring-carriers.cfm)
- TIA — [Watchdog](https://tiawatchdog.com/), [TIA Framework to Combat Fraud (report summary)](https://www.logisticsmgmt.com/article/new_tia_report_takes_a_deep_dive_into_ways_of_combatting_freight_fraud)
- FMCSA — [Small Entity Compliance Guide for Brokers](https://www.fmcsa.dot.gov/sites/fmcsa.dot.gov/files/docs/small-entity-comp-guide-broker-operations-508.pdf), [L&I Carrier Search](https://li-public.fmcsa.dot.gov/LIVIEW/pkg_carrquery.prc_carrlist), [SMS Methodology](https://csa.fmcsa.dot.gov/documents/smsmethodology.pdf), [Drug & Alcohol Clearinghouse](https://clearinghouse.fmcsa.dot.gov/), [BOC-3](https://www.fmcsa.dot.gov/registration/form-boc-3-designation-agents-service-process)
- Legal — [Lewis Brisbois on Negligent Selection](https://lewisbrisbois.com/newsroom/articles/defending-freight-brokers-from-negligent-selection-claims-involving), [CCJ on Negligent Selection](https://www.ccjdigital.com/business/article/15293849/avoiding-liability-for-negligent-motor-carrier-selection), [49 CFR 371](https://www.ecfr.gov/current/title-49/subtitle-B/chapter-III/subchapter-B/part-371), [49 CFR 385.1005 chameleon rule]
- Fraud / red flags — [TransCredit double-broker 2025](https://www.transcredit.com/double-brokering-scams-freight-prevention-2025.html), [OTR Solutions](https://otrsolutions.com/blog/preventing-double-brokering-and-trucking-fraud), [Denim](https://www.denim.com/blog/unmasking-double-brokers-tips-to-protect-your-brokerage), [PFA Protects](https://pfaprotects.com/2023/02/27/best-practices-protect-your-business-by-properly-vetting-your-motor-carriers-2/), [Truckstop 7 Ways](https://truckstop.com/blog/carrier-vetting/)
- CCJ Q1 2026 — [Cargo Theft Losses Up 60%](https://www.ccjdigital.com/technology/article/15820736/cargo-theft-losses-up-60-how-tech-is-fighting-2026s-fraud-surge)

**Word count:** ~1,650
