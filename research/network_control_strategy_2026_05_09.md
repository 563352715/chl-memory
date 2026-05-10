# CHL Network-Control Strategy — Take the Whole US Freight Network

**Operator-mandated 2026-05-09 EOD-17.** Captured for ongoing strategic discussion.

## Operator's exact words

> "Our platform need to know when we are losing money overall. I dont ever want to book loads at a loss, but we need to discuss our overall plan to control the entire network of freight loads that move through the country using the methods we will have at our disposal."

## Two hard rules implemented tonight

### Rule 1 — Never book at a loss (per-load)

Implemented in `backend/pnl_monitor.py::compute_load_pnl`. A load loses money if:

```
revenue_after_factor_fee − carrier_rate − visibility_bonus − ops_cost − reservation_buffer < 0
```

Defaults: factor_fee=2.5%, visibility_bonus=$50, ops_cost=$12, reservation_buffer=$8 (all tunable in `db.business_settings`).

Operator's "never" is absolute — NO sovereign override available for losing-money loads. This is stricter than the broker-premium floor (1.08); thin lanes can fail break-even even at 1.08 if carrier rate is high relative to other costs.

### Rule 2 — Aggregate RED state auto-blocks new quotes

Implemented in `backend/pnl_monitor.py::evaluate_window_pnl + is_quote_auto_blocked`. Rolling 24h P&L:
- GREEN ≥ 5% net of revenue → healthy
- YELLOW 1-5% → advisory
- ORANGE -1% to 1% → operator review
- RED < -1% → auto-block new bookings until window clears

**LIVE finding tonight at 23:13 UTC:** 24h window net = **−$4,898.77** (RED) from today's 5% placeholder loads. The monitor caught it. Auto-block fires correctly. The state will clear as those loads age out of the rolling window.

## Network-control roadmap (NCS-1 through NCS-11)

The "methods at our disposal":

| ID | Capability | Status | Effort |
|---|---|---|---|
| NCS-1 | Universal load-board observation engine (see EVERY load posted on EVERY board) | TODO | 3-6 weeks |
| NCS-2 | Driver-mode toggle (home-arrival / max-revenue) on PWA | TODO | 3-4 weeks |
| NCS-3 | Multi-leg constrained solver (load chains routing home or maximizing revenue) | TODO | 1-1.5 months |
| NCS-4 | Mode-aware pricing activation (operator-gated flip from survival-everywhere to per-lane modes) | Foundation built; wiring deferred | 1 week |
| NCS-5 | Real-time market awareness (DAT/Truckstop spot-rate ingestion) | TODO; depends on credentials | 2 weeks/API |
| NCS-6 | Predictive pricing (next-week rate forecasts) | TODO; depends on NCS-1 + NCS-5 | 1 month |
| NCS-7 | Carrier-loyalty economics (tiered fast-pay / preferred-dispatch programs) | TODO | 3-4 weeks |
| NCS-8 | Shipper lock-in economics (volume discounts / contract conversion) | TODO | 3-4 weeks |
| NCS-9 | Cross-lane optimization (deadhead reduction, hub-and-spoke patterns) | TODO; depends on NCS-1 | 1-2 months |
| NCS-10 | Strategic capacity reservation (block carrier-hours for high-margin lanes) | TODO | 3-6 weeks |
| NCS-11 | Lane-entry targeting (find under-served lanes we don't run today) | TODO; depends on NCS-1 | 2 weeks |

**Total estimated build: 6-12 months at sustained pace, gated by load-board credential arrivals.**

## Foundation already shipped (this session, LLL16-LLL18)

- `backend/rate_calc_engine.py` — 11-equipment-band rate calculator with distance × commodity × specials multipliers
- `backend/pricing_modes.py` — 6 mode functions (survival/bid_aggressive/defensive/carrier_attract/volume_grab/outcome_protect)
- `backend/model_calculators.py` — Model A/B/C/D operating-model wrappers (Model C ACTIVE)
- `backend/negotiation_engine.py` — counter-offer logic with concession discipline
- `backend/model_selector.py` — picks model + mode per (lane, shipper) with 5-level precedence
- `backend/lane_intelligence.py` — lane classifier + per-lane premium override
- `backend/margin_config.py` — config-driven premium with floor + override hierarchy
- `backend/vetting_checks.py` — authority-age + phone-FMCSA cross-check (fraud filters)
- `backend/cron_settlement_24h.py` — 24h-post-factor-funding settlement cron
- `backend/load_state_machine.py` — geofence enforcement at 50yd inclusive + POD precondition gate
- `backend/pnl_monitor.py` — per-load break-even guard + aggregate window P&L (TONIGHT)

All BUILT and SMOKE-TESTED. Wiring into `/api/rate-calculator` and `/api/loads` endpoints is deferred — operator confirms when. Until then, the rate calc keeps running at Model C / survival mode (default 1.12 premium, floor 1.08).

## Sequencing

Without the universal load-board observation engine (NCS-1), most other pieces are blind. Suggested order:

1. **NCS-1 first** (data foundation; depends on load-board credentials)
2. **NCS-4 + NCS-2 in parallel** (mode pricing flip + driver-mode toggle)
3. **NCS-5** (DAT/Truckstop spot rates) when keys arrive
4. **NCS-3** (multi-leg solver — the killer routing feature for carriers)
5. **NCS-7 + NCS-8** (loyalty programs)
6. **NCS-9 + NCS-11** (cross-lane optimization + lane targeting)
7. **NCS-6 + NCS-10** (predictive pricing + capacity reservation — most data-dependent, last)

## Cross-references

- `~/.claude/projects/c--CHL/memory/project_business_model_decision_2026_05_09.md` — Model C generalist mandate
- `~/.claude/projects/c--CHL/memory/project_market_dominance_strategy_2026_05_09.md` — phased survive→undercut→dominate
- `~/.claude/projects/c--CHL/memory/project_network_control_strategy_2026_05_09.md` — this doc's project-side mirror
- `chl-memory/progress/NORTH_STAR_BUILD_PLAN.md` — Phase A/B/C build plan (this doc adds NCS-1..11 dimension)
- `C:/CHL/memory/CAPABILITIES.md` section 0 — current rate calc + pricing-modes + model-calculators + negotiation + selector + pnl-monitor inventory
