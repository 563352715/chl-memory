# Partner Platform Profiles — canonical CHL identity values

**Created:** 2026-05-11
**Purpose:** Single source of truth for "fill this same form 20 times across
load boards / factors / TMSes / shipper portals" identity data. Prevents
re-derivation drift (wrong phone number, wrong address, prefixed DOT, etc.)
when registering on a new partner platform.

**When to use:** any new partner signup form (load board, factor, TMS, shipper
portal, FMCSA registration update, insurance binder, banking application).

---

## Canonical block (copy-paste ready)

```
Legal Entity Name:       Continental Haul Logistics LLC
DBA:                     Continental Haul Logistics
Entity Type:             LLC (Missouri, sole-member)
EIN:                     42-2003434
DOT Number:              4569843
MC Number:               1817555
SCAC:                    (not assigned)
NAICS:                   484110 (General Freight Trucking, Local)
                         484121 (General Freight Trucking, Long-Distance, TL)

Dispatch Phone:          (866) 490-6433        [toll-free, customer-facing]
Direct Phone:            (417) 219-3856        [operator cell]
Fax:                     (none)

Email (primary):         dispatch@continentalhaul.com
Email (operator):        jasonandpugee@gmail.com  [DO NOT use for partner forms]

Website:                 https://continentalhaul.com
Carrier Onboarding URL:  https://continentalhaul.com/onboard
Terms of Service URL:    https://continentalhaul.com/terms
Privacy Policy URL:      https://continentalhaul.com/privacy
SMS Opt-In URL:          https://continentalhaul.com/opt-in

Mailing / Physical Address:
    618 W Greenwood St
    Springfield, MO 65807
    United States

Operator (Responsible Party):
    Jason Meyer
    Title: Owner / Member-Manager
    Email: dispatch@continentalhaul.com
    Phone: (417) 219-3856
```

---

## Field-formatting gotchas

| Field | DO | DON'T |
|---|---|---|
| DOT Number | `4569843` (bare numeric) | `DOT-4569843`, `USDOT 4569843` (some platforms auto-prefix; do not include the prefix yourself) |
| MC Number | `1817555` (bare numeric) | `MC-1817555`, `MC#1817555` |
| Phone | `(866) 490-6433` (US format) | `+18664906433`, `866.490.6433` (some FMCSA-cross-reference platforms reject other formats) |
| Email | `dispatch@continentalhaul.com` | `jasonandpugee@gmail.com` (operator's personal email — only use if a platform refuses business domains, which is rare) |
| Contact Name | `Jason Meyer` (for FMCSA-verification contexts) OR `Dispatch` (for outbound email signatures) | The standing outbound-email signing rule is "Dispatch / Continental Haul Logistics LLC". Partner-platform Contact Name fields are an exception — they expect a real responsible-party name and may cross-check it against FMCSA SAFER (which lists Jason Meyer as the registrant). When in doubt, use Jason Meyer for partner-form fields. |
| Website | `continentalhaul.com` (no protocol) OR `https://continentalhaul.com` (with) — depends on platform validation; if both fail, try without trailing slash | `www.continentalhaul.com` — drop the `www.`; we don't redirect that subdomain |

---

## Signed-up platforms (registry)

| Platform | Status | Account email | Notes |
|---|---|---|---|
| DAT | ACCOUNT CREATED + SUBSCRIPTION PURCHASED (2026-05-12) | dispatch@continentalhaul.com | Tier: **DAT One Select Broker $319/mo** ($3,828/yr). Email verification pending; account activation up to 4hr. Subscription includes CarrierWatch (carrier vetting + insurance certs), Market Rate via DAT iQ, TMS & FTP Integration. Credentials + recovery code in vault (ids 8551680a + a07db203). API cert queue request sent to developersupport@dat.com 2026-05-12 — homegrown API requires few-week certification process per DAT integration team. API is free for first 3 users post-certification. |
| Truckstop.com | ACCOUNT CREATED + INQUIRY SENT (2026-05-11) | dispatch@continentalhaul.com | Credentials in vault (id 83ad7f98-830a-475e-87ba-26a6952bd56a). API inquiry to partnerships@truckstop.com awaiting response. |
| 123Loadboard | INQUIRY SENT (2026-05-11) | — | SSO blocked w/ AADSTS50020; email to partner-integrations@123loadboard.com awaiting response |
| Direct Freight | ACCOUNT CREATED + BROKER-VERIFY QUEUED (2026-05-11) | dispatch@continentalhaul.com | Profile filled per canonical block above. Mobile SMS verified to personal cell 816-560-9180 (bridge until Telnyx 2026-05-14, see backlog O18b). Broker-authority verify requested via manual callback at 866-490-6433 — DirectFreight to call (business hours Mon 2026-05-12). FMCSA `Carriers Only` block stays N/A (broker, doesn't apply). **Credentials not yet in vault — operator to add.** |
| Uber Freight | REMOVED 2026-05-11 | — | No broker portal — they ARE the broker. Carrier login would require us to re-broker their freight (illegal). |
| Loadsmart | REMOVED 2026-05-11 | — | No broker portal — they ARE the broker. Nav has Shippers / Carriers / Docks & Yards only. **Note:** Loadsmart offers their own factoring product — consider for D2 factor decision. |
| TruckSmarter | ACCOUNT CREATED + BROKER REVIEW QUEUED (2026-05-11) | dispatch@continentalhaul.com | Broker account created. Free (no posting/booking fees). Account is in Broker Success review (24-72hr typical) — API + Access Settings locked until approval. Watch dispatch@continentalhaul.com inbox for review-complete email. 500,000+ MC-verified carriers in network. Credentials in vault (id 4ce4414b-da5b-4e61-a70d-07b1676d7cb6). |
| NextLOAD | APPLICATION SENT 2026-05-11 | dispatch@continentalhaul.com | Free broker access for Apex Capital factoring clients (broker-side is also free + vetted). Application email sent to support@nextload.com requesting broker-vetting steps + documentation requirements. Email flags Apex as factor option under D2 evaluation. **Pairing strategy:** if D2 selects Apex → NextLOAD becomes amplified-value tool (load posts reach pool of pre-vetted Apex factoring carriers). Operated by Apex Capital — 301 Commerce St Suite 1200, Fort Worth, TX 76102 / 844-827-7700. |
| FR8Star | DEFERRED (pricing-gated) 2026-05-11 | — | NO FREE TIER for brokers — Basic $49/wk = $2,548/yr, Pro $129/wk = $6,708/yr. Exceeds $20/wk pre-revenue threshold. Heavy-haul / oversize specialty — useful future vertical experiment when we have lowboy/RGN/step-deck carrier relationships. Revisit post-Phase-1 launch. |
| SAM.gov | API LIVE but data quality blocked (2026-05-11) | dispatch@continentalhaul.com | API key provisioned (env: SAM_GOV_API_KEY). Adapter at `loadboards.py:145`. HTTP 200 OK. **BUT:** deeper smoke test 2026-05-11 found SAM.gov's `naics=` filter is unreliable — asking for trucking NAICS returns light fixtures / health insurance / shipyard parts. <1% of records are actual freight. **Backlog B1b is REQUIRED before SAM.gov is usable as a load source.** Also pending: entity registration (UEI/CAGE) to actually BID on federal contracts. |
| uShip | REMOVED 2026-05-11 | — | Wrong product fit (consumer C2C, not B2B brokerage). |
| ComFreight Haul Pay | APPLICATION PENDING (>1-2 biz day SLA) | dispatch@continentalhaul.com | Follow-up email sent 2026-05-11 |

---

## When you fill a new partner platform

1. Open this doc. Copy the canonical block.
2. Map fields 1:1. If platform asks for something not in the block (e.g., "Years in Business: __"), pause and ask the operator before guessing.
3. After save, add a row to the "Signed-up platforms (registry)" table above.
4. Store login credentials in `db.vault_entries` (Fernet-encrypted) — never in plaintext anywhere.
5. **SMS verification — known constraint:** (417) 219-3856 is a VoIP number (Twilio → migrating to Telnyx). There is NO traditional cellular carrier. Email-to-SMS-gateway verifications WILL FAIL on the dispatch line. Options:
   - **Skip:** select `Other/Unknown` and accept that partner SMS notifications won't reach.
   - **Bridge with personal cell:** use operator's personal cellular number for the SMS-verify step, then swap to dispatch line later. Bridge approach is in use on DirectFreight as of 2026-05-11 — see master backlog O18b for the swap-back reminder. **DO NOT** leave personal cell on partner platforms long-term (privacy + mixing personal/business audit trail).
   - **Long-term fix:** see master backlog O18 (Mint Mobile prepaid line, or test whether Telnyx passes carrier checks after FOC 2026-05-14).

---

## Stable refs (don't change without operator)

- FMCSA SAFER public record: https://safer.fmcsa.dot.gov/CompanyProfile.aspx?usdot=4569843
- FMCSA L&I (broker authority): https://li-public.fmcsa.dot.gov/LIVIEW/pkg_carrquery.prc_carrlist?n_dotno=4569843
- Missouri Secretary of State LLC record: (operator to add link if useful)
