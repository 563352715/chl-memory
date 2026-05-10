# CHL Hardware Assessment + RAM Upgrade Plan — 2026-05-10

**Operator-mandated 2026-05-10.** Mirror of project-side memory file `~/.claude/projects/c--CHL/memory/project_hardware_assessment_2026_05_10.md` for PM Claude visibility.

## Current hardware (measured)

| Component | Spec |
|---|---|
| **CPU** | Intel i7-8700K (6c/12t, 3.7 GHz) — 3-6% utilized |
| **Motherboard** | ASUS ROG STRIX Z390-E GAMING (Rev 1.xx) — 4 DIMM slots / 2 used / 2 OPEN |
| **RAM installed** | 16 GB total — 2 × 8 GB G.Skill `F4-3200C16-8GTZRX` (Trident Z RGB DDR4) |
| **RAM running speed** | 2133 MHz (modules rated 3200 — XMP not enabled) |
| **RAM max** | 64 GB official, 128 GB with BIOS ≥ 1502 |
| **Disk** | C: 787 GB free / 930 GB; D: 517 GB free / 931 GB |
| **OS** | Windows 10 Home (NOT production-grade) |

## Bottleneck

**Memory is the constraint.** Currently only 2.2 GB free of 16 GB. Chrome eating 3.7 GB, Mongo 1 GB, backend 1 GB. CPU has 20× headroom. Disk has years of headroom.

## Recommended RAM kit

> **G.Skill Trident Z RGB 64GB (4×16GB) DDR4-3200 CL16 — `F4-3200C16Q-64GTZR`**
> 4 sticks, matched kit, ~$170, on ASUS Z390 QVL.

Buy the KIT (not 4 individual sticks). Pull existing 2 × 8 GB before install (don't mix). Re-enable XMP in BIOS after install.

## Capability fit on current vs upgraded box

| Capability | 16 GB | 32 GB | 64 GB |
|---|---|---|---|
| Current platform (rate calc + intel + LLL18) | ✅ | ✅ | ✅ |
| NCS-1 universal load-board observation | ⚠️ marginal | ✅ | ✅ |
| NCS-2/3 driver modes + multi-leg solver | ⚠️ marginal | ✅ | ✅ |
| NCS-6 predictive pricing (ML) | ❌ | ⚠️ tight | ✅ |
| NCS-9 cross-lane optimization (offline) | ✅ | ✅ | ✅ |
| Phase 4 "take the market" 24/7 prod | ❌ | ❌ | ❌ — needs cloud |

## Tier path

- **Tier 0 (free, tonight):** enable XMP in BIOS → 50% memory bandwidth gain
- **Tier 1 (~$170, 30 min):** 4×16GB DDR4-3200 kit → 64 GB → covers everything through NCS-3
- **Tier 2 ($80-700/mo, mo 6-9):** Hetzner AX42 dedicated ($80-110) OR AWS m6i.4xlarge ($500-700)
- **Tier 3 ($3-10K/mo, 12-18 mo):** multi-region cloud, GPU inference, replica MongoDB

## NCS-6 predictive pricing — why ML needs dedicated RAM

- ~5,000 (lane × equipment) combinations at scale
- Each loaded model = 1-4 GB in memory
- Can't load all simultaneously; needs LRU model cache (4-8 GB dedicated)
- On 16 GB box already at 2.2 GB free → no room
- On 64 GB box → comfortable

## Phase 4 "take the market" — why cloud is mandatory

- 1K-5K concurrent shippers + 5K-20K concurrent carriers + 500-2K concurrent driver routing queries
- One i7-8700K can't handle 20K WebSocket sessions (rule: 500-1000 per CPU thread)
- Needs 30-60 cores spread across multi-node deployment
- Single home broadband can't sustain 10+ Gbps with redundancy
- Single-PSU/single-board = no failover
- Geographic latency: CA carriers can't talk to MO server with <100ms

## Operator-confirmed actions

1. Enable XMP in BIOS (free, tonight)
2. Order G.Skill F4-3200C16Q-64GTZR (~$170, Newegg or Amazon-sold-by-Amazon)
3. Replace all 4 DIMM slots with new kit, re-enable XMP, verify 64 GB at 3200 MHz
4. Sell existing 2 × 8 GB on eBay (~$30-40 recovered)
5. Plan Hetzner/AWS review for July-September 2026
