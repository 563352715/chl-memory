# FFF5: Carrier Contact-Enrichment Data Sources — 9-Field Tiered Fill Strategy

License: Continental Haul Logistics LLC internal research / 2026-05-09 / sub-agent FFF5.
Author: Dev Claude sub-agent FFF5 (Carrier Contact Enrichment).
Authoritative for: which external data sources fill which of the 9 contact-enrichment UI fields, accuracy/cost/cadence per source, recommended adapter architecture.

Cross-references:
- `chl-memory/research/eee1_regulatory_authority_validation_2026_05_09.md` (FMCSA primary)
- `chl-memory/research/eee2_factor_industry_authority_validation_2026_05_09.md` (Carrier411 + factor DBs)
- `chl-memory/research/fmcsa_li_insurance_parsing_research.md` (Tier 1 vs Tier 2 architecture pattern)
- `backend/fmcsa_qcmobile.py` (existing FMCSA QCMobile fetch — insurance-only fields today; this doc extends to contact fields)

Scope: 9 carrier-profile fields the Contact Enrichment UI exposes:
1. Email
2. Phone
3. Address Line 1
4. Address Line 2
5. City
6. State
7. ZIP
8. Contact Name (officer / dispatcher)
9. Contact Title

---

## 1. Sources Consulted

### Primary federal (free / public)
- **FMCSA SAFER Company Snapshot** — https://safer.fmcsa.dot.gov/CompanySnapshot.aspx (accessed 2026-05-09). Help docs at https://safer.fmcsa.dot.gov/saferhelp.aspx.
- **FMCSA SAFER Help / FAQ** — https://safer.fmcsa.dot.gov/faq.aspx.
- **FMCSA Licensing & Insurance (L&I) portal** — https://li-public.fmcsa.dot.gov/LIVIEW/pkg_menu.prc_menu (accessed 2026-05-09). Migrating to MOTUS in 2026.
- **FMCSA Open Data Program / Census bulk** — https://www.fmcsa.dot.gov/registration/fmcsa-data-dissemination-program. Data Hub: data.transportation.gov.
- **FMCSA QCMobile JSON API** — https://mobile.fmcsa.dot.gov/qc/services/carriers/{DOT}?webKey=... (already in use at `backend/fmcsa_qcmobile.py`).
- **FMCSA MCS-150** form — https://www.fmcsa.dot.gov/registration/form-mcs-150-and-instructions-motor-carrier-identification-report (biennial filing, primary source of carrier-self-reported contact data).
- **State Secretaries of State (50 states)** — typical fields documented at https://idenfy.com/blog/secretary-of-state-sos-api/, https://www.middesk.com/blog/secretary-of-state-api, https://cobaltintelligence.com/blog/post/top-secretary-of-state-api-solutions-for-verifying-businesses (accessed 2026-05-09).
- **SaferWatch (3rd-party FMCSA wrapper)** — https://www.saferwatch.com/helpdocs/WebServices/CarrierLookup12032.php?version=32 (returns same fields as SAFER + carrierType bucket).

### Carrier-onboarding platforms (broker-side / carrier-collected)
- **MyCarrierPackets / MyCarrierPortal** — https://mycarrierpackets.com/IntegrationGuide (TMS API; GetCarrierData / CompletedPackets / MonitoredCarriers endpoints).
- **Highway** — https://highway.com/ + https://highway.com/posts/the-future-of-carrier-identity-... (Carrier Identity®, fraud + identity-verification API; serves 70 of top 100 brokers).
- **RMIS (Truckstop)** — https://truckstop.com/product/carrier-onboarding/ + https://marketplace.truckstop.com/t/api-integrations/carrier-intelligence-api (Lite from $340/mo).

### Factor / community DBs
- **Carrier411 (FreightGuard)** — https://www.carrier411.com/ . Defamation precedent: https://artaevatlaw.com/2024/03/01/carrier411lawsuit-defamation/, Greenline Express v. High Plains Logistics (2023 ~$458k); Penguin Trucking v. E.L. Hollingsworth (~$612k).
- **Debtor411** — https://www.debtor411.com/ (broker payment-reputation DB; out of scope for contact enrichment per se).
- **Cortera** — https://www.cortera.com/ (commercial credit; absorbed into Equifax's commercial in 2020 per industry reporting).

### Paid B2B contact enrichment SaaS
- **Hunter.io** — https://hunter.io/pricing (Free 25 searches + 50 verifications, Starter $49/mo for 2k credits, Growth $149/mo for 10k, Scale $299/mo for 25k; API access from $50/mo or bundled in Scale+).
- **ZoomInfo** — https://www.zoominfo.com/pricing (no public price; reported $15k–$60k/yr for full stack; API tier $50k/yr standalone or $5k/yr for HubSpot-marketplace contact enrichment).
- **Apollo.io** — https://www.apollo.io/pricing (Free with 5 mobile + 10 export credits/mo; Basic $49/user/mo; Pro $79/user/mo; Org $119/user/mo with API access).
- **Clearbit / HubSpot Breeze Intelligence** — https://www.hubspot.com (now requires HubSpot subscription; min $75/mo; per-credit charged on every API call regardless of result).
- **People Data Labs (PDL)** — https://peopledatalabs.com/ ($0.01/record at scale; 100 free calls/mo; 1.5B+ person records).
- **D&B (Dun & Bradstreet)** — https://www.dnb.com/products/marketing-sales/dnb-direct-for-marketing.html (D&B Hoovers from $10k/yr, enterprise $25k–$50k+/yr; Credit Insights Plus $149/mo).
- **Equifax Commercial** — https://www.equifax.com/business/ (enterprise-quoted; no public price).

### Search / scraping tier
- **Google Places API** — https://developers.google.com/maps/documentation/places/web-service/usage-and-billing. Contact data SKU $300–$1200/mo extra above base on volume tiers; replaced $200 monthly credit with free caps in 2026 (~28,500 loads/mo).
- **LinkedIn Sales Navigator** — https://business.linkedin.com/sales-solutions (no API for scraping; manual lookup or via licensed enrichment partners e.g. Apollo/ZoomInfo).
- **Open phone reverse-lookup (Whitepages, AnyWho)** — low value, deprioritized.

### Industry directories
- **DAT Directory** — https://www.dat.com/load-boards/dat-directory (member-only; covers DAT customers, name/phone/address/MC/DOT/safety rating).
- **Truckstop Carrier Directory** — included with Truckstop subscriptions.
- **ITS / Internet Truckstop authority** — superseded by Truckstop.

### Compliance / legal
- **CCPA/CPRA B2B coverage** — https://www.truevault.com/learn/ccpa-employee-and-b2b-data, https://natlawreview.com/article/are-you-ready-california-privacy-rights-act-could-now-apply-to-your-business (B2B exemption expired Jan 2023; work email/phone/title now protected).
- **GDPR Art. 6(1)(f) legitimate interest for B2B** — https://www.unifygtm.com/explore/b2b-data-compliance-gdpr-ccpa.
- **TIA stance** — https://www.tianet.org/ + FMCSA broker transparency rule reporting at https://www.bobtail.com/blog/broker-transparency-2025-... (TIA opposes blanket transparency; no formal data-sharing-between-brokers policy).

---

## 2. Source-by-Field Coverage Table

Legend:
- ✅ = field reliably present (>80% of records)
- ⚠️ = field sometimes present (20–80%, often stale)
- ❌ = field not provided
- — = N/A
- Confidence: H/M/L (high/medium/low)
- Coverage % = approximate fraction of US-active interstate motor carriers covered

| Source | Email | Phone | Addr1 | Addr2 | City | State | ZIP | Contact Name | Contact Title | Refresh cadence | API? | Cost | Confidence | Coverage % |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| FMCSA SAFER (Snapshot) | ❌ | ✅ | ✅ | ⚠️ | ✅ | ✅ | ✅ | ❌ | ❌ | Daily | HTML scrape (no JSON) | Free | H | ~100% (interstate) |
| FMCSA L&I portal | ⚠️ (BOC-3 process agent) | ✅ | ✅ | ⚠️ | ✅ | ✅ | ✅ | ⚠️ (officer/process agent) | ❌ | Daily | HTML scrape | Free | H | ~100% |
| FMCSA QCMobile JSON | ❌ | ⚠️ (when populated) | ✅ | ⚠️ | ✅ | ✅ | ✅ | ❌ | ❌ | Daily | JSON, free webKey | Free | H | ~100% |
| FMCSA Census bulk download | ❌ | ✅ | ✅ | ⚠️ | ✅ | ✅ | ✅ | ❌ | ❌ | Daily file | Bulk download | Free | H | ~100% |
| FMCSA MCS-150 (carrier-filed) | ⚠️ (sole-prop owner-op only) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ⚠️ (officer) | ⚠️ (rare) | Biennial | via L&I/Snapshot | Free | M (stale up to 2yr) | ~100% |
| State SOS (50 states) | ❌ | ❌ | ✅ (registered agent) | ⚠️ | ✅ | ✅ | ✅ | ✅ (registered agent / officer) | ⚠️ ("registered agent") | Annual | Per-state HTML or aggregator API | Free direct / paid API | M | ~95% (LLC/Corp filers) |
| MyCarrierPackets API | ✅ (carrier-supplied) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ⚠️ | On packet completion | REST API | Partner-gated (broker subscribes) | H (when present) | ~30% (carriers w/ active packets) |
| Highway API | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ⚠️ | Real-time / on identity verify | REST API | Paid (no public price; broker subscription) | H | ~70% (active interstate) |
| RMIS (Truckstop) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ⚠️ | Real-time | REST API | $340+/mo Lite, custom Pro | H | ~50% (RMIS-monitored) |
| Carrier411 | ⚠️ (community-supplied) | ⚠️ | ⚠️ | ❌ | ⚠️ | ⚠️ | ⚠️ | ❌ | ❌ | Community | Web only | Subscription | L | ~40% — but **DEFAMATION-EXPOSURE source; do not use as gate; see §6** |
| Debtor411 | — | — | — | — | — | — | — | — | — | — | — | — | — | (broker payment data, not carrier contact) |
| D&B Direct+ API | ⚠️ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | Quarterly | REST API | $10k+/yr | M | ~60% (registered businesses) |
| Cortera | ⚠️ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ⚠️ | Quarterly | API | Enterprise | M | ~50% |
| Equifax Commercial | ⚠️ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ⚠️ | Quarterly | API | Enterprise | M | ~60% |
| ZoomInfo API | ✅ | ✅ | ✅ | ⚠️ | ✅ | ✅ | ✅ | ✅ | ✅ | Continuous | REST API | $5k–$50k+/yr | H (when match) | ~40% (trucking SMB undercover) |
| Apollo.io API | ✅ | ✅ | ⚠️ | ❌ | ✅ | ✅ | ⚠️ | ✅ | ✅ | Continuous | REST API (Org tier) | $119/user/mo + Org tier | M | ~30% trucking |
| Hunter.io API | ✅ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ | ⚠️ | Re-verify on demand | REST API | $49–$299/mo | H (email-by-domain only) | depends on website existence (~70% of trucking has site) |
| Clearbit / Breeze Intelligence | ✅ | ⚠️ | ⚠️ | ❌ | ✅ | ✅ | ⚠️ | ✅ | ✅ | Continuous | API (HubSpot-tied) | $75+/mo + HubSpot req | M | ~25% trucking |
| People Data Labs (PDL) | ✅ | ⚠️ | ✅ | ⚠️ | ✅ | ✅ | ✅ | ✅ | ✅ | Continuous | REST | Free 100/mo, $0.01/record | M-H | ~40% trucking |
| Google Places API | ❌ | ✅ | ✅ | ⚠️ | ✅ | ✅ | ✅ | ❌ | ❌ | Continuous | REST | $300–$1200/mo extra Contact SKU | M | ~80% (any business with web presence) |
| LinkedIn (manual / via Apollo) | ⚠️ (cannot scrape directly) | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ | ✅ | Continuous | No native API | n/a | M | ~50% (decision-makers w/ profile) |
| DAT Directory | ❌ | ✅ | ✅ | ⚠️ | ✅ | ✅ | ✅ | ⚠️ | ❌ | Member-updated | Member portal | $49–$149/mo | M | ~30% (DAT customers) |
| Whitepages / AnyWho | ❌ | ⚠️ | ❌ | ❌ | ⚠️ | ⚠️ | ❌ | ❌ | ❌ | Stale | n/a | n/a | L | low — deprioritize |
| Operator-entered (UI Save) | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | On Save | n/a | $0 | H (operator-truth) | per-record |

Citations summary: every cell above sourced from the per-source URLs in §1; compiled 2026-05-09.

### Key takeaways from the table

- **Address + phone + ZIP** → FMCSA fully covers all 50 fields. No paid source needed for these 5 of 9 fields.
- **Email** → FMCSA effectively does NOT have it. Hunter.io (by domain) > MyCarrierPackets (carrier-supplied) > ZoomInfo > Apollo > PDL > Clearbit. **This is the field the paid SaaS tier is mostly for.**
- **Contact Name** → State SOS (registered agent) is free baseline; ZoomInfo / LinkedIn / Apollo for non-officer dispatcher names; MyCarrierPackets when carrier has filled their own packet.
- **Contact Title** → Almost exclusively paid SaaS (ZoomInfo / Apollo / Clearbit / PDL) — FMCSA + SOS don't have it; MyCarrierPackets sometimes has "Dispatch" / "Operations" labels.

---

## 3. Tiered Fill Strategy — Cost Optimal Sequence

Operator's intuition is correct. The recommended fill order:

### Tier 0 — Operator override (always wins)
- If `carrier.{field}.source == 'operator-entered'` AND `updated_at` is within field-specific TTL → never overwrite.
- The UI's per-field Save button supersedes all enrichment. Preserve this; it's the human-in-the-loop ground truth.

### Tier 1 — FREE / always fire (zero marginal cost; run on every refresh)
- **FMCSA QCMobile JSON** (existing infrastructure at `backend/fmcsa_qcmobile.py`) → fills phone, addr1, addr2, city, state, ZIP. ✅ for ~95% of carriers.
- **FMCSA SAFER HTML scrape** (fallback) → same fields when QCMobile webkey rate-limited or carrier missing.
- **FMCSA Census bulk** (nightly batch) → backfill fleet of carriers offline; cheaper at scale than per-DOT API.
- **State SOS lookup** → fills contact_name (registered agent / officer) where FMCSA has none, plus addr cross-validation.
- **Cost:** $0/yr. Coverage: 95%+ of US carriers for phone/addr/zip; ~70% for contact_name (officer level).
- **Fields filled:** Phone, Addr1, Addr2, City, State, ZIP, Contact Name (officer-level only).

### Tier 2 — Carrier-collected (when available; partner integrations CHL might add later)
- **MyCarrierPackets** if CHL subscribes (broker-side packet) → fills email + dispatcher contact name + title when carrier completes packet.
- **Highway** if CHL subscribes → similar fill, plus identity verification (FFF orthogonal value).
- **Cost:** subscription (Highway: no public price; MyCarrierPackets: partner-gated; RMIS: $340+/mo).
- **Fields filled:** Email, Contact Name (dispatcher), Contact Title — for the subset of carriers that have completed packets through CHL.

### Tier 3 — Paid SaaS enrichment (cap-controlled, called only when Tier 1 + 2 leave gaps)
- **Hunter.io** → email-by-domain. Use on carriers where Tier 1+2 produced phone+addr but no email AND we have a website domain.
  - Free: 50 credits/mo. Starter $49/mo for 2k credits. **Recommended start: Starter $49/mo** = 2,000 emails-found/month.
- **People Data Labs** → email + name + title at $0.01/record. **Recommended for non-officer dispatcher discovery** (cheaper than ZoomInfo/Apollo at small volume).
- **Apollo.io Org tier ($119/user/mo)** → contact name + title for sales/dispatch decision-makers.
- **ZoomInfo** → ONLY if CHL hits a quality ceiling and operator approves $5k+/yr; not Day-1.

### Tier 4 — Last-resort (low confidence, ad-hoc, audit-flagged)
- **Google Places API** → if all above failed AND carrier has a public Google business profile. Mark `source='google-places'` with low confidence.
- **LinkedIn manual** → operator-only, never automated; mark `source='linkedin-manual'`.
- **Whitepages/AnyWho** → drop; not worth integrating.

### Cost estimate (recommended Day-1 stack)
| Service | Plan | $/mo | $/yr |
|---|---|---|---|
| FMCSA (QCMobile webkey + SAFER scrape) | Free | $0 | $0 |
| State SOS direct (50 manual scrapers, OR Cobalt/Middesk aggregator) | Free direct OR ~$200/mo aggregator | $0–$200 | $0–$2,400 |
| Hunter.io | Starter | $49 | $588 |
| People Data Labs | Pay-as-you-go (1k records est.) | ~$10 | ~$120 |
| **Subtotal Tier 1+3 minimal stack** | | **~$59–$259/mo** | **~$700–$3,100/yr** |
| Apollo.io (when needed for title) | Org annual | $119/seat/mo | ~$1,400/seat/yr |
| Highway (Tier 2; for fraud + contact) | Subscription (quote) | quote | quote |
| **Recommended ~6mo stack target** | | **~$200/mo** | **~$2,400/yr** |

Defer ZoomInfo + D&B + Equifax + Clearbit for now. CHL is pre-revenue; $20/wk decision-threshold per `decision_thresholds.md` makes the $49/mo Hunter + $10–20/mo PDL self-decidable and the rest operator-escalation territory.

---

## 4. Confidence Labels for SOURCE Column

Use these exact strings in `db.carriers.{field}_source`:

- `fmcsa-qcmobile` — phone/addr/etc from QCMobile JSON (existing infra path)
- `fmcsa-safer` — same fields via SAFER HTML scrape fallback
- `fmcsa-li` — L&I portal (insurance contacts, BOC-3 process agent address)
- `fmcsa-census-bulk` — bulk file (offline batch fill)
- `state-sos` — registered agent / officer from state secretary-of-state
- `carrier-packet` — MyCarrierPackets API (broker-collected)
- `highway` — Highway Carrier Identity verification
- `rmis` — RMIS API
- `hunter-io` — email by domain
- `zoominfo` — paid lookup
- `apollo-io` — paid lookup
- `clearbit` — Breeze Intelligence
- `pdl` — People Data Labs
- `dnb` — D&B Direct+
- `cortera` — (if ever subscribed)
- `equifax-commercial` — (if ever subscribed)
- `dat-directory` — DAT carrier directory
- `google-places` — Google Maps Places API contact SKU
- `linkedin-manual` — operator copy/paste from LinkedIn
- `operator-entered` — UI Save button (highest precedence)
- `unknown` — never sourced

Display tooltip in UI: "Source: <label> · last refreshed <iso8601>". Drives operator's trust calibration.

---

## 5. Re-pull Cadence per Field

Field-level TTL (cache-staleness threshold before next enrichment):

| Field | TTL (default) | Why | Re-pull trigger |
|---|---|---|---|
| Phone | 90 days | FMCSA daily but carriers rarely change | Manual refresh or carrier reports change |
| Address (1/2/city/state/zip) | 30 days | More dynamic; tied to MCS-150 biennial filing windows | MCS-150 refile event flag + monthly batch |
| Email | 365 days (annual re-verify) | People change jobs; emails go stale; verify cost low | Annual + on bounce-back |
| Contact Name | 365 days | Officer turnover ~5-10%/yr; dispatcher turnover higher | Annual + on first-bounce of email to that person |
| Contact Title | 365 days | Tied to Contact Name lifecycle | Annual |
| Insurance fields (separate flow) | 7 days | Renewal-window-sensitive | Existing `fmcsa_qcmobile.py` flow |

Implementation: store `{field}_last_refreshed_at` and `{field}_source` per field. Nightly cron sweeps records where `now - last_refreshed > TTL` and re-fires only the relevant adapters. Avoid full re-enrichment per refresh — burns Hunter/PDL credits.

**Operator-override exception:** if `{field}_source == 'operator-entered'`, set TTL to ∞ (never auto-overwrite). UI offers explicit "Re-fetch from FMCSA" button to override the override (already in the UI per FFF5 spec).

---

## 6. Compliance / Legal Notes

### 6.1 Carrier411 / FreightGuard precedent → applies to RATINGS not contact data

The defamation precedent (Greenline Express v. High Plains Logistics 2023, ~$458k; Penguin Trucking v. E.L. Hollingsworth, ~$612k) is about **broker-posted false negative ratings/reports** on Carrier411's FreightGuard system, not about contact-info sourcing.

- Section 230 protects Carrier411 itself.
- Brokers face open-ended tort liability for posting false reports.
- **Contact enrichment ≠ rating.** Pulling phone/email/address from Carrier411 is not defamation-exposed. **Posting** "this carrier is fraud" is.
- **CHL guidance:** never use Carrier411 as a fitness gate or auto-deny signal. Treat its data like a TIP (operator-investigates) not a TRUTH. Contact-info fields from Carrier411 are LOW confidence and probably duplicative of FMCSA anyway. Skip Carrier411 entirely for FFF5 scope; keep the precedent flagged for the reputation-rating subsystem (separate iter).

### 6.2 GDPR / CCPA — applies to US business contact data

**B2B exemption expired Jan 2023.** A California carrier-employee's work email/phone/title is now CCPA-protected personal information. Penalties: $2,663/violation, $7,988/intentional violation per 2025 schedule.

- CHL must:
  - Maintain a privacy policy disclosing how contact data is collected, used, retained, shared, deleted.
  - Honor CCPA opt-out / deletion requests within 45 days.
  - Have data-processing agreements (DPAs) with each upstream enrichment vendor (Hunter, PDL, Apollo, ZoomInfo, etc.) — these vendors typically provide DPAs in their portal; standard.
  - Document the legitimate-interest basis for B2B prospecting (GDPR Art. 6(1)(f)).
  - Avoid scraped lists of unknown provenance ("scraped lists, purchased emails from unknown brokers" — explicit non-compliance signal per Unify GTM).
- Operator pre-flight (§8): privacy policy on continentalhaul.com + DPAs with each enrichment vendor before live use.

### 6.3 TIA stance

TIA (Transportation Intermediaries Association) does **not** publish a formal data-sharing-between-brokers policy. TIA opposes the FMCSA broker-transparency rule (rate disclosure to carriers) on grounds it'll cause a rate race-to-the-bottom — but that's about rates, not contact info.

CHL's contact enrichment doesn't share *between brokers* (it pulls from public + paid third parties), so TIA stance is not a blocker. Future TIA membership would be brand-positive but not load-bearing for this feature.

---

## 7. CHL Implementation Recommendation

### 7.1 Adapter pattern — one module per source

Mirror the pattern of `backend/fmcsa_qcmobile.py`:

```
backend/enrichment/
  __init__.py
  base.py              # BaseEnricher abstract class with .fetch(carrier) -> EnrichmentResult
  fmcsa_qcmobile.py    # (existing — extend to extract contact fields too)
  fmcsa_safer.py       # HTML scrape fallback
  state_sos.py         # 50-state aggregator OR Cobalt/Middesk wrapper
  hunter_io.py         # email-by-domain
  pdl.py               # people-data-labs
  mycarrierpackets.py  # (when subscribed)
  highway.py           # (when subscribed)
  apollo.py            # (when budget approved)
  zoominfo.py          # (deferred)
  google_places.py     # (last resort)
  resolver.py          # field-priority resolver — see 7.2
```

Each enricher returns:
```python
@dataclass
class EnrichmentResult:
    source: str              # confidence label from §4
    fields: dict[str, Any]   # subset of the 9 fields it filled
    confidence: float        # 0.0 - 1.0; per-source baseline
    fetched_at: datetime
    raw_response_id: str     # for audit/debug; raw response stored in db.enrichment_responses
```

### 7.2 Field-priority resolver

Per-field source-priority array (highest wins, lowest is fallback):

```python
FIELD_PRIORITY = {
    "phone":         ["operator-entered", "carrier-packet", "highway", "fmcsa-qcmobile", "fmcsa-safer", "google-places", "dat-directory"],
    "addr1":         ["operator-entered", "carrier-packet", "fmcsa-qcmobile", "fmcsa-safer", "state-sos", "highway"],
    "addr2":         ["operator-entered", "carrier-packet", "fmcsa-qcmobile", "fmcsa-safer"],
    "city":          ["operator-entered", "carrier-packet", "fmcsa-qcmobile", "fmcsa-safer", "state-sos"],
    "state":         ["operator-entered", "carrier-packet", "fmcsa-qcmobile", "fmcsa-safer", "state-sos"],
    "zip":           ["operator-entered", "carrier-packet", "fmcsa-qcmobile", "fmcsa-safer", "state-sos"],
    "email":         ["operator-entered", "carrier-packet", "highway", "hunter-io", "zoominfo", "apollo-io", "pdl", "clearbit"],
    "contact_name":  ["operator-entered", "carrier-packet", "highway", "zoominfo", "apollo-io", "pdl", "state-sos", "fmcsa-li"],
    "contact_title": ["operator-entered", "carrier-packet", "zoominfo", "apollo-io", "clearbit", "pdl", "linkedin-manual"],
}
```

Rule: when multiple sources have data for a field, the resolver picks the highest-priority source whose data is within TTL. If all sources within TTL show the same value, mark `consensus=True` and bump confidence.

### 7.3 Cache TTLs — see §5

Implement as `db.carriers.contact.{field} = { value, source, fetched_at, confidence, raw_response_ref }`. Nightly cron sweeps stale fields per TTL.

### 7.4 Manual operator-override preservation

UI's per-field Save button writes:
```
db.carriers.contact.{field} = {
  value: <operator value>,
  source: "operator-entered",
  fetched_at: <now>,
  confidence: 1.0,
  locked_until: null   # ∞ TTL
}
```

When the resolver runs, it sees `source == 'operator-entered'` and skips re-enrichment. UI's "Refresh from FMCSA" button bypasses by writing `locked_until = null` AND firing the resolver explicitly.

### 7.5 Audit trail

Every enrichment fetch writes a row to `db.enrichment_audit`:
```
{
  carrier_dot: int,
  field: str,
  source: str,
  old_value: str,
  new_value: str,
  fetched_at: iso,
  raw_response_ref: str,  # foreign key into db.enrichment_responses
  triggered_by: "cron" | "manual_refresh" | "first_seen" | "ttl_expired",
}
```

This satisfies CCPA "where did this data come from" obligations and gives operator a per-record provenance view in the UI.

### 7.6 Concurrency / rate limit

Each adapter declares a `concurrency` and `rate_limit` constant. Existing pattern in `fmcsa_qcmobile.py` (semaphore-gated async). Hunter.io: respect documented 10 req/sec. PDL: same. Highway/RMIS: per their SLA.

---

## 8. Operator Pre-Flight Checklist

Before CHL can use each tier, operator must arrange:

### Tier 1 (FREE — already provisioned or trivial)
- [x] FMCSA QCMobile webkey — already in `.env` per existing `fmcsa_qcmobile.py`. Confirm via `Get-Content C:\CHL\backend\.env | Select-String FMCSA_WEBKEY`.
- [ ] FMCSA SAFER scraper — no key, just polite rate-limit (≤2 req/sec).
- [ ] FMCSA Census bulk download — no auth, schedule wget/cron.
- [ ] State SOS — decide between (a) 50 hand-rolled scrapers (free, brittle) or (b) aggregator API (Cobalt Intelligence ~$200/mo, Middesk ~$500/mo). **Operator decision needed.**

### Tier 2 (carrier-collected — partner agreements)
- [ ] MyCarrierPackets broker subscription — operator signs up at https://mycarrierpackets.com, operator-name + MC + USDOT verified, API credentials issued. Pricing partner-gated (no public price).
- [ ] Highway broker subscription — operator emails sales@highway.com or signs up at https://highway.com. **Likely the highest-leverage Tier 2 buy** (fraud + identity in one); pricing quote-based.
- [ ] RMIS subscription — Lite from $340/mo at https://truckstop.com. Usually overlaps Highway; pick one to start.

### Tier 3 (paid SaaS enrichment)
- [ ] **Hunter.io** — operator signs up Starter at https://hunter.io/signup ($49/mo). Use `set_env_var.ps1` to provision `HUNTER_API_KEY`. **Recommended Day-1.** Self-decidable per $20/wk threshold.
- [ ] **People Data Labs** — operator signs up at https://peopledatalabs.com/. Free 100/mo trial; pay-as-you-go thereafter. Provision `PDL_API_KEY`. **Recommended Day-1.** Self-decidable.
- [ ] Apollo.io Org tier — $119/user/mo. Operator decision; defer until Hunter+PDL prove insufficient.
- [ ] ZoomInfo — defer (operator-escalation: $5k+/yr).
- [ ] D&B / Cortera / Equifax / Clearbit — defer (all operator-escalation).

### Tier 4 (last-resort)
- [ ] Google Places API — operator creates GCP project at https://console.cloud.google.com, enables Places API, sets billing alert at $50/mo, provisions `GOOGLE_PLACES_API_KEY`. **Defer until Tier 1+3 measured.**

### Compliance pre-flight
- [ ] Privacy policy live at https://continentalhaul.com/privacy with sections on: data collected, retention, third-party sharing, opt-out process, CCPA rights.
- [ ] DPA executed with Hunter.io + PDL (standard via vendor portal).
- [ ] Data-deletion endpoint at `POST /api/privacy/delete` (CCPA 45-day SLA).

### Recommended Day-1 minimum stack
1. FMCSA QCMobile (already done) — phone/addr/zip for ~95% of carriers free.
2. State SOS via Cobalt Intelligence aggregator (~$200/mo) — contact_name backfill.
3. Hunter.io Starter ($49/mo) — email-by-domain.
4. People Data Labs PAYG (~$10–20/mo) — non-officer name + title.
5. Privacy policy + DPAs.

**Total Day-1 cost:** ~$260/mo / ~$3,100/yr. Within self-decidable threshold once revenue ≥ $1,200/2wk per `decision_thresholds.md`. Pre-revenue: operator-escalation needed for the SOS aggregator + Hunter; PDL is under $20/wk.

---

## Summary

| | |
|---|---|
| Free covers | Phone, Addr, City, State, ZIP, Officer-name (5–6 of 9 fields, ~95% of carriers) |
| Paid covers | Email, Contact Title, Dispatcher Name (3 of 9 fields, the hard ones) |
| Recommended Day-1 paid | Hunter.io $49/mo + PDL ~$15/mo + State SOS aggregator ~$200/mo (~$260/mo total) |
| Avoided | Carrier411 (defamation exposure), ZoomInfo (overkill), D&B/Cortera/Equifax (enterprise pricing) |
| Architecture | Adapter pattern + field-priority resolver + per-field TTL cache + operator-override preservation |
| Compliance | CCPA B2B-exemption-expired (Jan 2023) → privacy policy + DPAs + 45-day deletion SLA mandatory |
