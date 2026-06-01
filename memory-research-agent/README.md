# 🧠 Memory Research Agent

An AI research assistant that **remembers** across conversations. Built with LangGraph, Chroma, and Phoenix tracing.

Unlike standard chatbots that lose context between sessions, this agent maintains **7 types of persistent memory** — from conversation history to semantic knowledge bases, learned tool-call patterns, and extracted entities — so it gets more useful over time.

---

## Demo


https://github.com/user-attachments/assets/f5031119-eb29-4cd8-bd6b-7b0e23697d48


The demo walks through: searching arXiv for papers, ingesting them into the knowledge base, querying across threads, and watching the context window auto-summarise when it fills up.

---

## Features

- **7 persistent memory types** — conversational, knowledge base, workflow, toolbox, entity, summary, tool log
- **Context window management** — auto-summarises when usage exceeds threshold; JIT expansion on demand
- **Semantic tool selection** — only the most relevant tools from the registry are passed to the LLM each turn
- **arXiv integration** — search papers, download PDFs, chunk and store in the knowledge base
- **Web search** — Tavily integration with automatic knowledge base persistence
- **Multi-thread support** — each thread has its own conversation but shares the global knowledge base
- **LangGraph agent** — proper state-machine architecture (build_context → reason → persist)
- **Phoenix tracing** — full observability of LLM calls, tool executions, and latency

---

## Architecture

```
┌──────────────────────────────────────────────────────────────────────────┐
│                         Streamlit UI                                      │
│  Sidebar (threads, context gauge) · Chat · Summaries · Tool Logs · Memory│
└──────────────────────────────────────┬───────────────────────────────────┘
                                       │
   ┌───────────────────────────────────▼───────────────────────────────────┐
   │                    LangGraph Agent (ReAct)                             │
   │                                                                        │
   │  BUILD CONTEXT ──▶ TOKEN BUDGET ──▶ SEMANTIC TOOL SELECTION ──▶ LLM   │
   │  (5 memory reads)   (summarise       (top-5 from registry)    REASON  │
   │                      if > threshold)                            + TOOL │
   │                                                                  CALLS│
   │  ──────────────────────────────────────────────────────────────────── │
   │  PERSIST: conversation + workflow + entity + tool_log                  │
   └───────────────────────────┬───────────────────────────────────────────┘
                               │
       ┌───────────────────────┼──────────────────────┐
       │                       │                      │
┌──────▼──────┐         ┌──────▼──────┐       ┌──────▼──────┐
│  Chroma DB  │         │   SQLite    │       │   Phoenix   │
│ • knowledge │         │ • convo     │       │ • traces    │
│ • workflow  │         │ • tool_log  │       │ • latency   │
│ • entity   │         └─────────────┘       └─────────────┘
│ • summary  │
│ • toolbox  │
└─────────────┘
```

> Architecture diagram: [`docs/architecture.mmd`](docs/architecture.mmd)

---

## Memory Types

| Memory | Storage | Purpose |
|--------|---------|---------|
| **Conversational** | SQLite | Chat history per thread |
| **Knowledge Base** | Chroma | Documents, papers, web search results |
| **Workflow** | Chroma | Learned tool-call sequences |
| **Toolbox** | Chroma | Registered tools with semantic retrieval |
| **Entity** | Chroma | People, organisations, systems mentioned |
| **Summary** | Chroma | Compressed older context (JIT-expandable) |
| **Tool Log** | SQLite | Full tool execution audit trail |

---

## Key Design Decisions

- **Semantic tool retrieval** — tools are embedded in Chroma; only the top-5 most relevant are injected per turn, enabling the registry to scale without context bloat.
- **Automatic context management** — token budget checked every turn. Older messages compressed into structured summaries (technical info, entities, action items) stored for JIT retrieval.
- **Portable storage** — Chroma + SQLite require zero infrastructure. No Postgres, no Redis, no external services.

---

## Tech Stack

| Component | Technology |
|-----------|-----------|
| LLM | OpenAI GPT-4o-mini |
| Agent Framework | LangGraph (state-machine ReAct) |
| Vector Store | Chroma (persistent, local) |
| Relational Store | SQLite |
| Embeddings | sentence-transformers (paraphrase-mpnet-base-v2) |
| arXiv Access | `arxiv` Python library |
| Web Search | Tavily |
| PDF Extraction | PyMuPDF |
| Frontend | Streamlit |
| Observability | Phoenix (OpenTelemetry) |
| Deployment | Docker Compose |
| Language | Python 3.11+ |

---

## Source Code Access

This repository shows architecture and documentation only. For full source code access, [contact me on LinkedIn](https://www.linkedin.com/in/davidalmagro).
