# ⚡ Energy Bill Assistant — Production-Grade RAG System

A production-grade RAG system that lets you ask natural-language questions about your electricity bills and get grounded, cited answers. Supports multilingual queries (Spanish/English) with three progressive retrieval strategies.

---

## Demo

https://github.com/user-attachments/assets/558e920c-5eda-455a-bab7-7b24915da731

The demo shows multilingual questions using **Hybrid RAG** (vector + BM25): queries in both Spanish and English answered accurately with cited sources. When a challenging cross-lingual question stumps the hybrid approach, we switch to the **Full Pipeline** — adding cross-encoder re-ranking and a verification gate — and it handles the query correctly.

---

## Features

- **Three retrieval strategies** (switchable from sidebar):
  - **Simple RAG** — semantic vector search
  - **Hybrid RAG** — vector + BM25 keyword search merged with Reciprocal Rank Fusion
  - **Full Pipeline** — hybrid + cross-encoder re-ranking + verification gate
- **Verification gate** — rejects low-confidence results rather than hallucinating
- **Multilingual** — ask in English, Spanish, or any language; cross-lingual retrieval works
- **Aggregate vs specific detection** — automatically handles "which month was cheapest?" differently from "what did I pay in March?"
- **LangGraph orchestration** — explicit, inspectable state machine (classify → retrieve → rerank → verify → generate)
- **Cited answers** — responses reference exact source pages

---

## Architecture

```
User question
     │
     ▼
  Classify (specific / aggregate)
     │
     ▼
  Retrieve (vector search + BM25 keyword search + RRF)
     │
     ▼
  [Full pipeline only] Re-rank (cross-encoder scoring)
     │
     ▼
  [Full pipeline only] Verify (reject low-confidence chunks)
     │
     ▼
  Generate (LLM answer with citations) — or Abstain
```

> Architecture diagram: [`docs/architecture.mmd`](docs/architecture.mmd)

---

## Three Retrieval Strategies

| Strategy | What it does | When to use |
|----------|-------------|-------------|
| **Simple RAG** | Semantic vector search (cosine similarity) | Fast, good for straightforward lookups |
| **Hybrid RAG** | Vector + BM25 keyword search, merged with RRF | Better when queries use exact terms (tariff codes, invoice numbers) |
| **Full Pipeline** | Hybrid + cross-encoder re-ranker + verification gate | Best accuracy, slightly slower |

---

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Orchestration | LangGraph |
| LLM | GPT-4o-mini (temperature=0) |
| Embeddings | OpenAI text-embedding-3-small |
| Vector store | ChromaDB (local, persisted) |
| Keyword search | BM25 (rank-bm25) |
| Re-ranker | cross-encoder/mmarco-mMiniLMv2 (multilingual) |
| PDF extraction | pdfplumber (table-aware) |
| UI | Streamlit |
| Language | Python 3.11+ |

---

## Source Code Access

This repository shows architecture and documentation only. For full source code access, [contact me on LinkedIn](https://www.linkedin.com/in/davidalmagro).
