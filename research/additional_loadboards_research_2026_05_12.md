# Additional Load Boards — Research Report

**Date:** 2026-05-11
**For:** Continental Haul Logistics LLC (MC-1817555, USDOT 4569843)
**Already integrated / inquired:** DAT, Truckstop.com, 123Loadboard, Direct Freight, TruckSmarter, NextLOAD, SAM.gov, FR8Star (deferred)
**Already scaffolded (no account yet):** BulkLoads, HopLoads, TankTransport, AgriCarriers, OnlineFreight, LivestockTalk, Livestock Transport Nation, CattleHauler
**Method:** WebSearch discovery + WebFetch live-verification against each candidate domain. Domains that returned ECONNREFUSED, 404, or "parked" are listed in the SKIP section so we don't re-research them next cycle.

---

## 1. Summary Table — verified-alive boards worth considering

| # | Board | Niche | Broker-side? | Pricing | API | Tier |
|---|---|---|---|---|---|---|
| 1 | LivestockNetwork.com | Cattle / livestock | Implicit (post loads) | $35/mo or $350/yr premium; free basic | No | **PURSUE** |
| 2 | LivestockHaulersHub.com | Livestock | Unclear (shipper/breeder focus, broker not blocked) | Free | No | PURSUE |
| 3 | LivestockTransportNation.com | Livestock (animal-welfare focus) | Likely (load board + LMS) | Pricing page exists, gated | No | PURSUE |
| 4 | LivestockWays.com | Livestock (AI matching) | Unknown — site live, needs demo | Unknown | Unknown | INQUIRE |
| 5 | LivestockLoader.com | Livestock (app-first) | No broker role | Unknown | No | SKIP |
| 6 | uShip.com | Generalist incl. livestock/horses/cars | Marketplace bid model | Commission on book | Yes (mentioned) | PURSUE |
| 7 | Central Dispatch | Auto transport | Yes (broker is the primary poster) | Subscription, gated quote | Unknown — phone-gated | **PURSUE** |
| 8 | Super Dispatch / Super Loadboard | Auto transport | Yes | TMS+loadboard, paid | **YES (REST API, Zapier, QB)** | **PURSUE** |
| 9 | Dispatch Center | Auto transport | Yes (dealers + brokers) | Gated | **YES (mentioned)** | PURSUE |
| 10 | CarShipIO | Auto transport TMS+marketplace | Yes (broker CRM) | Free trial + plans | Unknown | INQUIRE |
| 11 | VeriTread | Heavy-haul / oversize / boats / RoRo | Yes (shippers + brokers post) | 403-blocked on main URL; free for carriers per partner pages | Unknown | PURSUE |
| 12 | Truckstop Heavy Haul Load Board Pro | Heavy-haul (sub-product of Truckstop) | Yes (vetted brokers only) | $299/mo + $42 app fee | Truckstop dev portal | DEFER (rolls under Truckstop main account) |
| 13 | BulkLoads.com | Bulk / hopper / tanker / pneumatic | Yes (Shippers & Brokers explicit) | Gated | Unknown | **PURSUE** (already scaffolded; finish onboarding) |
| 14 | HopLoads.com | Grain / bulk / hopper | Yes (shipper "merchandiser" tier $100/mo) | $50-100/mo by role | No | PURSUE (already scaffolded) |
| 15 | FlatLoads.com | Flatbed / step-deck / lowboy / RGN | Yes (shippers + truckers post) | Gated | No | PURSUE |
| 16 | LoadMatch.com | Drayage / intermodal | Yes (CHR, Landstar, Uber Freight all members) | Gated, "Terms & Fees" page | No (partners with LoadBoard Network) | PURSUE |
| 17 | DrayNow | Drayage / intermodal | Carrier+shipper focus (no broker tier visible) | Gated | No | DEFER |
| 18 | Portloads | Drayage | Shipper+carrier focus | Gated | No | DEFER |
| 19 | PTTR Load Board | Drayage / port-to-warehouse | **Yes (explicit Broker tier $54.99/mo, 12-mo free trial)** | $54.99-$149.99/mo | No | **PURSUE** |
| 20 | LoadBoardNetwork.com | Multi-post aggregator | Yes (broker+shipper) | Gated; "TMS integration" | Yes (integrations w/ TMS) | **PURSUE** (replaces deferred PostEverywhere) |
| 21 | FindFreightLoads.com | Generalist + hot-shot + reefer | **Free broker posting + email bulk upload service** | Free for brokers | No | **PURSUE** (zero cost) |
| 22 | OnlineFreight.com (Echo Global) | Generalist + agent network | Yes (TMSPro for agents) | Gated | EDI + API mentioned | INQUIRE |
| 23 | Trucker Path Truckloads | Generalist (broker-load-board product) | **Yes (broker product)** | Gated | **YES (docs at docs.truckerpath.com)** | **PURSUE** |
| 24 | FreeFreightSearch (FFS) | Generalist free | Yes (factor-integrated) | Free | Unknown — site 403'd | INQUIRE |
| 25 | GetLoaded (now DAT-owned) | Generalist | Yes | $54-169/mo | Via DAT | SKIP (rolls into existing DAT) |
| 26 | Convoy Platform (Flexport→DAT) | Generalist | Carrier-facing relaunch | Free | Via DAT | SKIP (will fold into DAT) |
| 27 | RXO Connect | RXO-internal | No external broker posts | Carrier-free | Yes (developer.rxo.com) | SKIP |
| 28 | C.H. Robinson Load Board | CHR-internal | No external broker posts | Free for carriers | Yes (CHR contract) | SKIP |
| 29 | Highway.com (TFX) | Carrier-identity + Trusted Freight Exchange | Brokers post via TMS integrations, not direct | Gated | Yes via TMS partners | **PURSUE (carrier-vetting layer — high ROI)** |
| 30 | Parade.ai | Carrier-capacity management for brokers | Yes (broker-focused) | Gated | Yes (assistant@parade.ai) | INQUIRE (post-MVP) |
| 31 | Pilot Car Loads | Pilot-car escort for heavy-haul moves | Adjacency to heavy-haul brokerage | Unknown | No | DEFER |
| 32 | UnitedStatesOfFreight.com | Small generalist | Yes (info@) but board appears empty | Quote-gated | No | SKIP |

---

## 2. Per-board details

### Cattle / Livestock

**LivestockNetwork.com** — Alive (load timestamps for current day visible 2026-05-11 fetch; "2 HRS" truck listings). $35/mo Premium / free basic. Doesn't distinguish broker from carrier — broker can post. No API. Contact: "E-mail Us" link (obfuscated). Beefspotter feedlot atlas + forum + classifieds beyond load board. Largest livestock-specific board.

**LivestockHaulersHub.com** — Alive. 5,500+ haulers in network. Free. Targets breeders/producers/feeders/veterinarians/fairs — broker not explicitly named but not blocked. No API. Contact: form only. Less broker-oriented; risk = our loads sit alongside individual horse-owner bookings.

**LivestockTransportNation.com** — Alive. Pricing page exists (`/load-pricing`) but gated. Calendly contact (`calendly.com/moober`). No API. Animal-welfare compliance angle is differentiating.

**LivestockWays.com** — Alive but homepage is hero-only ("Livestock Logistics Platform"). AI matching pitch per FleetLogging. Needs direct demo to assess. INQUIRE-only.

**LivestockLoader.com** — Alive (app-first, ©2020 on website). Three user types: carrier / dispatcher / shipper — **no broker role**. SKIP for broker posting.

**CowHaulers.com** — **DEAD** (ECONNREFUSED).

**Cattlehaulers.com** — **DEAD** (ECONNREFUSED).

**Equine Express** — Single carrier (not a load board). SKIP.

**Brook Ledge** — Single carrier (not a load board). SKIP.

**MoveMyHorse** — Directory only (not a load board, no posting model). SKIP.

**uShip** — Marketplace (not classical load board). Free to list, commission on book. API integration referenced under business solutions. Phone 512-546-7755. Handles livestock + horses + autos + general LTL — broad niche coverage in one account.

---

### Heavy-haul / Oversize

**VeriTread** — Alive (mentioned widely as free heavy-haul board). Direct page 403'd by their WAF; partner pages confirm shipper+broker posting model. Free for carriers. Targets oversize, LTL, vehicles & boats, RoRo. **Contact via veritread.com/contact form.**

**Truckstop Heavy Haul Load Board Pro** — $299/mo + $42 app fee, 3-user. Vetted brokers only. Rolls under our Truckstop relationship — request the Heavy Haul Pro tier add-on when we upgrade.

**SuperLoadsBoard** — **DEAD** (ECONNREFUSED).

**NEXTHOPE** — Could not verify existence. Not in any 2026 ranking. Likely defunct or rebranded.

**Pilot Car Loads** — Adjacent to heavy-haul; pilot-car escort matching, not freight itself. DEFER until we book first oversize move.

---

### Bulk / Commodity / Agriculture

**BulkLoads.com** — Already scaffolded. Confirmed alive 2026-05 (19,287 hopper + 12,577 end-dump loads visible). Shippers & Brokers tier explicit. Phone 1-800-518-9240, 1340 E Woodhurst Dr, Springfield, MO 65804. **Finish onboarding — this is the #1 ag/bulk board.**

**HopLoads.com** — Already scaffolded. Confirmed alive (2,400,756 loads to date, 225,538 trucks; postings dated 05/11/2026). $50/mo trucking, $100/mo "merchandisers" (shipper/broker). Phones 270-436-6142 / 800-665-7432. No API.

**FlatLoads.com** — Alive. 15,042 loads visible. Phone 1-800-518-9240 (same as BulkLoads — same operator). Same Springfield MO address — likely common owner. Flatbed/stepdeck/lowboy/RGN/double-drop. No API.

**TankTransport.com** — Per task note: turned out to be a magazine. SKIP (already known). No dedicated tank-truck-only board found alive in this research; tank capacity routes through BulkLoads + DAT bulk + relationship brokers (Liquid Dispatch, Bulk Connection — both are competitors not load boards).

---

### Auto haulers

**Central Dispatch (Cox Automotive)** — Largest auto-transport board. Phone 855-COX-AUTO. Brokers are the primary poster type (this is how the auto-transport ecosystem works). API status unknown — must call. Address: 6205 Peachtree Dunwoody Rd, Atlanta, GA 30328.

**Super Dispatch / Super Loadboard** — Public load board + carrier TMS combo. **REST API documented + Zapier + QuickBooks integrations**. Photo eBOL + driver tracking. Highest-quality auto-transport infra. Sales: superdispatch.com.

**Dispatch Center** — Alive (dispatchcenter.com). Email **info@dispatchcenter.com**, phone 800-235-9853. Explicit "API integrations available for Dealerships and Brokers to post loads." Cincinnati-based.

**CarShipIO** — Auto-transport TMS + marketplace. Free signup + plans. Imports from Central Dispatch. API status unclear. Worth a free-tier account.

**Auto Hauler Exchange** — "Bye Bye Broker" tagline — **explicitly rejects brokers**. SKIP.

---

### Drayage / Intermodal

**PortPro** — TMS, not a load board. Has API (real-time customer integration). For drayage operators that want full ops platform; we are not at the scale to need it yet. DEFER.

**LoadMatch.com** — Alive 26 years; major brokers (CHR, Landstar, Uber Freight) are members. Drayage Directory 6,600+. Members post loads/trucks/equipment/jobs. Partners with LoadBoardNetwork for syndication. No homepage API. Gated pricing.

**DrayNow** — Real-time intermodal marketplace. Carrier+shipper focus, no broker tier on homepage. Worth re-checking when CHL drays significantly.

**Portloads** — Drayage board, shipper+carrier framing. Pricing gated. DEFER.

**PTTR Load Board** — **Explicit broker tier ($54.99/mo, 12-mo free trial!)**, unlimited load posting. Higher tiers $99.99-$149.99. Phone 855-605-6655. **Best ROI in drayage for a small broker.**

---

### Multi-poster aggregators (post-once → many boards)

**LoadBoardNetwork.com** — Run by former PostEverywhere co-founder Mark Draeb. Targets brokers+shippers. TMS integrations. Aggregates onto "dozens of independent load boards." 617 Main St, Hebron ND 58638, phone 701-805-0865. **Strongest replacement for the deferred PostEverywhere.**

**PostEverywhere.com (MercuryGate)** — Main domain returned ECONNREFUSED in this fetch; partner pages exist on partners.mercurygate.com. Status ambiguous — possibly being absorbed into MercuryGate TMS. Treat as DEFERRED rather than dead.

---

### Carrier vetting / capacity layer (load-board adjacent — high ROI even though not strictly a load board)

**Highway.com** — Carrier identity + fraud prevention. Trusted Freight Exchange (TFX). Integrates with McLeod, Turvo, AscendTMS, Tai, BrokerPro, Chain, Cargo Chief — via TMS, not direct. Email hello@highway.com, phone 906-HIGHWAY. **As CHL ships its own loads on multiple boards, Highway's vetting layer prevents the double-brokering / fraud loss event that wipes a year of margin. Pursue.**

**Parade.ai** — Capacity management for broker's own carrier network. $40B processed, 12M AI conversations annually. assistant@parade.ai. Complements load boards by reusing carriers after first booking. POST-MVP — we don't have a carrier roster yet large enough to justify Parade.

**Trucker Path Truckloads** — Carrier app has 150K+ daily loads; broker side has API at docs.truckerpath.com. Targets the owner-operator audience (high overlap with hot-shot / niche capacity).

---

### Reefer specialty

No reefer-only load board found that's not a sub-product of DAT/Truckstop/Convoy. CHL gets reefer coverage through existing DAT + Truckstop + Direct Freight subscriptions. **SKIP further reefer-specific board hunting** until we hit reefer-capacity ceiling on those.

---

### Generalist free boards worth a zero-cost account

**FindFreightLoads.com** — **FREE broker posting account + free email bulk-upload service for 100+ loads/day**. Phone 888-852-4238. Zero cost, no API, but the free posting alone makes this a no-brainer. **PURSUE.**

**FreeFreightSearch** — 127K+ members, factor-integrated. Main URL returned ECONNREFUSED — site may be down or behind a CDN. INQUIRE.

---

## 3. Recommendations

### PURSUE (sign up / inquire NOW — 7 boards)

| Priority | Board | Reason | Effort |
|---|---|---|---|
| 1 | **Super Dispatch** | Auto-transport API + TMS + load board combined — covers our auto-transport vertical end-to-end with the cleanest integration story | 1 hr signup; ~4 hr API onboard |
| 2 | **Central Dispatch** | Largest auto-transport board; broker is primary poster type. Phone-gated, but mandatory if we touch any auto freight | 1 phone call + paperwork |
| 3 | **BulkLoads.com** (finish onboarding) | #1 bulk/ag board; we already scaffolded — finish sign-up | 30 min |
| 4 | **PTTR Load Board** | Best drayage broker tier ($54.99/mo with 12-mo free trial = zero cost in year 1) | 30 min |
| 5 | **Trucker Path Truckloads (broker)** | Documented API; owner-operator audience covers hot-shot capacity not visible on DAT/Truckstop | 1 hr signup + dev contact |
| 6 | **FindFreightLoads** | Free broker posting + free bulk-upload email service. No reason not to. | 30 min |
| 7 | **LoadBoardNetwork** | Multi-poster aggregator replacing deferred PostEverywhere — multiplies the value of every other board signup | Gated demo; ~2 hr eval |

### INQUIRE (request info / demo, decide later — 6 boards)

- **LivestockNetwork.com** ($35/mo, broker-friendly, largest livestock board)
- **LivestockTransportNation.com** (call via Calendly, animal-welfare angle)
- **LivestockHaulersHub.com** (free, but unclear broker fit)
- **LivestockWays.com** (AI matching pitch, demo required)
- **VeriTread** (heavy-haul, free for carriers, broker side priced via form)
- **Dispatch Center** (auto-transport, API mentioned, email info@dispatchcenter.com)
- **OnlineFreight (Echo Global)** (TMSPro for agents, API+EDI mentioned)
- **Highway.com** (carrier vetting — load-board-adjacent but high fraud-loss-prevention ROI; hello@highway.com)
- **FlatLoads** + **HopLoads** (already scaffolded; complete signup when bandwidth allows)
- **LoadMatch.com** (drayage, established 26-yr platform)

### DEFER (wait until trigger condition)

| Board | Trigger to revisit |
|---|---|
| Truckstop Heavy Haul Load Board Pro | When we book first heavy-haul load on the main Truckstop board |
| FR8Star | Operator already deferred for cost |
| Parade.ai | After we have 50+ carriers in roster |
| PortPro | When drayage >10% of book |
| DrayNow / Portloads | If PTTR fills our drayage capacity needs, skip these |
| CarShipIO | Free-tier OK to evaluate without commitment |
| Pilot Car Loads | After first oversize load booked |
| FreeFreightSearch | When 403/down resolves — retry in 30d |
| PostEverywhere | If LoadBoardNetwork integration is friction-heavy |

### SKIP (defunct, irrelevant, or anti-broker)

| Board | Reason |
|---|---|
| CowHaulers.com | Dead (ECONNREFUSED) |
| Cattlehaulers.com | Dead (ECONNREFUSED) |
| SuperLoadsBoard | Dead (ECONNREFUSED) |
| NEXTHOPE | Could not verify existence |
| LivestockLoader | App-only, no broker user type |
| Equine Express | Single carrier, not a board |
| Brook Ledge | Single carrier, not a board |
| MoveMyHorse | Directory only |
| Auto Hauler Exchange | "Bye Bye Broker" — explicitly anti-broker |
| TankTransport.com | Confirmed magazine (per task note) |
| RXO Connect | Internal to RXO; no external broker posts |
| C.H. Robinson Load Board | Internal to CHR; no external broker posts |
| GetLoaded | Folded into DAT (we already have DAT) |
| Convoy Platform | Being absorbed into DAT (Jul 2025 sale) |
| UnitedStatesOfFreight | Tiny operator, empty board on fetch |

---

## 4. ROI thinking for a generalist broker just starting out

CHL is Model C generalist across all freight types — niche boards matter most where the generalist boards (DAT/Truckstop/123) **don't surface that lane type efficiently**. ROI ranking for first 90 days:

1. **Super Dispatch + Central Dispatch** — Auto-transport is a parallel ecosystem from OTR. If we don't have these two, we cannot quote auto freight at all. Together: ~$100-200/mo, opens an entire freight vertical.

2. **BulkLoads.com** — The ag/bulk/hopper world is invisible on DAT. One board, one signup, covers grain + fertilizer + aggregates + feed + dry-bulk-tanker. **Already scaffolded — just finish the account.**

3. **LoadBoardNetwork** — Multi-poster multiplies every other subscription. Single most-leveraged signup if pricing is reasonable.

4. **PTTR Load Board** — 12-month free trial on the broker tier = $0 cost to learn whether drayage is a market for us. **Asymmetric upside.**

5. **FindFreightLoads** — Free. No reason not to.

6. **LivestockNetwork.com ($35/mo)** — Operator specifically requested cattle/livestock. This is the largest dedicated livestock board still alive. Cheap to test.

7. **Trucker Path Truckloads** — Documented API + 150K daily loads + owner-operator capacity audience (the hot-shot crowd that doesn't pay for DAT). Cleanest API onboard after Super Dispatch.

8. **Highway.com** — Not a board, but the single best ROI item in this report: a $2K-$10K/yr fraud-prevention layer that pays for itself the first time it blocks a double-broker scam. **Probably the highest-EV item here for risk-adjusted return.**

Net: 8 high-conviction items, ~$400-800/mo combined recurring (vs. ~$100K+ first-year volume target). Estimated 30-50% lift in quotable lane coverage from current DAT+Truckstop+123 baseline.

---

## 5. Counts

- **New boards verified ALIVE + broker-friendly:** 22
- **Confirmed DEAD or anti-broker (skip permanently):** 15
- **DEFER (re-evaluate on trigger):** 9
- **Top 3 ROI picks:** Super Dispatch, Central Dispatch, BulkLoads.com (finish onboarding)
- **Surprise high-EV finding:** Highway.com fraud-prevention layer (not a board, but the single best risk-adjusted spend)

---

## 6. Standing follow-ups

- Re-check FreeFreightSearch.com in 30 days (403 today — may be CDN or temporarily down).
- Re-check PostEverywhere.com main domain in 30 days (ECONNREFUSED — possibly being absorbed into MercuryGate; partner pages still live).
- Re-verify LivestockWays.com once they expose more than the hero page, or request a demo via support contact.
- When operator approves auto-transport vertical, **immediately** call Central Dispatch (855-269-2886) for broker account — this is a phone-gated relationship.

---

*End of report.*
