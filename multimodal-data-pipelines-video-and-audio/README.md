# 🏠 HomeInventory AI — Visual Property Scanner

A **multimodal AI pipeline** that watches a house tour video and produces a structured, machine-readable property inventory — every room identified, every piece of furniture catalogued, with screenshots attached.

Upload a walkthrough video. Get back a JSON file ready to feed into a property management system, a real-estate listing tool, or your own downstream pipeline.

---

## Demo

https://github.com/user-attachments/assets/04084f7e-067c-4ed0-a886-e3b55ef81045

---

## Features

- **Three-stage multimodal pipeline** — frame extraction → audio transcription → VLM analysis
- **Multimodal fusion** — audio narration and visual frames in a single GPT-4o call
- **Pydantic structured output** — guaranteed valid, schema-conformant JSON (no regex post-processing)
- **Interactive Knowledge Graph** — pyvis renders the property as an interactive graph (Property → Room → Furniture → Attribute)
- **Cost-efficient** — ~$0.05 per video analysis
- **Graceful audio degradation** — continues with visual-only analysis if no audio track
- **Uniform temporal sampling** — frames spread evenly across video duration, capped at 20

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                      Streamlit Web UI                            │
│  Video Upload → Cost Preview → Analyze Button                   │
└─────────────────────────────────┬───────────────────────────────┘
                                  │
┌─────────────────────────────────▼───────────────────────────────┐
│                    Multimodal Pipeline                            │
│                                                                  │
│  Stage 1: Frame Extraction                                       │
│  OpenCV · uniform temporal sampling · ≤20 frames · max 1024px    │
│                         │                                        │
│  Stage 2: Audio Transcription                                    │
│  ffmpeg (AAC → MP3) → Whisper-1 · graceful fallback             │
│                         │                                        │
│  Stage 3: VLM Analysis                                           │
│  GPT-4o Vision · frames + transcript → Pydantic response_format  │
└─────────────────────────┬───────────────────────────────────────┘
                          │
┌─────────────────────────▼───────────────────────────────────────┐
│                      Results UI                                   │
│  Summary · Room Inventory · Knowledge Graph (pyvis) · JSON ⬇     │
└──────────────────────────────────────────────────────────────────┘
```

> Architecture diagram: [`docs/architecture.mmd`](docs/architecture.mmd)

---

## Pipeline Steps

| Stage | What It Does |
|-------|--------------|
| **1. Frame Extraction** | OpenCV reads video, selects ≤20 frames uniformly by time, downscales to ≤1024px, encodes as base64 JPEG |
| **2. Audio Transcription** | ffmpeg extracts audio; Whisper-1 transcribes spoken narration |
| **3. VLM Analysis** | GPT-4o Vision receives all frames (with `[Frame at mm:ss]` labels) + transcript, returns validated `HouseAnalysis` Pydantic model |
| **4. Knowledge Graph** | Converts analysis into pyvis interactive graph |

---

## Tech Stack

| Component | Technology |
|-----------|-----------|
| VLM | OpenAI GPT-4o Vision (structured output) |
| Audio transcription | OpenAI Whisper-1 |
| Frame extraction | OpenCV |
| Schema validation | Pydantic v2 |
| Knowledge Graph | pyvis (interactive, embedded in Streamlit) |
| Frontend | Streamlit |
| Audio extraction | ffmpeg |
| Language | Python 3.11+ |

---

## Source Code Access

This repository shows architecture and documentation only. For full source code access, [contact me on LinkedIn](https://www.linkedin.com/in/davidalmagro).
