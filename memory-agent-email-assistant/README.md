# 📧 Memory-Aware Email Assistant

A **production-grade email triage agent** that connects to a live Gmail inbox, classifies emails, drafts replies, and — most importantly — **learns from your corrections in real time** using three types of persistent long-term memory.

Correct a wrong classification once. The agent remembers the specific example (episodic memory), rewrites its own triage rules (procedural memory), and stores contact facts for future context (semantic memory). No retraining, no redeployment — the prompts evolve while the system runs.

---

## Demo

https://github.com/user-attachments/assets/7d627e7c-379b-4a09-acaf-28af29b939f9

---

## Features

- **Live Gmail integration** — connects via IMAP/SMTP to process real emails
- **Three memory types** — episodic (few-shot examples), procedural (mutable rules), semantic (contact facts)
- **Self-improving** — an LLM optimizer rewrites triage rules after each user correction
- **Per-user isolation** — each user has their own memory namespace in Qdrant
- **Two-model architecture** — GPT-4o-mini for fast triage, GPT-4o for quality response drafting
- **Full feedback loop** — correction → episodic storage + prompt optimization → rules updated in Qdrant
- **Streamlit UI** — email monitor + memory inspector showing current rules and stored examples

---

## Architecture

```
┌──────────────────────────────────────────────────────────────────────┐
│                    Streamlit UI  (localhost:8501)                     │
│  User Selector · Email History · Correction Form · Memory Inspector  │
└─────────────────────────────────┬────────────────────────────────────┘
                                  │
              ┌───────────────────▼───────────────┐
              │   FastAPI Backend (:8000)          │
              │   /emails/check · /correct · /mem  │──── Gmail IMAP/SMTP
              └───────────────────┬───────────────┘
                                  │
              ┌───────────────────▼───────────────┐
              │   LangGraph Agent                  │
              │                                    │
              │  triage_router (GPT-4o-mini)       │◄── Episodic examples
              │    ├── ignore → END                │◄── Procedural rules
              │    ├── notify → END                │
              │    └── respond →                   │
              │         response_agent (GPT-4o)    │◄── Semantic memory
              │           └── write_email → Gmail  │
              └───────────────────┬───────────────┘
                                  │
              ┌───────────────────▼───────────────┐
              │   Qdrant Vector Store (:6333)      │
              │   📋 Procedural · 📝 Episodic · 🧩 Semantic │
              └───────────────────────────────────┘
              ┌───────────────────────────────────┐
              │   Prompt Optimizer (LangMem)       │
              │   Triggered on correction →        │
              │   rewrites rules via LLM           │
              └───────────────────────────────────┘
```

> Architecture diagram: [`docs/architecture.mmd`](docs/architecture.mmd)

---

## How Memory Works

| Memory Type | What It Stores | How It's Updated |
|-------------|---------------|-----------------|
| **Semantic** | Contact facts, preferences | Agent calls `manage_memory` autonomously |
| **Episodic** | Labeled email examples (email → correct classification) | Stored when user corrects a classification |
| **Procedural** | Triage rules (ignore/notify/respond criteria) | Rewritten by LLM optimizer after corrections |

### The Feedback Loop

```
User corrects classification
    ├── Store episodic example (specific email + label)
    │     → Similar future emails match via semantic search
    └── Run prompt optimizer (trajectory + reason)
          → LLM rewrites triage rules → persisted to Qdrant
```

---

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Agent framework | LangGraph (state machine with `Command` routing) |
| Vector store | Qdrant (cosine similarity, 1536-dim) |
| LLM — triage | OpenAI GPT-4o-mini (structured output) |
| LLM — response | OpenAI GPT-4o (ReAct agent with tools) |
| Memory toolkit | LangMem (`create_manage_memory_tool`, `create_multi_prompt_optimizer`) |
| API | FastAPI + Uvicorn |
| Email | Gmail IMAP4_SSL + SMTP with STARTTLS |
| Frontend | Streamlit |
| Containerization | Docker Compose (Qdrant + API) |
| Language | Python 3.11+ |

---

## Source Code Access

This repository shows architecture and documentation only. For full source code access, [contact me on LinkedIn](https://www.linkedin.com/in/davidalmagro).
