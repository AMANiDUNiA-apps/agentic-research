# agentic-research

Practical research on **free-tier LLMs for agentic workflows** — what actually works,
what it costs (in tokens, watts, and patience), and how to build agent pipelines on top.

Part of the [mnemo](https://github.com/AMANiDUNiA-apps/mnemo) ecosystem: a multi-layer memory
framework for LLM agents, driven by a teacher-student loop (strong model distills skills,
free models execute).

## Reports

| Report | Topic | Status |
|--------|-------|--------|
| [free-llm-verification](reports/2026-07-free-llm-verification.md) | Which free models on OpenRouter actually respond (catalog vs. reality) | ✅ |
| [llm-efficiency](reports/2026-07-llm-efficiency.md) | Energy per token, tokens-per-watt, environmental footprint | ✅ |
| [bm25-rag](reports/2026-07-bm25-rag.md) | BM25 vs. dense embeddings for RAG retrieval | ✅ |
| [context-cost](reports/2026-07-context-cost.md) | What a Claude Code session pays at startup — measured, before/after a 5-step diet | ✅ |

## Method

1. **Catalog check** — is the model listed (e.g. OpenRouter `/api/v1/models`)?
2. **Practice test** — headless agent run with a minimal prompt; listed ≠ working.
3. **Role mapping** — teacher/student × text/vision, with cost and rate-limit budgets.
4. Findings feed agent model-definitions (Markdown + YAML frontmatter) that profiles compose.

## Why free models?

Agentic workloads burn tokens fast. Our approach: a strong frontier model acts as
**teacher** (review, distillation, architecture), free MoE models act as **students**
(execution). A validator gate catches hallucinated references. This repo documents
what holds up in practice.

## Support this work ⭐

This research is done on free-tier models and a small ARM server, as part of building
[**mnemo**](https://github.com/AMANiDUNiA-apps/mnemo) — an open multi-layer memory framework
for LLM agents. If you find it useful, **star the mnemo repo**: community stars directly
help this project qualify for sponsored Claude access, which funds the teacher side of
the teacher-student loop.

*Maintained by [@amaniagent](https://github.com/amaniagent) / AMANiDUNiA-apps. Findings are point-in-time snapshots — free tiers change weekly.*
