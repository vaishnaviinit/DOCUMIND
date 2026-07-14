# DocuMind — AI Service

Python + FastAPI RAG intelligence layer. Turns raw documents into grounded, cited answers. Owned by the **GenAI** developer.

## Run

```bash
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
uvicorn app.main:app --reload --port 8000     # http://localhost:8000
```

## Structure

| Folder | Responsibility |
|--------|----------------|
| `app/api/` | FastAPI routers — `/ingest`, `/chat`, `/health`. |
| `app/core/` | Settings, logging, dependency wiring, provider selection. |
| `app/models/` | Internal domain models. |
| `app/schemas/` | Pydantic request/response schemas (match `shared/` contracts). |
| `app/ingestion/` | Extract → clean → chunk orchestration. |
| `app/retrieval/` | Similarity search + context assembly. |
| `app/embeddings/` | Embedding provider abstraction (OpenAI / Gemini). |
| `app/vectorstore/` | ChromaDB adapter (swappable). |
| `app/prompts/` | Prompt templates — grounding, refusal. |
| `app/llm/` | LLM provider abstraction. |
| `app/parsers/` | PDF/text extractors — PyMuPDF, pdfplumber. |
| `app/pipelines/` | High-level ingest & answer pipelines. |
| `app/utils/` | Token counting, text helpers. |
| `tests/` | Unit and pipeline tests. |

## Env

`LLM_PROVIDER`, `OPENAI_API_KEY` / `GEMINI_API_KEY`, `EMBEDDING_MODEL`, `CHROMA_PERSIST_DIR`, `CHUNK_SIZE`, `CHUNK_OVERLAP`, `TOP_K`. See root README for the full list.

## Anti-hallucination

Every answer is grounded in retrieved context. See [../docs/RAG_PIPELINE.md](../docs/RAG_PIPELINE.md) for the three guardrails: context-only prompting, the relevance-threshold refusal path, and source-backed responses.
