# AGENTS.md instructions for /Users/jaeman/Codes/SoloLingo

<INSTRUCTIONS>
@/Users/jaeman/.codex/RTK.md


<!-- headroom:rtk-instructions -->
# RTK (Rust Token Killer) - Token-Optimized Commands

When running shell commands, **always prefix with `rtk`**. This reduces context
usage by 60-90% with zero behavior change. If rtk has no filter for a command,
it passes through unchanged — so it is always safe to use.

## Key Commands
```bash
# Git (59-80% savings)
rtk git status          rtk git diff            rtk git log

# Files & Search (60-75% savings)
rtk ls <path>           rtk read <file>         rtk grep <pattern>
rtk find <pattern>      rtk diff <file>

# Test (90-99% savings) — shows failures only
rtk pytest tests/       rtk cargo test          rtk test <cmd>

# Build & Lint (80-90% savings) — shows errors only
rtk tsc                 rtk lint                rtk cargo build
rtk prettier --check    rtk mypy                rtk ruff check

# Analysis (70-90% savings)
rtk err <cmd>           rtk log <file>          rtk json <file>
rtk summary <cmd>       rtk deps                rtk env

# GitHub (26-87% savings)
rtk gh pr view <n>      rtk gh run list         rtk gh issue list

# Infrastructure (85% savings)
rtk docker ps           rtk kubectl get         rtk docker logs <c>

# Package managers (70-90% savings)
rtk pip list            rtk pnpm install        rtk npm run <script>
```

## Rules
- In command chains, prefix each segment: `rtk git add . && rtk git commit -m "msg"`
- For debugging, use raw command without rtk prefix
- `rtk proxy <cmd>` runs command without filtering but tracks usage
<!-- /headroom:rtk-instructions -->

--- project-doc ---

# Project: LinguaEdge / PaperLens Core Prototype

Build the first technical prototype of a local-first academic language learning harness.

This is not a PDF chatbot, translator, or summarizer. The first implementation goal is a document-to-learning-object pipeline:

```txt
document input -> ingestion/parsing -> chunking -> mock analysis pipeline -> structured result -> dictionary save
```

The prototype should prioritize:
- clean architecture
- extensible model adapter layer
- structured document analysis pipeline
- stable data models
- good developer experience
- clear UI foundation
- future expansion readiness

The prototype should not prioritize:
- chat UI as the main interface
- fancy animations
- auth, payment, or cloud deployment
- video/audio/mobile features
- vector database or multi-document RAG
- perfect LLM output quality

## Product Thesis

Every academic or technical document can become a personalized language lesson.

Translation solves the current sentence. PaperLens builds the learner's ability to read the next sentence.

## Core User

Initial user:
- non-native English-speaking undergraduate or graduate student
- reads papers, reports, documentation, and research material
- needs both comprehension and durable language acquisition

## First Vertical Slice

Support:
- plain text input
- markdown/text file upload
- basic PDF upload if practical, via parser abstraction and simple `pypdf` extraction

Core flow:
1. User uploads or pastes academic text/report/paper excerpt.
2. System extracts and normalizes text.
3. System chunks the document.
4. System detects domain and document type.
5. System extracts important terms and academic expressions.
6. System decomposes difficult sentences.
7. System generates layered summaries.
8. User saves terms/phrases/sentences into dictionary.
9. App tracks repeated terms and structures.
10. Future quiz/review mode can be added later.

## Required Stack

Frontend:
- Next.js
- TypeScript
- Tailwind CSS
- shadcn/ui or Material UI style primitives
- Material Design 3-inspired principles: card-based information structure, hierarchical typography, clear spacing, restrained color, accessible contrast, predictable navigation, progressive disclosure

Backend:
- FastAPI
- Python 3.11+
- Pydantic
- SQLite
- SQLAlchemy or SQLModel
- Uvicorn

LLM layer:
- provider-neutral model adapter interface
- `MockModelAdapter`
- `OllamaAdapter` or local HTTP scaffold
- future Gemma adapter placeholder
- structured JSON output
- retry on invalid JSON
- fallback to mock output

The prototype must run without a real local model by using `MockModelAdapter`.

## Required Docs First

Before coding the app, create:
1. `design.md`
2. `IMPLEMENTATION_REQUIREMENTS.md`
3. `MODEL_ADAPTER_STRATEGY.md`
4. backend API schema plan
5. frontend component plan

Also include:
- `ROADMAP.md`
- `README.md`

Use these documents as the source of truth. If a feature is not required for the first prototype, put it in `ROADMAP.md` instead of implementing it.

## Target Commit Sequence

Use this commit structure:

1. `docs: add product design and implementation requirements`
2. `backend: add FastAPI skeleton and data schemas`
3. `backend: add mock model adapter and analysis pipeline`
4. `backend: add document and dictionary endpoints`
5. `frontend: add Material-style app shell`
6. `frontend: add document input and analysis result pages`
7. `frontend: add dictionary page`
8. `integration: connect frontend to backend`
9. `docs: add run instructions and roadmap`
10. `model: add Ollama adapter scaffold`

## Backend Architecture

Suggested folders:

```txt
backend/
  app/
    main.py
    core/
      config.py
      errors.py
      logging.py
    models/
      document.py
      analysis.py
      dictionary.py
      user_profile.py
    schemas/
      document_schema.py
      analysis_schema.py
      dictionary_schema.py
    services/
      document_ingestion_service.py
      chunking_service.py
      analysis_pipeline_service.py
      dictionary_service.py
      memory_service.py
    llm/
      base.py
      mock_adapter.py
      ollama_adapter.py
      prompts/
        domain_detection.md
        term_extraction.md
        academic_phrase_extraction.md
        sentence_decomposition.md
        layered_summary.md
    repositories/
      document_repository.py
      analysis_repository.py
      dictionary_repository.py
    api/
      routes_documents.py
      routes_analysis.py
      routes_dictionary.py
      routes_health.py
    db/
      session.py
      init_db.py
```

Implement or scaffold endpoints:

```txt
GET    /health
POST   /documents
GET    /documents
GET    /documents/{document_id}

POST   /documents/{document_id}/analyze
GET    /documents/{document_id}/analysis

POST   /dictionary/items
GET    /dictionary/items
DELETE /dictionary/items/{item_id}
```

Do not put prompts in route handlers. Do not store raw LLM responses only; parse them into structured objects.

## Analysis Output Schema

Analysis result shape:

```json
{
  "document_id": "string",
  "domain": {
    "primary_domain": "string",
    "secondary_domains": ["string"],
    "document_type": "paper | report | article | unknown",
    "confidence": 0.0
  },
  "difficulty": {
    "overall_level": "B1 | B2 | C1 | C2 | domain-heavy | unknown",
    "lexical_difficulty": 0,
    "syntax_difficulty": 0,
    "domain_difficulty": 0,
    "reason": "string"
  },
  "terms": [
    {
      "term": "string",
      "meaning": "string",
      "domain_relevance": "low | medium | high",
      "difficulty": "easy | medium | hard",
      "source_sentence": "string",
      "should_save": true
    }
  ],
  "phrases": [
    {
      "phrase": "string",
      "function": "claim | contrast | limitation | method | result | general",
      "explanation": "string",
      "source_sentence": "string"
    }
  ],
  "sentences": [
    {
      "sentence": "string",
      "core_structure": "string",
      "simplified_version": "string",
      "korean_explanation": "string",
      "difficulty_reason": "string"
    }
  ],
  "summaries": {
    "one_line": "string",
    "simple": "string",
    "academic": "string",
    "study_notes": ["string"]
  }
}
```

## Frontend Architecture

Build only four screens:
1. Dashboard
2. Document Input
3. Analysis Result
4. Dictionary

Suggested folders:

```txt
frontend/
  app/
    page.tsx
    documents/
      page.tsx
    analysis/
      [documentId]/
        page.tsx
    dictionary/
      page.tsx
  components/
    layout/
      AppShell.tsx
      Sidebar.tsx
      TopBar.tsx
    document/
      DocumentInputPanel.tsx
      DocumentUploadCard.tsx
      DocumentPreview.tsx
    analysis/
      DomainOverviewCard.tsx
      TermTable.tsx
      SentenceDecompositionCard.tsx
      LayeredSummaryPanel.tsx
      DifficultyBadge.tsx
    dictionary/
      DictionaryTable.tsx
      SavedTermCard.tsx
    common/
      EmptyState.tsx
      LoadingState.tsx
      ErrorState.tsx
  lib/
    api.ts
    types.ts
```

Dashboard copy:

```txt
Turn difficult documents into personalized language lessons.

Analyze academic papers and reports by domain, vocabulary, sentence structure, and learning priority.
```

## Mock Data

Use this sample paragraph:

```txt
Although previous studies have suggested a correlation between sleep deprivation and reduced cognitive performance, the extent to which these findings generalize across real-world learning environments remains unclear. To address this gap, we analyze longitudinal study logs collected from undergraduate students over a six-week period.
```

Mock output must include:
- domain: education / cognitive science
- terms: sleep deprivation, cognitive performance, generalize, real-world learning environments, longitudinal study
- phrases: previous studies have suggested, the extent to which, remains unclear, to address this gap
- sentence structures:
  - `Although A, B remains unclear.`
  - `To address this gap, we analyze X collected from Y over Z.`

## Quality Bar

Prototype is successful when:
- app runs locally
- UI is clean and serious
- analysis result is structured
- model adapter can be swapped
- mock adapter works without external dependencies
- dictionary saving works
- design documents explain future expansion
- codebase is easy to extend

</INSTRUCTIONS>
