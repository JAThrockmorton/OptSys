# Build Log — Optical Systems Engineering Assistant

A running record of key decisions, the options considered, the reasoning behind each choice, and the conditions under which we'd revisit. Maintained in Markdown for compatibility with version control, LLM ingestion, and export to other formats.

---

## How to use this log

Each entry follows this structure:

```
## [YYYY-MM-DD] Decision title
**Decision:** What was decided.
**Options considered:** What alternatives existed.
**Reasoning:** Why this option was chosen.
**Revisit if:** Conditions that would change this decision.
```

Entries are added chronologically. Do not edit past entries — add a new entry if a decision is revised, and reference the original.

---

## [2026-04-25] Project framing: side project, not startup

**Decision:** Build this as a side project on personal time, personal hardware, personal accounts. Keep employed at L3Harris throughout. No external fundraising, no incorporation.

**Options considered:**
- Leave L3Harris to build full time
- Seek external funding / accelerator
- Build entirely within L3Harris on Palantir Foundry

**Reasoning:** Healthcare and income security are non-negotiable constraints. The 18-month horizon allows deliberate validation before any larger commitment. Building outside L3Harris's environment keeps IP clean and avoids proprietary data entanglement.

**Revisit if:** Tool demonstrates clear external market pull after Stage 3 user feedback, and personal financial runway allows a transition.

---

## [2026-04-25] Target user: optical systems engineers at defense/space primes

**Decision:** Primary user is an optical systems engineer at a defense contractor or national lab — specifically someone who currently uses Excel and 30-year-old textbooks for systems-level reasoning.

**Options considered:**
- University research groups
- Commercial space startups
- Broader "engineering" audience

**Reasoning:** This is the community we know best from 10 years in L3Harris's Imaging Division. Deep domain familiarity reduces the risk of building the wrong thing. Defense/space primes are the highest-density cluster of this user type.

**Revisit if:** Stage 3 feedback reveals stronger pull from adjacent communities (commercial space, university labs).

---

## [2026-04-25] Knowledge base strategy: RAG over curated reference texts

**Decision:** Build a RAG pipeline over a curated set of domain reference texts rather than fine-tuning a model or relying solely on Claude's base knowledge.

**Options considered:**
- Fine-tune a model on domain text
- Rely on Claude base knowledge + prompt engineering
- RAG over curated texts (chosen)
- Knowledge graph only

**Reasoning:** Fine-tuning requires significantly more data, compute, and expertise than is available at this stage. Base Claude knowledge lacks the specificity and citability required for engineering credibility. RAG allows explicit citation, honest limitation flagging, and incremental knowledge base improvement.

**Revisit if:** RAG retrieval quality proves insufficient for complex multi-domain queries, or a knowledge graph layer is added that changes retrieval architecture.

---

## [2026-04-25] Tech stack: Python + LlamaIndex/LangChain + Chroma/FAISS + Streamlit

**Decision:** Python as the primary language. LlamaIndex or LangChain for RAG orchestration (TBD after first build). Chroma or FAISS for local vector store. Streamlit for UI prototyping.

**Options considered:**
- JavaScript/TypeScript stack
- Managed RAG platforms (e.g. Vertex AI, Bedrock)
- LlamaIndex vs LangChain (deferred to first build)

**Reasoning:** Python is the dominant language for LLM tooling and data pipelines. Local vector store keeps costs near zero during development. Streamlit allows rapid UI prototyping without frontend expertise. Managed platforms introduce cost and vendor lock-in before product-market fit is established.

**Revisit if:** Deployment requirements change (e.g. cloud hosting for external users), or LlamaIndex/LangChain evaluation reveals a clear winner.

---

## [2026-04-25] LLM: Claude API (claude-sonnet)

**Decision:** Use Anthropic's Claude API, claude-sonnet model, as the primary LLM.

**Options considered:**
- GPT-4o (OpenAI)
- Gemini Pro (Google)
- Open-source models (Llama, Mistral)

**Reasoning:** Domain familiarity from existing Claude usage. Strong reasoning and citation behavior. Honest limitation acknowledgment aligns with the tool's non-negotiable design principle. API access is straightforward on personal accounts.

**Revisit if:** Cost becomes prohibitive at scale, or open-source models reach comparable performance for technical domain tasks.

---

## [2026-04-25] Non-negotiable design principle: honest limitation mechanism

**Decision:** The tool must explicitly flag when it is out of its depth and point toward deeper resources rather than guessing. This is treated as a hard constraint, not a nice-to-have.

**Options considered:** N/A — this is a founding principle, not a trade-off.

**Reasoning:** Engineering credibility requires this. A confident wrong answer in a space telescope context has real consequences. "Here's what I know, but for your specific orbit and mission duration you need SPENVIS" is more valuable than a hallucinated answer. This also differentiates the tool from general-purpose LLM assistants.

**Revisit if:** Never. This is not subject to revision.

---

## [2026-04-25] Primary source material: freely available SMAD GetMORE PDFs as Stage 1 foundation

**Decision:** Begin Stage 1 ingestion with the freely available GetMORE PDFs from sme-smad.com rather than attempting to ingest full textbooks.

**Options considered:**
- Purchase and ingest full textbook PDFs
- Scan physical books
- Use only Claude base knowledge for Stage 1

**Reasoning:** The GetMORE PDFs (especially Section 17.1, 17.4, Appendix B) are clean digital text, legally unambiguous, and directly on-domain. They provide enough material for a demonstrable Stage 1 without navigating DRM or copyright complexity. Full textbook ingestion deferred to Stage 2+ pending legal/licensing decisions.

**Revisit if:** Stage 1 retrieval quality is insufficient, requiring broader coverage.

---

## [2026-04-25] Copyright and licensing strategy: private demo now, licensed sources for external deployment

**Decision:** For Stage 1-2 (private, internal, non-commercial), proceed with purchased PDFs where available and freely distributed content. For Stage 3+ (external users), rebuild knowledge base on properly licensed or open-source content before deployment.

**Options considered:**
- Proceed with all available PDFs regardless of DRM/licensing
- Use only public domain / open-access content from day one
- Pursue immediate licensing agreements

**Reasoning:** Private, non-commercial internal use has low legal risk. The architecture should be designed from the start to swap in licensed content at Stage 3 without rewriting retrieval logic. Pursuing licensing agreements before a working demo exists is premature.

**Revisit if:** Any plans for external deployment, or if a partnership conversation with SPIE or Microcosm begins.

---

## [2026-04-25] Chunk metadata schema: flat, minimal for Stage 1

**Decision:** Use a flat JSON metadata schema per chunk: source, domain, content_type, keywords, leads_to_calculation, calculation_refs. Additional fields deferred until real retrieval failures justify them.

**Options considered:**
- Rich hierarchical schema from the start
- No metadata (pure semantic retrieval)
- Flat minimal schema (chosen)

**Reasoning:** Over-engineering the schema before seeing real query patterns is a common failure mode. Flat metadata is easy to author, easy to query, and easy to extend. The `leads_to_calculation` + `calculation_refs` pair is the key architectural bridge between text RAG and the SMAD Excel calculation tools.

**Revisit if:** Retrieval failures reveal that flat schema cannot distinguish between meaningfully different chunk types.

---

## [2026-04-25] Two-layer architecture: text RAG + calculation index

**Decision:** Design the system as two distinct layers from the start: (1) text RAG for reasoning and navigation, (2) a calculation index that maps domain concepts to specific SMAD Excel tools.

**Options considered:**
- Text RAG only
- Calculation tools only
- Integrated single-layer approach

**Reasoning:** Text RAG answers "what is this and when does it apply." The calculation index answers "here is the tool you actually use." These are different jobs. Conflating them produces worse answers for both. The SMAD Excel files are validated calculations — far more reliable than any LLM-generated arithmetic.

**Revisit if:** User feedback indicates the two-layer distinction is confusing rather than helpful.

---

## [2026-04-25] Partnership strategy: build first, demonstrate second

**Decision:** Pursue potential SPIE and/or Microcosm (SMAD) partnership from a position of demonstrated working tool, not a pitch deck. Target partnership conversation no earlier than end of Stage 2 (month 6).

**Options considered:**
- Approach potential partners now for content licensing
- Build entirely independently, never seek partnership
- Build first, demonstrate second (chosen)

**Reasoning:** A working demo is a fundamentally different conversation than a proposal. Both SPIE and Microcosm are nonprofits with aligned incentives — they want their content used by working engineers. The value to them is a tool that extends reach of their content, not a licensing fee. Approaching before the tool exists invites "interesting but come back when it's real."

**Revisit if:** A contact opportunity arises (e.g. SPIE conference) that makes an earlier conversation natural and low-stakes.

---

## [2026-04-25] Build log format: Markdown in decisions/ folder

**Decision:** Maintain this build log as a single `BUILD_LOG.md` file in a `decisions/` folder within the project repository.

**Options considered:**
- Notion or Confluence wiki
- Google Doc
- Markdown in repo (chosen)
- Structured database

**Reasoning:** Markdown is readable by both humans and LLMs, version-controlled naturally in git, exports cleanly to PDF/Word, and requires no proprietary tooling. A single file is easier to maintain than a fragmented wiki during solo development. Split into phase-specific files only if the log becomes unwieldy.

**Revisit if:** Team grows beyond one person and collaborative editing becomes a bottleneck.

---

## [2026-04-26] SMAD Chapter 17 content audit — proceed with partial content

**Decision:** Proceed with Stage 1 demo using the partial SMAD Chapter 17 content we have access to, while continuing to search for complete non-DRM reference material.

**What we actually have:**
- SMAD 17.1 — 6 of 15 pages (taxonomy and system types, not detailed design)
- SMAD 17.4 — 1 page (minimal value)
- Table 17-5 — Diffraction Limited Resolution (complete Excel calculation)
- Table 17-9 — Parameter Calculations for a Passive Optical Sensor (complete Excel calculation, Section F incomplete)

**The core gap identified:** The calculation spreadsheets (17-5 and 17-9) are orphaned from their explanatory text. The chapters that describe when and how to use these calculations are in the sections we don't have access to (17.2 primarily). We have the answers without the questions.

**Options considered:**
- Wait until complete reference material is found before building
- Proceed with partial content and author bridging metadata
- Abandon SMAD as primary source and pivot immediately to Fiete

**Reasoning:** The taxonomy content in the 6 available pages is genuinely useful for early-career engineer queries. The Excel tables are valuable Layer 2 endpoints regardless of missing explanatory text. Authored bridging metadata (written by us, not copied) can serve as the RAG-retrievable context linking queries to calculations. Waiting for perfect source material before building anything violates the "one working thing beats ten planned things" principle.

**Revisit if:** A complete digital copy of SMAD Chapter 17 becomes available through legitimate licensing or partnership with Microcosm.

---

## [2026-04-26] Authored bridging metadata as legitimate RAG content

**Decision:** Write original authored descriptions for each SMAD calculation table to serve as the RAG-retrievable text that links user queries to the Excel calculation tools. This content is authored by us, not copied from the book.

**Options considered:**
- Leave calculations as Layer 2 endpoints with no explanatory text
- Copy explanatory text from the book (copyright concern)
- Write original bridging descriptions based on analysis of the spreadsheet formulas and structure (chosen)

**Reasoning:** Parsing the actual spreadsheet formulas and structure gives us ground truth about what each calculation does, what its inputs and outputs are, and where its limitations lie. Original authored descriptions based on this analysis are legitimate, do not raise copyright concerns, and can be more precise and honest about limitations than book text would be. This approach also exercises the "honest limitation mechanism" at the metadata level — flagging incomplete sections, instrument-type assumptions, and external tool dependencies explicitly.

**Revisit if:** A licensing arrangement with Microcosm provides access to the actual explanatory text, at which point authored descriptions should be reviewed against the original and updated or replaced.

---

## [2026-04-26] Table 17-5 metadata: single-domain, introductory

**Decision:** Tag Table 17-5 (Diffraction Limited Resolution) as `domain: optical_performance`, `content_type: calculation_setup`, `complexity: introductory`.

**Reasoning:** Table 17-5 implements a single equation (GSD = h × λ / D) across three spectral bands and representative orbital scenarios. It is the first calculation an engineer reaches for when asking "can this aperture resolve my target from this orbit?" Single domain, single formula, appropriate for early-career users. Complexity tagged introductory because the physics is fundamental and the inputs are minimal.

**Revisit if:** User feedback reveals engineers find this calculation insufficient and immediately need to chain to 17-9.

---

## [2026-04-26] Table 17-9 metadata: multi-domain, intermediate, limitations flagged

**Decision:** Tag Table 17-9 (Parameter Calculations for a Passive Optical Sensor) as spanning four domains (`optical_performance`, `detector_and_sensors`, `image_chain`, `mission_requirements`), `content_type: calculation_setup`, `complexity: intermediate`. Explicitly flag three limitations in metadata.

**Reasoning:** Table 17-9 is the most powerful calculation asset we have — it chains orbit geometry through pixel parameters, optics sizing, and full radiometric analysis in a single spreadsheet. Its multi-domain nature is unusual and worth capturing explicitly so the query router knows it can address cross-domain questions. Three limitations flagged in metadata: (1) Section F NEDT is incomplete in Version 1, (2) atmospheric transmissivity requires external input (MODTRAN or published curves), (3) whiskbroom instrument architecture assumed in Section C. These are real gotchas for an early-career engineer and surfacing them proactively is exactly what the honest limitation mechanism is for.

**Revisit if:** Microcosm releases an updated version of Table 17-9 with Section F complete, or if user feedback reveals additional limitations not captured here.

---

## [2026-04-26] Next knowledge base target: Fiete — Modeling the Imaging Chain

**Decision:** Pursue the Fiete book (Modeling the Imaging Chain of Digital Cameras, SPIE Press) as the next knowledge base addition after SMAD Stage 1 demo.

**Options considered:**
- Continue expanding SMAD content
- Pursue Fortescue (Spacecraft Systems Engineering) for broader coverage
- Pursue Fiete for image chain depth (chosen)

**Reasoning:** The image chain is the domain where our current content is thinnest relative to its importance. Table 17-9 covers radiometry at a first-order level but an engineer doing serious detector characterization or image quality analysis needs more depth. Fiete is SPIE-published, which means (a) the publisher is a potential future partner, and (b) PDF access through SPIE Digital Library is more tractable than Wiley or Cambridge titles. Directly on-domain for space imaging applications.

**Revisit if:** SPIE PDF access proves intractable, or user feedback from Stage 1 demo identifies a different domain gap as more urgent.

---

*Last updated: 2026-04-26*
