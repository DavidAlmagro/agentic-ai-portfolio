# Agentic AI Portfolio

8 production-grade AI projects demonstrating agentic systems, RAG, memory architectures, multimodal pipelines, voice agents, semantic caching, and knowledge graphs.

Each project below includes a video demo, architecture diagram, feature overview, and tech stack. For full source code access, see [below](#source-code-access).

---

## Projects

| Project | Description | Key Tech | Highlight |
|---------|-------------|----------|-----------|
| [arxiv-mcp-vs-direct](arxiv-mcp-vs-direct/) | Benchmark comparing MCP vs direct LangChain tool-binding for LLM tool access | OpenAI, MCP, LangChain, Streamlit | Empirical data: MCP adds only ~7% latency overhead |
| [memory-agent-email-assistant](memory-agent-email-assistant/) | Email triage agent connected to live Gmail that learns from user corrections via 3 types of persistent memory | LangGraph, Qdrant, LangMem, FastAPI, Gmail IMAP | Self-improving without retraining — procedural rules rewritten by LLM optimizer |
| [memory-research-agent](memory-research-agent/) | Research assistant with 7 types of persistent memory across conversations | LangGraph, Chroma, SQLite, arXiv, Tavily, Phoenix | Auto-summarizes older context + semantic tool selection to scale tool sets |
| [multimodal-data-pipelines-video-and-audio](multimodal-data-pipelines-video-and-audio/) | Multimodal pipeline producing structured property inventory from house tour videos | GPT-4o Vision, Whisper, OpenCV, Pydantic | Fuses audio + visual in a single VLM call; ~$0.05 per video |
| [prod-grade-RAG-system](prod-grade-RAG-system/) | RAG system for electricity bill Q&A with 3 retrieval strategies and multilingual support | LangGraph, ChromaDB, BM25, cross-encoder | Progressive RAG with verification gate rejecting low-confidence answers |
| [self-generated-knowledge-graph](self-generated-knowledge-graph/) | Agentic pipeline building a knowledge graph for real estate analysis from structured + unstructured data | GPT-4o, Neo4j, LLMGraphTransformer, pyvis | Proposal/critic agent loop for schema design + Jaro-Winkler entity resolution |
| [semantic-cache-agent](semantic-cache-agent/) | Agent reducing LLM cost/latency by intercepting semantically similar queries with Redis cache | LangGraph, Redis Stack, RedisVL, FastAPI | Quality gating — only LLM-judge-approved answers enter cache |
| [voice-agent-portfolio-guide](voice-agent-portfolio-guide/) | Live voice agent presenting this portfolio via microphone conversation | Google ADK, Gemini native-audio, google_search | Real-time voice + server-side browsing + domain guardrails + audit callbacks |

---

## Skills Matrix

| Skill | Projects |
|-------|----------|
| **Agentic orchestration** (LangGraph / ADK state machines) | memory-agent-email-assistant · memory-research-agent · prod-grade-RAG-system · semantic-cache-agent · self-generated-knowledge-graph · voice-agent-portfolio-guide |
| **Memory & persistence** (episodic, procedural, semantic) | memory-agent-email-assistant · memory-research-agent |
| **RAG** (hybrid retrieval, re-ranking, verification) | prod-grade-RAG-system · semantic-cache-agent |
| **Knowledge graphs** (Neo4j, entity resolution, schema automation) | self-generated-knowledge-graph |
| **Multimodal** (video + audio fusion, structured output) | multimodal-data-pipelines-video-and-audio |
| **Voice agents** (real-time audio, native-audio models) | voice-agent-portfolio-guide |
| **Semantic caching** (vector similarity, quality gating) | semantic-cache-agent |
| **MCP** (Model Context Protocol benchmarking) | arxiv-mcp-vs-direct · voice-agent-portfolio-guide |
| **Guardrails & observability** (callbacks, tracing, LangSmith, Phoenix) | voice-agent-portfolio-guide · semantic-cache-agent · memory-research-agent |
| **Production practices** (Docker, FastAPI, testing, CI) | All projects |

---

## Getting Started

```bash
git clone https://github.com/DavidAlmagro/agentic-ai-portfolio.git
cd agentic-ai-portfolio/<project-name>
```

Each project folder contains a README with demo, architecture, and feature details. Click any project above to explore.

---

## Source Code Access

This repository contains documentation, architecture diagrams, and demos only. The full source code is maintained in a private repository.

**To request access to the complete codebase**, please reach out:

- LinkedIn: [David Almagro](https://www.linkedin.com/in/davidalmagro)

---

## About

Portfolio by **David Almagro** — focused on building production-grade AI systems, not toy demos. Every project includes proper architecture decisions, observability, testing, and documentation.
