# Hardware Load Test + Recommendations — 2026-05-10

**Status:** Probed live hardware + ran synthetic load test against running CHL backend. Recommendations ranked by leverage for May 14 launch.

**Operator-requested:** 2026-05-10 — "simulate a test to load the hardware components on my PC we are running from, and tell me your recommendation for added hardware."

---

## Current hardware (probed via Win32_*)

| Component | Spec | Status |
|---|---|---|
| **CPU** | Intel i7-8700K @ 3.7 GHz (6 cores / 12 threads, 12 MB L3) | Adequate; idle most of the time |
| **RAM** | 16 GB (2 × 8 GB G.Skill F4-3200C16-8GTZRX, DDR4) | **CRITICAL — 83.6% utilized at idle** |
| **RAM speed** | Rated 3200 MT/s, running at **2133 MT/s** | **XMP not enabled in BIOS** — losing ~33% bandwidth |
| **DIMM slots used** | 2 of 4 (ChannelA-DIMM2 + ChannelB-DIMM2) | 2 free slots |
| **C: drive** | Samsung 860 EVO SSD 1 TB | 783 GB free, healthy |
| **D: drive** | Seagate Expansion 1 TB HDD | 514 GB free, healthy (backup target) |
| **GPU** | NVIDIA GTX 1070 Ti (8 GB VRAM) | Idle — not used by CHL |
| **Network** | localhost-only test | p95 29 ms on /api/health/system |

### Top RAM consumers (current snapshot)

| Process | RAM | Notes |
|---|---:|---|
| mongod | 1,553 MB | CHL database |
| VS Code (with Claude Code session) | 1,305 MB | Dev IDE |
| chrome (×6 processes) | ~2 GB | Browser tabs |
| node (frontend dev server) | 797 MB | React dev |
| **Windows Memory Compression** | **716 MB** | Compressing inactive pages — RAM-pressure signal |
| python (uvicorn / backend) | 373 MB | CHL backend |
| MsMpEng (Defender) | 345 MB | AV |

---

## Load test results (live backend)

**Test 1 — 200 sequential GET /api/health/system:**
- Completed in 5.04s @ **39.7 req/s**
- **0 errors**
- Latency: p50=23ms / p95=29ms / p99=66ms / avg=24.7ms

**Test 2 — 50 concurrent burst:**
- All 50 succeeded, **0 errors**
- median=729ms, max=850ms
- Burst latency is mediocre (FastAPI/Mongo/Python concurrency limits) but acceptable for launch volume

**Test 3 — RAM under load:**
- Delta -0.32 GB during load (Python GC kicked, memory actually freed)
- mongod stayed flat at 1553 MB
- backend ended at 367 MB (-4 MB)

**Verdict:** Platform handles ~40 req/s sustained without strain. RAM was the bottleneck at idle, not under load.

---

## Critical findings

1. **RAM is at 83% before any meaningful workload.** Windows Memory Compression at 716 MB is the smoking gun. Any sustained spike (e.g., synthetic burst of email parsing + load creation + factor submission) would push into swap → slow disk paging → cascading latency.

2. **XMP is OFF.** The G.Skill DIMMs are rated 3200 MT/s but running at 2133 MT/s. **Free 33% bandwidth gain available with a BIOS toggle.**

3. **64 GB RAM kit already ordered (arriving 2026-05-16).** G.Skill F4-3200C16Q-64GTZR — on the ASUS Z390-E QVL. Right call. Resolves the pressure entirely.

4. **No UPS on this box.** A 5-second power flicker corrupts mongod's journal mid-write. Recovery is possible but you'd be down for hours during launch.

5. **GPU is wasted.** GTX 1070 Ti (8 GB) sits idle. Could host local LLM for parser fallback (cut Claude API spend) but only worth it at high volumes.

6. **CPU has cycles to spare.** Don't upgrade this box's CPU — cloud migration plan (Hetzner/AWS) is ~July-Sept 2026, so any new CPU here gets thrown away soon.

---

## Recommendations (ranked by leverage)

### 🔴 DO THIS WEEK

| Action | Cost | Effort | Why |
|---|---|---|---|
| **Enable XMP in BIOS** | $0 | 5 min | Free 33% memory bandwidth boost. F2/DEL on boot → AI Tweaker → Ai Overclock Tuner → XMP I → Save & Exit (F10). Verify after reboot with `wmic memorychip get speed` showing 3200. |
| **Buy APC Back-UPS Pro 1500** | $180-220 | 15 min install | Single highest-leverage hardware purchase before May 14. 15 min runtime on the desktop + modem during brownouts. Prevents corrupted mongo journals from power flickers. **Higher priority than the RAM upgrade because RAM is already ordered.** |

### 🟢 ALREADY IN MOTION

| Action | Cost | Timing |
|---|---|---|
| Install 64 GB G.Skill F4-3200C16Q-64GTZR kit | $170 (paid) | Arriving 2026-05-16; install day-of |

### 🟡 DEFER (post-May-14)

| Action | Cost | Reasoning |
|---|---|---|
| Second 1 TB SSD for mongo data separation | $60-80 | Doubles IOPS headroom; isolates DB from OS thrash. Not blocking. |
| Network upgrade (gigabit unmanaged switch if not already) | $30-50 | Only matters once external integrations are heavy |

### ⚪ DO NOT BUY (waste of money)

| Item | Why not |
|---|---|
| CPU upgrade | Cloud migration in 2-3 months; new CPU = new motherboard + DDR5 RAM = effectively a new build. Pointless. |
| GPU upgrade | Not used by CHL. Only viable if you decide to run local LLM inference for parser fallback, which probably doesn't beat Claude API economics at launch scale. |
| External monitor / peripherals | Off-topic for launch reliability |

---

## BIOS XMP enable — step by step

1. Save any work + reboot
2. During the ASUS splash screen, press **F2** or **DEL** rapidly
3. UEFI BIOS opens. Press **F7** to enter **Advanced Mode** if you're in EZ Mode
4. Navigate: **Ai Tweaker** tab (top menu)
5. First item is **Ai Overclock Tuner** — currently shows `Auto`
6. Change to **XMP I** (or **XMP II** if XMP I doesn't post — try I first)
7. (Optional) **DRAM Frequency** below should auto-set to **3200 MHz**
8. Press **F10** to Save & Exit
9. PC reboots
10. After Windows loads, open PowerShell and run: `wmic memorychip get speed`
11. Should now show **3200** (was 2133)

If the PC fails to POST after XMP enable (very rare on QVL-listed kits), clear CMOS via the motherboard jumper or pull the battery for 30 seconds, then try **XMP II** instead.

---

## What this changes about May 14 readiness

**Before today's audit:** Hardware was a silent risk — RAM at 83% with no UPS.

**After acting on these recommendations:**
- Enable XMP → 33% bandwidth gain, no cost
- Install 64 GB kit on 5/16 → 13.3/64 GB = 21% headroom, plenty of room
- Buy + install UPS → power flicker safety net
- Combined: hardware stops being a launch risk

**Cost to close the gap: ~$200 (UPS only — everything else is free or already paid for).**
