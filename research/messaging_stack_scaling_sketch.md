# Messaging Stack Scaling Sketch (cross-strategic-Phase 3/8/9)

> **Source:** PM Claude (`@pm-lead`) drafted 2026-05-07 per @dev-engineer task brief.
> **Status:** Preliminary sketch (not a binding agenda). Operator + dev refine into actual iter agendas later.
> **Filename history:** Originally `phase_4_scaling_architecture_sketch.md`; renamed 2026-05-07 to `messaging_stack_scaling_sketch.md` to avoid terminology collision with `CHL_STRATEGIC_PLAYBOOK.md` Phase 4 ("Tracking & Exception Handling"). This sketch's concerns cross strategic playbook Phase 3 (carrier network), Phase 8 (scale & optimization), and Phase 9 (multi-broker SaaS). Use the playbook's strategic phase numbers when scoping iters; treat this doc as a cross-cutting planning artifact for SMS infrastructure decisions.

---

## Goal

Plan how CHL's SMS / messaging stack scales from v1 pre-revenue (~50–200 msg/mo) through v3 multi-broker SaaS (5,000–15,000 msg/mo, possibly higher). Identify trigger points where architecture decisions become load-bearing and what the next-step iter agenda should look like at each trigger.

This is forward-looking planning, not an immediate iter. The 4-stage iter 141.3 Plivo migration is the foundational stack; this sketch is the road from there.

---

## 1. Volume Forecasting

**Calibration anchor** (from `plivo_white_glove_packet_FINAL.md` §5):
- Per-recipient frequency: 5–20 msgs/month, event-driven (carriers receive on assigned-load events only).
- Per-load SMS count: ~3–5 (offer + confirm + status check-in + document request + payment). Use 4 as the anchor.

**Derived volume tiers:**

| Phase | Daily peak | Monthly volume | Loads/month | Active carriers | Probable timeline |
|---|---|---|---|---|---|
| v1 — Pre-revenue testing | <50 msg/day | 50–200 | 12–50 | 5–15 | Now → ~6 weeks |
| v2 — Single-broker live ops | ~50–200 msg/day | 1,500–6,000 | 375–1,500 | 150–600 | Months 2–9 |
| v2.5 — Single-broker scaled | ~500 msg/day | ~15,000 | ~3,750 | ~1,500 | Months 9–18 |
| v3 — Multi-broker SaaS (entry) | ~1,000–2,000 msg/day | 30,000–60,000 | ~7,500–15,000 | ~3,000–6,000 | Year 2+ |
| v3+ — Multi-broker SaaS (mature) | 5,000+ msg/day | 150,000+ | 37,500+ | 15,000+ | Year 3+ |

**Trigger thresholds for architecture revisits:**
- **200 msg/day** (≈v2 entry): brand-vetting decision becomes worth running. See §2.
- **500 msg/day** (≈v2.5): suppression-list architecture must move beyond single-flag model. See §5.
- **2,000 msg/day** (≈v3 entry): multi-broker SaaS architecture decisions become load-bearing. See §3.
- **Single-broker beyond 5,000 msg/day:** indicates either (a) CHL shifted to a high-velocity load-board model that doesn't fit the operator-mediated thesis, OR (b) a single broker scaled past most freight brokerages — investigate before scaling SMS infrastructure further.

---

## 2. 10DLC Throughput Limits

**Plivo standard (non-vetted) brand:**
- Sustained throughput: ~2–3 msg/sec across the campaign.
- Burst tolerance: ~10–15 msg in any 5-second window before queuing.
- Adequate for: any volume up to ~200 msg/day with reasonable burst dispersion.

**Plivo vetted brand:**
- Top tier (after $40 vetting): up to 75 msg/sec sustained.
- Adequate for: any volume the operator/dev would realistically build for in v3 (60,000+ msg/mo).

**Short code (US):**
- 100+ msg/sec sustained.
- Approval timeline: 8–12 weeks; cost ~$1,000/mo.
- Adequate for: v3+ (high-velocity multi-broker scale) only. Probably not relevant before Year 3.

**Decision triggers:**
| Trigger | Action |
|---|---|
| Sustained avg msg/sec >0.5 (≈43k msg/mo) | Upgrade to vetted brand |
| Burst >10 msg/sec sustained >5×/day | Upgrade to vetted brand |
| Brand vetting still inadequate at peak | Add 2nd campaign on same vetted brand (different use-case partition) |
| Even 2-campaign vetted brand hits queue-latency >30s | Short-code investigation begins |

**CHL's most likely path:** standard brand serves v1–v2.5. At ~$1k/mo CHL recurring revenue (when vetted brand's $40 + ops overhead is justifiable), upgrade to vetted brand. Short-code remains a Year-3 conversation, not earlier.

---

## 3. Multi-Broker SaaS Architecture

**The decision:** when CHL onboards a 2nd broker, how does the SMS architecture partition?

### Architecture A — Per-broker brand registration

Each broker gets their own TCR brand + campaigns + numbers. CHL operates as the SMS service provider; each broker is a separate compliance entity.

- **Pros:** clean compliance per broker (one broker's STOP rate doesn't affect another); each broker's identity is preserved on TCR; brand vetting is per-broker (so one rejected brand doesn't cascade).
- **Cons:** $4 brand fee × N brokers + $10/mo per campaign × N brokers; 1–2 weeks vetting per new broker (slows onboarding); operational overhead in maintaining N TCR registrations.
- **Onboarding time per broker:** 2–4 weeks.

### Architecture B — Shared brand with brokered campaigns

CHL Logistics LLC is the TCR brand; each broker becomes a campaign under the CHL brand.

- **Pros:** ~10× faster broker onboarding (campaign vetting is 1–3 business days vs brand vetting's 1–2 weeks); economies of scale; shared brand reputation builds across brokers.
- **Cons:** TCR may classify CHL as a service-bureau (CSP-like) and apply stricter vetting; one broker's STOP rate aggregates against the brand's reputation; brand identity dilution as CHL ≠ broker name from recipient view.
- **Onboarding time per broker:** 3–7 days.

### Recommendation

- **Brokers 2–5:** Architecture B (shared brand). Faster onboarding offsets the service-bureau risk at low broker count.
- **Brokers 6+:** Migrate to Architecture A (per-broker brand). Service-bureau classification becomes a real TCR scrutiny risk; per-broker brands give compliance isolation. Migration is one-time pain during the transition.
- **Hybrid option:** keep CHL's primary operational SMS on shared brand even at 6+ brokers; new high-volume brokers go to per-broker brand. Separates the long-tail (small brokers, shared brand) from heavyweights (large brokers, own brand).

**Architecture decision date:** plan to lock at brokers 4 → 5 transition. Don't pre-build Architecture A infrastructure if multi-broker doesn't actually materialize.

---

## 4. Cost Projections

**SMS unit cost** (Plivo 2026 rates from `plivo_migration_research.md`): $0.0050/msg outbound.

| Daily peak | Monthly msg cost | + 10DLC fees (brand+campaign) | Total/mo |
|---|---|---|---|
| 50/day | $7.50 | $14 (year 1: $4 brand + $10/mo) | ~$25 |
| 200/day | $30 | $10 | ~$60 |
| 500/day | $75 | $10–20 (1–2 campaigns) | ~$95 |
| 2,000/day | $300 | $50 (vetted brand $40 first month + 5+ campaigns) | ~$400 |
| 5,000/day | $750 | $80 (multi-campaign) | ~$830 |

**Voice / callback line** (factored at ~5% recipient callback rate, 1-min avg call):
- v1 (50/day SMS): ~$1/mo voice
- v2 (200/day): ~$8/mo
- v3 entry (2,000/day): ~$40/mo
- v3 mature (5,000/day): ~$100/mo

**Per-broker COGS** at multi-broker scale (assume average broker = 100 msg/day):
- $15 SMS/mo + $10 campaign fee + $0.50 voice = ~$25–30/broker/mo SMS COGS.
- Pricing target: charge ~$200–500/broker/mo for the SMS+dispatch layer to maintain healthy gross margin (>80% gross). Validate against broker willingness-to-pay during Phase 9 GTM.

**Cost-of-not-scaling** (i.e., the cost of staying on Twilio + manual ops if CHL doesn't move to Plivo): operator-time cost is the binding constraint, not raw SMS cost. Plivo migration ROI is operational relief + multi-broker readiness, not unit-cost savings.

---

## 5. Operational Complexity at Scale

### 5a. Opt-in / STOP audit logging at scale

**Current model:** `db.carriers.sms_opt_in: bool` (one flag per recipient, in iter 142.1 stage 1d). Works at v1–v2.

**At v3 multi-broker:** same recipient (same carrier MC) may opt-in to broker-A and opt-out of broker-B. Single boolean breaks. Need:
```
db.carrier_consent: {
  carrier_id: str (FMCSA MC),
  broker_id: str (CHL or sub-broker tenant),
  status: "opted_in" | "opted_out" | "pending",
  source: "signed_agreement" | "web_cta" | "keyword",
  opt_in_at: datetime,
  opt_out_at: datetime,
  history: [...]
}
```

Compound index on `(carrier_id, broker_id)` for sub-millisecond consent lookup pre-send.

### 5b. Cross-broker carrier deduplication for STOP events

Question: if carrier MC-XXXXX texts STOP in response to a broker-A message, does that also unsubscribe them from broker-B?

**Strict legal view (FCC TCPA):** each broker's relationship with the carrier is separate. STOP unsubscribes the specific broker's campaign; cross-broker propagation is not required.

**Carrier UX expectation:** carriers may interpret STOP as global ("I don't want SMS from CHL anymore").

**Recommended hybrid:**
- STOP enforces unsubscription on the receiving broker's campaign immediately (legal compliance).
- Cross-broker propagation: optional, configurable per-broker tenant. Default: notify other brokers' tenants of the STOP event so they can choose to also unsubscribe (preserves carrier UX expectation without forcing it).
- Document in CHL's TOS + each broker's onboarding contract.

### 5c. Suppression-list propagation latency

**v1–v2 single-broker:** atomic Mongo update on `db.carriers.sms_opt_in`. Outreach module reads pre-send. Propagation = milliseconds.

**v3 multi-broker:** outreach modules per broker tenant (or per-campaign worker pool). If consent state is cached in-process for performance, cache invalidation must beat the next outbound message.

Solutions:
1. **TTL cache (recommended at v2.5+):** in-process cache with 30s TTL. Worst-case stale: 30s of post-STOP messages slip through. Acceptable for non-malicious flow; supplement with periodic suppression-audit job.
2. **Cache-bust on STOP webhook:** STOP webhook fans out invalidation pings to all per-broker outreach workers. Lower latency but adds infra (Redis pub/sub or equivalent).
3. **No cache (v3+ if compliance-strict):** every outbound query hits Mongo. Adds ~1–5ms per send. Acceptable at <5,000 msg/day, marginal beyond.

**Trigger to upgrade:** at ~500 msg/day OR when first STOP-leak post-opt-out is detected, move from no-cache to TTL cache. Don't pre-build cache-bust infrastructure until needed.

---

## 6. Provider-Redundancy Architecture

**Question:** should CHL maintain a 2nd SMS provider for failover, or commit pure-Plivo?

### Pure-Plivo (current direction post-iter-141.3)

- **Pros:** clean ops (single SDK, single billing, one TCR brand reuse), white-glove relationship value, simpler suppression-list semantics.
- **Cons:** single-vendor outage = full SMS path down. Plivo SLA 99.99% ≈ 4 min/mo downtime theoretical.

### Dual provider (Plivo primary + Twilio cold-backup)

- **Pros:** vendor-outage resilience; pricing leverage in renewal negotiations; insurance against Plivo making the same compliance-friction mistake Twilio made.
- **Cons:** 2× TCR brand registrations (or held-in-suspension at Twilio); 2× SDK code paths in `notification_service.py`; ~$5–10/mo Twilio minimum (1 held number + minimal volume); ops overhead syncing suppression lists across providers.

### Recommendation

| Phase | Posture | Rationale |
|---|---|---|
| v1 (now) | Pure-Plivo + Twilio in 30-day rollback window | Iter 141.3 cutover protocol; reverse-port available |
| v2 ($1k/mo recurring) | Pure-Plivo, Twilio account closed | Single-vendor risk acceptable at this scale; Plivo's white-glove relationship is the value-add |
| v2.5 (~$5k/mo recurring) | Warm-cold dual: Plivo primary, Twilio account re-active with 1 number for failover | Cost ~$10/mo for vendor-redundancy insurance |
| v3 multi-broker | Hot-warm dual with traffic split (e.g., 80/20) | Real failover capability; per-broker tenant can fail over independently if one provider has issues with a specific carrier vetting |

---

## 7. Phase Boundary Triggers (when does Phase 3 end and "Phase 4 scaling" begin?)

Phase 3 (CHL single-broker carrier network per `CHL_STRATEGIC_PLAYBOOK.md`) is the current ceiling. The transition to Phase 4 scaling (in this sketch's sense — multi-broker SaaS messaging architecture) requires:

1. **Recurring revenue threshold:** $1,200/2-week milestone hit consistently for 3 months (CHL's stated revenue gate). Establishes that Phase 3 thesis is validated; SaaS scaling has demand-side signal.
2. **Active-carrier count:** ~150 carriers actively assigned in trailing 30 days. Below this, the lever is Phase 3 carrier-network depth, not Phase 4 multi-broker breadth.
3. **LTV per broker tenant projection:** estimated gross margin per broker × 12-month retention > infrastructure cost of multi-broker buildout amortized over 12-month payback. Specific number TBD; depends on operator's pricing model (likely $200–500/broker/mo gross).
4. **Operational readiness:** Phase 7 (throttle / health) FOUNDATION COMPLETE [✅ already met as of iter 140.1]; Phase 3 (carrier network) at SHIPPED + 90 days stable. Don't open Phase 4 architecture work until Phase 3 has 90 days of green operations across all 4 sub-stages.

**Anti-trigger** (reasons to NOT yet open Phase 4): if CHL hits $1,200/2wk via deeper single-broker (e.g., higher-margin loads, faster cycle time) rather than broader carrier network, multi-broker may not be the highest-ROI next move. Re-evaluate the scaling vector at each $1,200/2wk renewal.

---

## Out-of-scope for this sketch

- GPS / tracking integration (`CHL_STRATEGIC_PLAYBOOK.md` Phase 4 — separate concern, iter 142.x post-Phase-3)
- POD / invoicing (Phase 5, iter 143.x)
- Factoring / settlement (Phase 6, iter 144.x)
- Specialized load handling — hazmat, oversize, refrigerated (Phase 9)
- ML model training for margin/reliability prediction (Phase 10)
- Voice IVR architecture beyond callback fallback
- International SMS / multi-country carrier support

---

## Open Questions (operator decision points before any "Phase 4 scaling" iter opens)

1. **Pricing model for broker tenants** — flat $X/broker/mo, per-load fee, hybrid? Drives LTV math.
2. **Brand-vetting timing** — proactive at $1k/mo (before bottlenecks) or reactive at first queue-latency event?
3. **Architecture A vs B switch threshold** — confirm "brokers 5→6" as the transition trigger, or operator override based on broker-mix.
4. **Provider-redundancy investment** — at what monthly recurring revenue does the ~$10/mo dual-provider insurance become justified?
5. **TCR-service-bureau classification** — pre-emptive conversation with Plivo's regulatory team about Architecture B classification BEFORE first 2nd-broker onboarding, or wait-and-see?

---

**Cross-reference:**
- `chl-memory/CHL_STRATEGIC_PLAYBOOK.md` — phase taxonomy + Phase 3 / 4 / 8 / 9 boundaries
- `chl-memory/research/plivo_migration_research.md` — current SMS stack + cost basis
- `chl-memory/research/plivo_white_glove_packet_FINAL.md` §5 — per-recipient frequency calibration
- `chl-memory/agenda/iter_142_1_full.md` stage 1d — current outreach module spec at single-broker scale
- `chl-memory/research/iter_142_1_phase_3_sketch.md` (SUPERSEDED) + `iter_146_x_phase_8_macro_sketch.md` — adjacent sketch artifacts

**Source:** Drafted by @pm-lead 2026-05-07 per @dev-engineer task brief during iter 141.3 stage 1a Plivo-blocker quiet-time. Forward-looking sketch only; no binding commitments.
