# 64GB RAM Upgrade Plan — arriving 2026-05-14

**Trigger:** Operator ordered G.Skill 64GB DDR4-3200 kit; arriving tomorrow.
**Current:** 16GB DDR4 on i7-8700K, ASUS Z390-E motherboard, XMP needs enabling in BIOS.
**Goal:** Maximize what local Ollama can run + drive AI throughput from ~10 req/min to ~30 req/min.

## What 64GB unlocks vs 16GB

| Capability | 16GB (today) | 64GB (tomorrow) |
|---|---|---|
| Concurrent hot models | 1 (with swap) | All 8 + room for 2-3 big new ones |
| Cold-swap latency | 5-15s | ~0 |
| Sustained throughput | ~10 req/min | ~30 req/min |
| Largest single model | 7B-class (~5GB) | 70B-Q3 (~30GB) |
| Headroom for browser/dev tools | tight | comfortable |

## Models to pull AFTER upgrade (in order of priority)

| Model | Size (Q4) | Replaces | Why |
|---|---|---|---|
| **Mixtral 8x7B Instruct** | 26GB | mistral:7b for cold-lane oracle + crosscheck | Sparse MoE 47B, much sharper reasoning |
| **Llama 3.2 Vision 11B** | 7GB | llava:7b in watcher + doc_parse | Sharper multimodal, better OCR on COIs / rate-cons |
| **DeepSeek-Coder-V2 16B** | 10GB | qwen2.5-coder:7b for code-review | Specialized for code; catch my bugs pre-commit |
| **Llama 3.1 70B (Q3_K_M)** | 30GB | llama3.1:8b for high-stakes drafting | Better persuasive writing for shipper-reply-drafter + cold-call-talking-points |
| **Qwen 2.5 32B Instruct** | 18GB | qwen2.5:7b for structured-JSON | Stronger JSON-following, better tier-2 email classifier |

After all 5 pulls: ~91GB of model assets on disk; **at most 2-3 hot simultaneously** (e.g., Mixtral + Llama 70B + Llama 3.2 Vision = 63GB, fits with 1GB OS headroom). The router picks the right one per task.

## Routing-table updates (post-pull)

```
cold_call_talking_points         qwen2.5:7b      -> llama3.1:70b
shipper_reply_draft              llama3.1:8b     -> llama3.1:70b
carrier_vetting_narrative        qwen2.5:7b      -> llama3.1:70b
rate_oracle_cold_lane            mistral:7b      -> mixtral:8x7b
pricing_strategy_reasoning       mistral:7b      -> mixtral:8x7b
classification_crosscheck        mistral:7b      -> mixtral:8x7b
vision_health_check              llava:7b        -> llama3.2-vision:11b
vision_doc_parse                 llava:7b        -> llama3.2-vision:11b
code_review_self                 qwen2.5-coder:7b -> deepseek-coder-v2:16b
load_listing_extract             nuextract       -> nuextract  (specialized; keep)
inbound_email_classify           llama3.1:8b     -> llama3.1:8b  (8B fine for low-stakes)
document_summarize               phi3:mini       -> phi3:mini   (fast-and-cheap fine)
embeddings                       nomic-embed-text -> nomic-embed-text  (no upgrade)
```

Pre-launch: 6 of 14 task-routes upgrade to a bigger/better model. Post-RAM, simply re-run `backend/.seed_llm_routing_2026_05_13.py` with updated mappings.

## Concurrent inference + keep-alive tuning

- Bump `keep_alive` from 30m → 6h for hot-path models so first-call latency stays near zero
- Add multi-model parallel inference helper — when shipper_reply_classifier + shipper_reply_drafter both need to fire in same minute, run concurrently
- Add backend startup model warmup — preload llama3.1:70b + mixtral on boot so first user-facing call doesn't pay cold-start

## Other things 64GB unlocks beyond AI

- **Faster Mongo** — can give Mongo a larger working-set RAM allocation, reducing disk I/O for hot collections
- **Larger uvicorn worker pool** — can run 4 workers instead of 2, doubling concurrent HTTP throughput
- **Headroom for matching algorithms** — load full carrier index into RAM (~30MB), full load index (~50MB), match without disk hits

## Hardware install steps tomorrow

1. Power off + unplug
2. Remove 2 existing DIMMs (saving them as future spares)
3. Insert 4 new 16GB DIMMs in slots A1, A2, B1, B2
4. Power on
5. Enter BIOS (F2 / Delete on boot)
6. **Enable XMP profile** (DDR4-3200 RGB kit needs this; without it sticks run at 2133 default)
7. Save + boot to Windows
8. Verify `(Get-CimInstance Win32_PhysicalMemory | Measure-Object Capacity -Sum).Sum / 1GB` reports 64
9. Tell me — I'll pull the 5 new models + update routing config + restart backend

## When to stop pulling

Disk budget on C: drive matters. Each new model is 7-30GB on disk. After the 5 above, plus the 8 we have today, total Ollama model footprint = ~115GB.

- Keep `models` directory on a drive with **≥ 200GB free** (~85GB headroom).
- If C: drive is tight, move Ollama models to D: drive via `OLLAMA_MODELS` env var.

## What I'm building TONIGHT to be ready

While operator sleeps and RAM is in transit, I'll ship:

1. **model_warmup.py** — runs at backend startup, pre-loads critical models so first call is fast (no-op today on 16GB; activates fully when 64GB lands)
2. **parallel_inference.py** — helper for concurrent multi-model calls
3. **Updated keep_alive** in existing AI callers (30m → 6h)
4. Tonight's routing config stays on 7B-class models; tomorrow flip to bigger ones.

These ship pre-RAM so the moment hardware lands, ONE command makes everything use the new capacity.
