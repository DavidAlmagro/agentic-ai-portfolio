# 🧠 Semantic Cache Agent

A production-grade LangGraph agent that reduces LLM cost and latency by intercepting semantically similar queries and returning cached answers from Redis — skipping the model entirely.

Unlike naive exact-match caches, this system understands **meaning**: "How do I cancel my subscription?" and "I want to cancel my plan" are recognized as the same intent, and only the first one triggers a full LLM research loop. The second is served from cache in milliseconds.

---

## Demo

https://github.com/user-attachments/assets/a21df9ce-bf23-47b4-a0ed-914404760dfe

---

## Features

- **Semantic similarity matching** — understands paraphrases, not just exact strings
- **Quality gating** — LLM-as-judge scores answers before caching; poor results never enter the cache
- **Query decomposition** — normalizes incoming queries into sub-questions for better cacheability
- **Write-back on approval** — only judge-approved answers get stored with embeddings
- **LangGraph orchestration** — explicit state machine (decompose → check → research → evaluate → write-back → synthesize)
- **Streamlit observability** — health status, cache stats, hit/miss indicators, latency metrics, A/B comparison
- **LangSmith tracing** — full observability of every LLM call and decision

---

## Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                    LangGraph Workflow                          │
│                                                              │
│  ① Decompose → ② Cache Check → ③ Research (on miss)          │
│       │              │                   │                    │
│       │         HIT: return         MISS: LLM research       │
│       │              │                   │                    │
│       │              ▼              ④ Quality Gate             │
│       │         ⑥ Synthesize            │                    │
│       │                            score ≥ 0.7?              │
│       │                           yes │    │ no              │
│       │                               ▼    └── skip cache    │
│       │                        ⑤ Write-back                  │
│       │                          (embed + store)             │
│       │                               │                      │
│       └───────────────────────────────▼──────────────────────│
│                          ⑥ Synthesize                         │
└──────────────────────────────┬───────────────────────────────┘
                               │
                    ┌──────────▼──────────┐
                    │   Redis Stack       │
                    │   HNSW vector index │
                    │   sub-10ms lookups  │
                    └─────────────────────┘
```

> Architecture diagram: [`docs/architecture.mmd`](docs/architecture.mmd)

---

## How the Cache Works

| Stage | What Happens | Why It Matters |
|-------|--------------|----------------|
| **1. Decompose** | Query normalized into sub-questions | Improves cacheability and routing |
| **2. Check cache** | Redis vector search for similar prior answers | Enables paraphrase hits |
| **3. Research on miss** | Agent researches via knowledge base + LLM | Preserves answer quality |
| **4. Evaluate** | LLM-as-judge scores answer before caching | Prevents poor answers from polluting cache |
| **5. Write back** | Approved answers stored with embeddings | Makes future paraphrases cheap |
| **6. Synthesize** | Sub-answers combined into final response | Keeps UX coherent |

---

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Agent orchestration | LangGraph |
| API | FastAPI + Uvicorn |
| UI | Streamlit |
| Semantic cache | Redis Stack + RedisVL |
| Embeddings | sentence-transformers (redis/langcache-embed-v1) |
| Models | OpenAI GPT-4.1 + GPT-4.1-mini |
| Dataset | HuggingFace (Bitext customer support) |
| URL ingest | Tavily |
| Testing | Pytest (22 tests) |
| Observability | LangSmith |
| Containerization | Docker Compose |
| Language | Python 3.11+ |

---

## Source Code Access

This repository shows architecture and documentation only. For full source code access, [contact me on LinkedIn](https://www.linkedin.com/in/davidalmagro).
