# Project Context: Optical Systems Engineering Assistant
*Claude Project reference document — update this file and re-upload when decisions change*

---

## About the Builder

PhD in Chemical Engineering (Drexel, 2015). 15 years in defense R&D at L3Harris Technologies, Rochester NY. Current role in the Enterprise Innovation, Engineering & Technology Office (CTO office), building AI-powered knowledge discovery and portfolio management tools for L3Harris's $500M+ internal R&D portfolio.

Technical level: Directing AI/LLM development work at L3Harris. Actively building toward proficient vibe coder/architect. Comfortable with high-level architecture decisions, learning Python and LLM application patterns through hands-on building.

Spent 10 years in L3Harris's Imaging Division working directly with optical systems engineers on space telescope programs. Deep domain familiarity with the users and their pain points. This project is a bridge back to that community.

**Hard constraints:**
- Rochester-based, not relocating
- Must remain employed at L3Harris (employer-provided healthcare required)
- Built entirely on personal time, personal hardware, personal accounts — no L3Harris IP or Palantir Foundry environment
- 18-month horizon, deliberate pace — validation over speed

---

## What the Tool Is

An LLM-powered companion for optical systems engineers working on space imaging programs. The target user currently relies on Excel spreadsheets and 30-year-old textbooks for systems-level reasoning. Digital engineering and MBSE approaches are years away from adoption in this community. This tool meets them where they are.

**What it does:**
- Answers systems-level optical engineering questions drawing on a curated reference library
- Points users toward the right specialized knowledge, tools, and references for deeper analysis
- Covers the key domains a systems engineer needs to hold in their head simultaneously
- Knows when it's out of its depth and says so explicitly — pointing toward deeper resources rather than guessing

**Target users:**
- Optical systems engineers at defense contractors and national labs (primary)
- Early-career engineers who lack the institutional knowledge of senior colleagues
- Potentially: university research groups, commercial space companies without deep heritage

**Dual purpose:**
- Internal demo at L3Harris Imaging Division — demonstrates AI tooling value, bridges CTO office with imaging heritage
- Potential external product — small but real addressable market

---

## Non-Negotiable Design Principle

**Honest limitation mechanism.** The tool must know when it is out of its depth. "Here's what I know about radiation hardening for optical systems, but for your specific orbit and mission duration you need to run this through SPENVIS" is more valuable than a confident wrong answer. This principle is not subject to revision.

---

## Coverage Domains

The eight domains a space optical systems engineer needs to reason about simultaneously:

1. `optical_performance` — wavefront error, MTF, diffraction limits, aberrations
2. `structural_thermal` — dimensional stability, thermal gradients, CTE management
3. `space_environment` — radiation, atomic oxygen, thermal cycling, contamination
4. `stray_light` — baffling, scattering, ghost images
5. `detector_and_sensors` — noise sources, QE, dynamic range
6. `image_chain` — from scene to pixel
7. `hyperspectral` — multispectral and hyperspectral considerations
8. `mission_requirements` — link to broader mission context

*Use these exact strings as the `domain` field value in chunk metadata.*

---

## Architecture: Two-Layer System

**Layer 1 — Text RAG:** Retrieves relevant explanatory content from the reference knowledge base. Answers "what is this, when does it apply, what parameters matter."

**Layer 2 — Calculation index:** Maps domain concepts to specific validated calculation tools (SMAD Excel spreadsheets). Surfaces the right tool alongside the text explanation. Answers "here is the actual calculation to run."

When a retrieved chunk has `leads_to_calculation: true`, the UI surfaces the linked Excel file alongside the text response automatically.

---

## Chunk Metadata Schema

Every ingested chunk receives this flat JSON metadata structure:

```json
{
  "chunk_id": "smad_ch17_001",
  "source": "SME-SMAD Section 17.1",
  "domain": "detector_and_sensors",
  "subdomain": "passive_solar_reflectance",
  "content_type": "concept_explanation",
  "complexity": "introductory",
  "keywords": ["multispectral", "hyperspectral", "diffraction", "spatial_resolution"],
  "leads_to_calculation": true,
  "calculation_refs": ["Table_17-5", "Table_17-9"],
  "related_tools": ["ZEMAX", "Code_V"],
  "related_chunks": ["smad_ch17_002"]
}
```

**`domain` values:** use the 8 domain strings above exactly.

**`content_type` values:**
- `concept_explanation` — answers the question in text
- `design_guideline` — "when designing X, consider Y"
- `calculation_setup` — explains inputs/outputs of a calculation
- `worked_example` — shows a calculation applied to a real system
- `reference_data` — constants, tables, standard values
- `tool_pointer` — points to an external tool or standard
- `limitation_flag` — marks where the tool should defer to deeper resources

**Stage 1 minimum fields:** `source`, `domain`, `content_type`, `keywords`, `calculation_refs`. Add other fields only when a real retrieval failure makes them necessary.

---

## Reference Knowledge Base

### Freely available digital content (Stage 1 foundation)
These are clean PDFs, legally unambiguous, already retrieved:

| Source | Content | Priority |
|--------|---------|----------|
| SME-SMAD Section 17.1 | Observation Payload Design | ⭐ High |
| SME-SMAD Section 17.4 | Evolution of Observation Payloads | Low |
| SME-SMAD Section 6.5 | Systems Engineering Tools | Medium |
| SME-SMAD Appendix B | Physical/Orbital Properties | Medium |
| SME-SMAD Appendix E | Time and Date Systems | Low |
| sme-smad.com Excel files | SMAD calculation spreadsheets | ⭐ High (Layer 2) |

GetMORE PDFs and Excel files available at: www.sme-smad.com

### Full reference texts (Stage 2+, pending licensing decisions)

| Title | Author(s) | Publisher | Digital Available? | Purchase Link |
|-------|-----------|-----------|-------------------|---------------|
| Modern Optical Engineering (4th ed.) | Smith | McGraw-Hill | VitalSource ebook | [VitalSource](https://www.vitalsource.com/products/modern-optical-engineering-4th-ed-warren-j-smith-v9780071593755) |
| Modeling the Imaging Chain of Digital Cameras | Fiete | SPIE | PDF via SPIE Digital Library | [SPIE.org](https://spie.org/publications/book/868276) |
| Space Mission Engineering: The New SMAD | Wertz et al. | Microcosm | ❌ No PDF available | [sme-smad.com](http://www.sme-smad.com) |
| Spacecraft Systems Engineering (4th ed.) | Fortescue et al. | Wiley | PDF via Wiley Online | [Wiley](https://onlinelibrary.wiley.com/doi/book/10.1002/9781119971009) |
| Remote Sensing: The Image Chain Approach (2nd ed.) | Schott | Oxford | PDF via Oxford Academic | [Oxford Academic](https://academic.oup.com/book/54786) |
| Introduction to Physics and Techniques of Remote Sensing (3rd ed.) | Elachi & van Zyl | Wiley | PDF via Wiley Online | [Wiley](https://www.wiley.com/en-us/Introduction+to+the+Physics+and+Techniques+of+Remote+Sensing%2C+3rd+Edition-p-9781119523086) |
| Hyperspectral Imaging Remote Sensing | Manolakis et al. | Cambridge | PDF via Cambridge Core | [Cambridge](https://www.cambridge.org/9781107083660) |

**Note on DRM:** Most publisher PDFs carry DRM restrictions. For Stage 1-2 (private, non-commercial internal use), legal risk is low. For Stage 3+ (external users), knowledge base must be rebuilt on properly licensed or open-source content before deployment.

### Standards and open-access sources (always fair game)
- MIL-SPEC optical standards (publicly available)
- ECSS standards (publicly available)
- NASA technical reports (public domain)
- Open-access SPIE proceedings

---

## Tech Stack

| Component | Choice | Notes |
|-----------|--------|-------|
| Language | Python | Learned by doing |
| LLM | Claude API, claude-sonnet | Via Anthropic personal account |
| RAG framework | LlamaIndex or LangChain | TBD after first build |
| Vector store | Chroma or FAISS | Local to start |
| UI | Streamlit | Prototyping only |
| Hosting | Local → simple cloud | When ready to share externally |

All development on personal hardware, personal accounts, personal time.

---

## Build Sequence

**Stage 1 (Months 1–3): Humble Demo**
Simple RAG pipeline over the SMAD GetMORE PDFs. Basic similarity retrieval, Streamlit chat interface. Goal: working demo on a laptop, showable to 3–5 optical engineers for feedback.

**Stage 2 (Months 3–6): Structured Knowledge Layer**
Add structured coverage of all 8 domains. Route queries to the right domain. Implement the honest limitation mechanism explicitly. Begin ingesting additional reference texts (pending licensing).

**Stage 3 (Months 6–12): Real User Feedback**
Deploy to small group of L3Harris Imaging colleagues. Watch how they actually use it. Resist adding features until feedback is clear.

**Stage 4 (Months 12–18): External Viability Assessment**
Based on feedback, assess whether the tool has legs outside L3Harris. Clean up architecture. Consider approaching SPIE or Microcosm for partnership. Consider simple external deployment.

---

## Partnership Strategy

SPIE and Microcosm (SMAD publisher) are both nonprofits with aligned incentives — they want their content used by working engineers. A well-built tool could be offered as a partnership, not a commercial transaction. No financial profit, but significant resume/credibility value.

**Sequencing:** Build a working demo first. Approach potential partners no earlier than end of Stage 2 (month 6). A working demo is a fundamentally different conversation than a proposal.

**Do not approach before Stage 2 is complete.**

---

## Parallel Learning Goals

Building this project is the vehicle for developing:
- Python: data pipelines, file I/O, API calls, async basics — learned by doing
- LLM/RAG patterns: chunking strategy, embedding models, retrieval quality, evaluation, structured output
- Shipped product discipline: one working thing beats ten planned things

Target proficiency: "Can prototype a working RAG pipeline and deploy a simple web app" within 6 months.

---

## What Good Looks Like in 18 Months

A tool that a working optical systems engineer would open before reaching for their textbook. Honest about its limits. Covers the domains that come up in real programs. Citable reference knowledge, not hallucinated answers. Potentially interesting to users outside L3Harris.

---

## Storage and Workflow

**This Claude Project:** Background context, design decisions, reference schemas. Update this file and re-upload when decisions change significantly.

**Git repo (source of truth for everything else):**
- All code
- `decisions/BUILD_LOG.md`
- Source documents and chunked text
- Configuration, schemas, prompts

**Workflow:** Build in repo → paste relevant files into chat when you want input → copy Claude output back into repo. Manual sync, works fine for solo project at this pace.

**Claude cannot access the repo directly** in this interface. Bring files into the conversation by pasting or uploading.

---

*Last updated: 2026-04-25*
