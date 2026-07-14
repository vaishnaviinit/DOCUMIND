# Development Roadmap — DocuMind

DocuMind is built in six phases. The guiding principle: **ship a thin version end-to-end first, then deepen each layer.** If only Phases 1–5 are completed, the product is still complete and demoable.

Ownership legend: **[FE]** frontend · **[BE]** backend · **[AI]** AI service · **[BOTH]** shared.

---

## Phase 1 — Repository Setup

*Goal: a clean monorepo both developers can work in without collisions.*

- [x] **[BOTH]** Initialize monorepo structure (`frontend/`, `backend/`, `ai-service/`, `shared/`, `docs/`).
- [x] **[BOTH]** Write architecture, API, database, and RAG documentation.
- [x] **[BOTH]** Define the API contract in `shared/api-contracts/` and shared constants.
- [ ] **[BOTH]** Add root `.gitignore`, `LICENSE`, `CONTRIBUTING.md`.
- [ ] **[BOTH]** Set up branch protection and PR templates.
- [ ] **[BOTH]** Add `.env.example` to each service.

---

## Phase 2 — Frontend (stubbed)

*Goal: the full UI works against a fake/stubbed API.*

- [ ] **[FE]** Scaffold Vite + React + Tailwind + React Router.
- [ ] **[FE]** Build `AuthLayout` and `AppLayout`.
- [ ] **[FE]** Signup and Login pages with form validation.
- [ ] **[FE]** `AuthContext` + `useAuth` hook; store JWT; protect routes.
- [ ] **[FE]** Document Library page — list, upload button, live status badges.
- [ ] **[FE]** Upload flow with progress and `processing → ready` states.
- [ ] **[FE]** Chat page — message thread, input, loading state.
- [ ] **[FE]** Citation rendering (clickable source chips).
- [ ] **[FE]** Axios service layer (`authApi`, `docApi`, `chatApi`) against stubbed responses.

---

## Phase 3 — Backend

*Goal: real auth, storage, and orchestration.*

- [ ] **[BE]** Scaffold Express with the layered architecture (routes → controllers → services → repositories).
- [ ] **[BE]** Connect MongoDB Atlas; define Mongoose models (User, Document, Conversation, Message, ProcessingStatus).
- [ ] **[BE]** JWT auth: signup, login, `protect` middleware.
- [ ] **[BE]** Multer + Cloudinary upload; create Document record.
- [ ] **[BE]** Documents CRUD: list, delete, status.
- [ ] **[BE]** Trigger AI `/ingest` after upload; handle status callback.
- [ ] **[BE]** Chat orchestration: persist messages, call AI `/chat`, return answer + sources.
- [ ] **[BE]** Conversations CRUD.
- [ ] **[BE]** Central error handling and request validation.

---

## Phase 4 — AI Service

*Goal: real ingestion and grounded retrieval, verified in isolation.*

- [ ] **[AI]** Scaffold FastAPI with `/ingest`, `/chat`, `/health`.
- [ ] **[AI]** PDF text extraction (PyMuPDF / pdfplumber) with page tracking.
- [ ] **[AI]** Cleaning + structure-aware chunking with overlap.
- [ ] **[AI]** Embedding provider abstraction (OpenAI / Gemini).
- [ ] **[AI]** ChromaDB vector store adapter; upsert with metadata.
- [ ] **[AI]** Similarity search filtered by `documentIds`; verify retrieval by printing pulled chunks.
- [ ] **[AI]** Grounding prompt + refusal guardrail ("I don't know").
- [ ] **[AI]** LLM provider abstraction; generate answer + sources.
- [ ] **[AI]** Pydantic schemas matching the shared contract.

---

## Phase 5 — Integration

*Goal: the two halves meet; the product works end-to-end.*

- [ ] **[BOTH]** Swap frontend stubs for the real backend.
- [ ] **[BOTH]** Wire backend to the live AI service; confirm ingest → ready → chat flow.
- [ ] **[BOTH]** End-to-end test: upload a PDF, wait for ready, ask a question, verify a correct, cited answer.
- [ ] **[FE]** Render real citations; link to source page.
- [ ] **[BOTH]** Conversation history across sessions.
- [ ] **[BOTH]** Error handling: bad file, empty answer, AI service down, processing failure.
- [ ] **[BOTH]** Multi-document chat.

---

## Phase 6 — Deployment

*Goal: publicly reachable, reproducible deployments.*

- [ ] **[FE]** Deploy frontend to Vercel.
- [ ] **[BE]** Deploy backend to Render; configure env + CORS.
- [ ] **[AI]** Deploy AI service to Render; persist ChromaDB.
- [ ] **[BOTH]** Wire production URLs across services.
- [ ] **[BOTH]** `docker-compose.yml` for local multi-service dev *(future)*.
- [ ] **[BOTH]** GitHub Actions CI: lint, test, build on PR *(future)*.

---

## Stretch goals

- [ ] Streaming answers (token-by-token via WebSocket / SSE).
- [ ] Follow-up questions with conversation memory.
- [ ] Multi-format uploads (Word, HTML, plain text).
- [ ] Library-wide search across all documents.
- [ ] Migrate ChromaDB → Pinecone/Qdrant for scale.
