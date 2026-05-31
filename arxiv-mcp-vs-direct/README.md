# Arxiv: MCP vs Direct API Benchmark

A Streamlit application that compares two architectural approaches for providing arxiv search capabilities to an LLM chatbot:

1. **MCP Server** — Tools exposed via Model Context Protocol (stdio transport). The LLM discovers tools dynamically through the MCP protocol.
2. **Direct API** — Tools bound directly to the LLM via LangChain's tool-calling mechanism. No intermediary server.

Both approaches use the same LLM (GPT-4o-mini) and identical arxiv search logic, isolating the tool-delivery mechanism as the only variable.

---

## Demo

https://github.com/user-attachments/assets/6af71a82-67bb-4f64-8466-e60ee21ccdf2

---

## Features

- **Single chat input** that runs both MCP and Direct and renders answers side by side
- **Per-query metrics** displayed inline: latency, LLM calls, tool calls, cost
- **Metrics dashboard** with:
  - Aggregate comparison table
  - Latency breakdown charts (LLM vs Tool time)
  - Token usage comparison
  - Cost distribution (box plot)
  - Full query history with CSV export

---

## Architecture

```
app.py                          # Streamlit UI (Chat + Metrics pages)
config.py                       # Shared configuration
├── mcp_approach/
│   ├── research_server.py      # MCP server (arxiv tools, stdio transport)
│   └── client.py               # MCP client + OpenAI reasoning loop
├── direct_approach/
│   ├── arxiv_tools.py          # LangChain @tool-decorated functions
│   └── client.py               # LangChain agent with bound tools
├── shared/
│   └── metrics.py              # SQLite metrics storage + cost calculator
└── data/
    └── metrics.db              # Auto-created on first run
```

---

## Key Results

| Metric | Direct | MCP | Gap |
|--------|--------|-----|-----|
| Avg total latency | 9,109 ms | 9,745 ms | ~7% |
| Avg tool latency | 107 ms | 225 ms | +118 ms |
| Token usage | ~1,024 in / 374 out | ~1,068 in / 382 out | ≈ equal |
| Cost | Equivalent | Equivalent | — |

**Takeaway**: MCP adds modest protocol overhead (~7% end-to-end) while providing dynamic tool discovery. Query-level variance is larger than the architecture effect.

---

## Tech Stack

| Component | Technology |
|-----------|-----------|
| LLM | OpenAI GPT-4o-mini |
| MCP transport | stdio (subprocess) |
| Direct binding | LangChain tool-calling |
| Metrics | SQLite |
| UI | Streamlit |
| Language | Python 3.11+ |

---

## Source Code Access

This repository shows architecture and documentation only. For full source code access, [contact me on LinkedIn](https://www.linkedin.com/in/davidalmagro).
