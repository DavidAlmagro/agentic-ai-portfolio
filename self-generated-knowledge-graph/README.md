# 🏘️ Self Generated Knowledge Graph: Andalucía Real Estate

An **agentic, human-in-the-loop pipeline** that builds a knowledge graph for real estate portfolio analysis in Andalucía. It combines **structured government data** (INE statistics, curated CSVs) with **unstructured Wikipedia articles**, all orchestrated through a Streamlit UI where the user guides each step.

---

## Demo

https://github.com/user-attachments/assets/013538d5-070f-41e6-b8dc-21b35ec1e51a

The demo walks through the full 6-step pipeline: defining the KG goal, selecting data files, designing structured and unstructured schemas, and constructing both the domain graph and the lexical graph in Neo4j — all with human approval at each stage.

---

## Features

- **6-step agentic pipeline** — intent → file selection → schema design → unstructured schema → domain graph → lexical graph
- **Proposal/critic agent loop** — dedicated critic agent validates schema proposals against actual CSV contents
- **Human-in-the-loop** — propose → review → approve cycle at every step; user can edit or override
- **Entity resolution** — Jaro-Winkler distance bridges structured + unstructured graphs via `CORRESPONDS_TO` edges
- **LLMGraphTransformer** — extracts entities and relationships from Wikipedia articles
- **Interactive visualization** — pyvis renders Domain, Lexical, or Combined graph views
- **State persistence** — pipeline progress survives browser refreshes
- **Hybrid UI** — each step offers both chatbot (agent-assisted) and manual control tabs

---

## Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│                     Streamlit Multi-Page UI                       │
│  1. Intent · 2. Files · 3. Structured · 4. Unstructured · 5+6   │
└──────────────────────────────────┬───────────────────────────────┘
                                   │
   ┌───────────────────────────────▼──────────────────────────────┐
   │         Agent Runner (OpenAI Function Calling)                │
   │                                                               │
   │  Step 1,4: Propose → User Review → Approve                   │
   │  Step 3:   Proposal Agent → Critic Agent → iterate until OK   │
   │  Step 6:   LLM extract → Jaro-Winkler entity resolution      │
   └───────────────────────────────┬──────────────────────────────┘
                                   │
       ┌───────────────────────────┼──────────────────────┐
       │                           │                      │
┌──────▼──────────┐    ┌───────────▼─────────┐    ┌──────▼──────┐
│ Data Layer      │    │ Neo4j 5 + APOC      │    │ State Layer │
│ CSVs + .md      │───▶│ Domain + Lexical    │    │ JSON persist│
│ (INE + Wiki)    │    │ graphs              │    └─────────────┘
└─────────────────┘    └─────────────────────┘
```

> Architecture diagram: [`docs/architecture.mmd`](docs/architecture.mmd)

---

## Pipeline Steps

| Step | Agent Pattern | What It Does |
|------|---------------|--------------|
| **1. User Intent** | Conversational (propose → approve) | Define the KG goal through natural language |
| **2. File Selection** | Analytical + manual checkboxes | Choose CSVs and Wikipedia articles to include |
| **3. Structured Schema** | Critic loop (proposal → critic → iterate) | Design node/relationship types and property mappings |
| **4. Unstructured Schema** | NER + Fact extraction | Define entity types and relationship triples to extract |
| **5. Domain Graph** | Deterministic (no LLM) | Execute `LOAD CSV` + `MERGE` Cypher |
| **6. Lexical Graph** | LLMGraphTransformer + entity resolution | Extract entities from Wikipedia, resolve via Jaro-Winkler |

---

## Tech Stack

| Component | Technology |
|-----------|-----------|
| LLM | OpenAI GPT-4o (function calling) |
| Graph Database | Neo4j 5 Community + APOC plugin |
| Entity Extraction | LangChain LLMGraphTransformer |
| Entity Resolution | APOC `apoc.text.jaroWinklerDistance` |
| Frontend | Streamlit (multi-page app) |
| Graph Visualisation | pyvis |
| Data Sources | INE API, Spanish Wikipedia |
| Containerization | Docker Compose |
| Language | Python 3.11+ |

---

## Source Code Access

This repository shows architecture and documentation only. For full source code access, [contact me on LinkedIn](https://www.linkedin.com/in/davidalmagro).
