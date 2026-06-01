# 🎙️ PortfolioGuide Voice Agent

A live conversational voice agent that presents David's Agentic AI portfolio to visitors, built with Google ADK and the Gemini native-audio model. Visitors speak into their microphone and get instant, natural voice responses about 7 portfolio projects.

**Implemented features:** Real-time voice agent (Google ADK + Gemini native-audio) · Server-side web browsing via `google_search` · Domain guardrails (`before_tool_callback` blocks non-professional sources) · Audit logging (`after_tool_callback` tracks all searches in session state) · Custom voice persona (Orus via SpeechConfig) · Embedded portfolio knowledge for zero-latency context access

---

## Demo

https://github.com/user-attachments/assets/3da9e201-fa25-4b10-90ca-dc32a549e1a1

---

## Features

- **Real-time voice interaction** — Gemini native-audio model via `bidiGenerateContent`
- **Server-side web browsing** — `google_search` tool grounds technology explanations with current data
- **Domain guardrails** — `before_tool_callback` blocks Reddit, Quora, YouTube, TikTok
- **Audit logging** — `after_tool_callback` tracks all searches in session state
- **Custom voice persona** — SpeechConfig with prebuilt voice "Orus"
- **Embedded portfolio knowledge** — 7 projects with full context in system instruction for zero-latency access
- **MCP server + Knowledge Graph** (implemented, parked due to ADK bug) — 887 nodes, 1416 edges

---

## Architecture

```mermaid
flowchart TD
    U([Visitor - Microphone]) --> WEB[ADK Web UI<br/>Real-time audio streaming]
    WEB --> BIDI[bidiGenerateContent<br/>Gemini native-audio]

    subgraph AGENT[ADK Agent — portfolio_guide]
        SI[System Instruction<br/>Embedded portfolio context<br/>7 projects · skills · patterns]
        GS[google_search<br/>Server-side tool]
        BC[before_tool_callback<br/>Domain guardrails]
        AC[after_tool_callback<br/>Audit log to session state]
    end

    BIDI --> SI
    SI -->|technology question| BC
    BC -->|allowed| GS
    BC -->|blocked domain| BLOCK[Reject with reason]
    GS --> AC
    AC --> BIDI

    SI -->|portfolio question| BIDI

    BIDI --> VOICE[SpeechConfig - Orus voice]
    VOICE --> U

    subgraph GUARDRAILS[Guardrails]
        direction LR
        G1[Block reddit.com]
        G2[Block quora.com]
        G3[Block youtube.com]
        G4[Block tiktok.com]
    end

    BC -.-> GUARDRAILS

    subgraph FUTURE[Future — pending ADK fix]
        direction LR
        MCP[MCP Server<br/>Knowledge Graph<br/>887 nodes · 1416 edges]
        SUB[Sub-agents<br/>project_explainer<br/>skills_agent]
    end
```

> Architecture diagram: [`docs/architecture.mmd`](docs/architecture.mmd)

---

## Key Design Decisions

| Decision | Rationale |
|----------|-----------|
| **Embedded portfolio context** | ADK 2.1.0 has a confirmed bug — custom function/tool calls in live mode with native-audio models never emit the actual `function_call` event. Embedding context guarantees instant responses. |
| **Server-side `google_search`** | Works in live mode (no client-side round-trip needed). Grounds tech explanations with current web data. |
| **`before_tool_callback` guardrails** | Blocks non-professional domains — only official sources allowed. |
| **`after_tool_callback` audit log** | Every search query logged to `ToolContext.state` — no external infra needed. |
| **MCP server kept in codebase** | Knowledge graph (887 nodes, 1416 edges) ready for when ADK fixes live-mode tool calling. |

---

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Agent framework | Google ADK 2.1.0 |
| Model | Gemini 2.5 Flash (native-audio) |
| Voice | SpeechConfig with prebuilt voice "Orus" |
| Server-side tool | `google_search` (grounding) |
| Knowledge graph (future) | FastMCP server + cached JSON graph |
| Language | Python 3.11+ |

---

## Source Code Access

This repository shows architecture and documentation only. For full source code access, [contact me on LinkedIn](https://www.linkedin.com/in/davidalmagro).
