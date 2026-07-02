---
title: "Free-tier LLM verification: catalog vs. reality"
date: 2026-07-02
method: "OpenRouter catalog API + headless agent practice tests (hermes)"
status: snapshot
---

# Free-tier LLM verification (July 2026)

**TL;DR:** Of the free coding-capable models listed on OpenRouter, several are listed
but effectively dead (rate-limited into uselessness or hanging). Always practice-test;
the catalog lies by omission.

## Setup

- Catalog: `https://openrouter.ai/api/v1/models` (338 models, 22 with `:free` tier)
- Practice test: one-shot headless agent run ("reply with exactly OK"), 3 retries
- Date: 2026-07-02

## Results

| Model (`:free`) | Catalog | Practice test | Notes |
|---|---|---|---|
| `nvidia/nemotron-3-super-120b-a12b` | ✅ | ✅ works | 12B active MoE, 1M ctx — solid student model |
| `cohere/north-mini-code` | ✅ | ✅ works | 3B active — lowest-power coding option |
| `poolside/laguna-xs.2` | ✅ | ✅ works | efficient coding agent class |
| `poolside/laguna-m.1` | ✅ | ❌ hangs (>2 min, no response) | avoid |
| `qwen/qwen3-coder` | ✅ | ❌ HTTP 429 after 3 retries | free pool exhausted |

## Modality verification (via catalog `architecture.input_modalities`)

| Model | Input modalities | Use as |
|---|---|---|
| `nvidia/nemotron-3-nano-omni-30b-a3b-reasoning:free` | text + image + video + **audio** | perception / VLM teacher |
| `google/gemma-4-26b-a4b-it:free` | text + image + video | VLM student (3.8B active MoE) |
| `google/gemma-4-31b-it:free` | text + image + video | VLM student (dense, stronger) |
| `nvidia/nemotron-nano-12b-v2-vl:free` | text + image + video | document/video specialist |

## Catalog corrections (things "everyone knows" that are wrong)

- **DeepSeek V4 Flash/Pro have no free tier** on OpenRouter (Flash: $0.09/$0.18 per M;
  Pro: $0.435/$0.87 per M). Cheap, but not free.
- **Xiaomi MiMo-V2.5** is paid ($0.105/$0.28 per M) — and the **Pro variant is text-only**,
  despite the base model being omni.
- **"Gemma 4 12B" does not exist.** The free Gemma-4 variants are 26B-A4B (MoE) and 31B (dense).
- **PaliGemma is not on OpenRouter** at all.

## Takeaways

1. **Listed ≠ working.** Free pools of popular models (qwen3-coder) get exhausted;
   test before wiring anything into a pipeline.
2. **MoE active-parameter count is the efficiency lever** — 3-12B-active models
   (north-mini-code, gemma-4-26b-a4b, nemotron-super) are the sweet spot for agent swarms.
3. **Free omni perception exists**: nemotron-3-nano-omni is the only free model taking
   image+video+audio — a viable free VLM teacher for perception tasks.
