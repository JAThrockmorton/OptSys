# Optical Systems Engineering Assistant

An LLM-powered companion tool for optical systems engineers working on space imaging programs. Built on a curated library of domain reference texts, with honest acknowledgment of its own limitations.

---

## What this is

Space optical systems engineers carry a lot of simultaneous context — wavefront error budgets, thermal stability requirements, detector noise floors, space environment effects, stray light paths. That knowledge lives in 30-year-old textbooks, senior colleagues, and hard-won program experience. This tool is an attempt to make some of that knowledge more accessible, especially for early-career engineers.

The tool answers systems-level questions, points toward the right references and calculations, and — critically — tells you when you need a specialist or a dedicated analysis tool instead. A confident wrong answer in this domain has real consequences. Honest uncertainty is a feature, not a bug.

---

## What it does

- Answers systems-level optical engineering questions grounded in curated reference texts
- Routes queries to the right domain knowledge (optics, thermal, detectors, image chain, space environment, etc.)
- Points to validated calculation tools (SMAD Excel spreadsheets) when a numeric answer is needed
- Flags explicitly when a query exceeds its knowledge and recommends deeper resources

---

## Coverage domains

1. Optical performance — wavefront error, MTF, diffraction limits, aberrations
2. Structural and thermal design — dimensional stability, thermal gradients, CTE management
3. Space environment survivability — radiation, atomic oxygen, thermal cycling, contamination
4. Stray light analysis — baffling, scattering, ghost images
5. Detector selection and characterization — noise sources, QE, dynamic range
6. Image chain analysis — from scene to pixel
7. Hyperspectral and multispectral considerations
8. Mission and system-level requirements

---

## Repository structure

```
optical-engineering-assistant/
├── README.md                  # This file
├── decisions/
│   └── BUILD_LOG.md           # Key decisions and reasoning, maintained throughout build
├── docs/
│   └── PROJECT_CONTEXT.md     # Full project context and architecture reference
├── src/                       # Python source code (Stage 1 build begins here)
└── data/                      # Chunked source documents and metadata
```

---

## Architecture (planned)

**Layer 1 — Text RAG**
A retrieval-augmented generation pipeline over chunked domain reference texts. Retrieves relevant passages and generates grounded, citable responses.

**Layer 2 — Calculation index**
Maps domain concepts to validated SMAD Excel calculation tools. Surfaces the right spreadsheet alongside the text explanation when a numeric answer is needed.

**Tech stack**
- Python
- Claude API (claude-sonnet) via Anthropic
- LlamaIndex or LangChain (TBD)
- Chroma or FAISS vector store
- Streamlit UI

---

## Build stages

| Stage | Timeline | Goal |
|-------|----------|------|
| 1 — Humble Demo | Months 1–3 | Working RAG pipeline over SMAD GetMORE PDFs. Showable on a laptop. |
| 2 — Structured Knowledge | Months 3–6 | Full domain coverage, honest limitation mechanism, broader reference ingestion |
| 3 — Real User Feedback | Months 6–12 | Deploy to small group of optical engineers. Watch. Don't add features yet. |
| 4 — External Viability | Months 12–18 | Assess market outside L3Harris. Potential SPIE/Microcosm partnership conversation. |

---

## Knowledge base

Built on published reference literature. Primary sources:

- *Space Mission Engineering: The New SMAD* — Wertz, Larson & Shunk (eds.)
- *Modern Optical Engineering* (4th ed.) — Smith
- *Modeling the Imaging Chain of Digital Cameras* — Fiete
- *Spacecraft Systems Engineering* — Fortescue, Swinerd & Stark
- *Remote Sensing: The Image Chain Approach* — Schott
- *Introduction to the Physics and Techniques of Remote Sensing* — Elachi & van Zyl
- *Hyperspectral Imaging Remote Sensing* — Manolakis, Lockwood & Cooley
- MIL-SPEC optical standards, ECSS standards, NASA technical reports

No proprietary or export-controlled content. Published literature only.

---

## Status

**Stage 1 — In progress**

- [x] Project scoped and documented
- [x] Build decision log initialized
- [x] SMAD GetMORE PDFs identified and retrieved
- [x] Chunk metadata schema designed
- [x] Two-layer architecture defined
- [ ] Git repo initialized
- [ ] Python environment set up
- [ ] First ingestion pipeline written
- [ ] Basic retrieval loop working
- [ ] Streamlit UI prototype

---

## Design principles

This tool is built for engineers, by someone who has worked alongside them for a decade. Two things that will never be compromised:

**Honest limitations.** The tool will always say when it doesn't know, and point toward the right resource rather than guessing. Engineering decisions have consequences.

**Citable knowledge.** Responses are grounded in specific reference texts, not generated from general training data. Users should always be able to trace an answer back to a source.

---

## Notes

Built as a personal side project on personal time, personal hardware, and personal accounts. No L3Harris proprietary content. No export-controlled material.

---

*Started: April 2026*
