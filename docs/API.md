# API Reference — DocuMind

This document specifies every endpoint across the two API surfaces:

- **Backend API** (`backend/`) — client-facing, consumed by the frontend. Base path: `/api`.
- **AI Service API** (`ai-service/`) — internal, called only by the backend.

All request/response bodies are JSON unless noted. Authenticated endpoints require a `Authorization: Bearer <JWT>` header.

---

## Conventions

**Standard error envelope**

```json
{
  "success": false,
  "error": { "code": "DOCUMENT_NOT_FOUND", "message": "No document with that id." }
}
```

**Standard success envelope**

```json
{ "success": true, "data": { } }
```

| Status | Meaning |
|--------|---------|
| `200` | OK |
| `201` | Created |
| `202` | Accepted (async processing started) |
| `400` | Validation error |
| `401` | Missing/invalid token |
| `403` | Authenticated but not allowed |
| `404` | Resource not found |
| `409` | Conflict (e.g. email already registered) |
| `422` | Unprocessable (e.g. unsupported file type) |
| `500` | Server error |

---

# Authentication

## `POST /api/auth/signup`

**Purpose** — register a new user and return a JWT.

**Request body**
```json
{ "name": "Ada Lovelace", "email": "ada@example.com", "password": "secret123" }
```

**Response** `201`
```json
{
  "success": true,
  "data": {
    "user": { "id": "u_1", "name": "Ada Lovelace", "email": "ada@example.com" },
    "token": "eyJhbGciOi..."
  }
}
```

**Status codes** — `201` created · `400` validation · `409` email taken.

---

## `POST /api/auth/login`

**Purpose** — authenticate an existing user.

**Request body**
```json
{ "email": "ada@example.com", "password": "secret123" }
```

**Response** `200`
```json
{
  "success": true,
  "data": {
    "user": { "id": "u_1", "name": "Ada Lovelace", "email": "ada@example.com" },
    "token": "eyJhbGciOi..."
  }
}
```

**Status codes** — `200` OK · `400` validation · `401` invalid credentials.

---

# User

## `GET /api/profile`

**Purpose** — return the authenticated user's profile.

**Request body** — none.

**Response** `200`
```json
{
  "success": true,
  "data": { "id": "u_1", "name": "Ada Lovelace", "email": "ada@example.com", "createdAt": "2026-01-01T00:00:00Z" }
}
```

**Status codes** — `200` OK · `401` unauthenticated.

---

# Documents

## `POST /api/documents/upload`

**Purpose** — upload a PDF. Stores the raw file in Cloudinary, creates a `documents` record, and triggers ingestion on the AI service.

**Request** — `multipart/form-data` with field `file` (PDF).

**Response** `201`
```json
{
  "success": true,
  "data": { "documentId": "abc123", "filename": "contract.pdf", "status": "processing" }
}
```

**Status codes** — `201` created · `400` no file · `401` unauthenticated · `422` unsupported type / too large.

---

## `GET /api/documents`

**Purpose** — list the authenticated user's documents (newest first).

**Query params** — `?status=ready` (optional filter), `?page=1&limit=20` (optional pagination).

**Response** `200`
```json
{
  "success": true,
  "data": [
    { "documentId": "abc123", "filename": "contract.pdf", "status": "ready", "pageCount": 12, "createdAt": "2026-07-14T10:00:00Z" }
  ]
}
```

**Status codes** — `200` OK · `401` unauthenticated.

---

## `DELETE /api/documents/:id`

**Purpose** — delete a document: removes the Cloudinary file, the Mongo record, and its vectors from ChromaDB.

**Request body** — none.

**Response** `200`
```json
{ "success": true, "data": { "documentId": "abc123", "deleted": true } }
```

**Status codes** — `200` OK · `401` unauthenticated · `403` not owner · `404` not found.

---

## `GET /api/documents/:id/status`

**Purpose** — poll a document's processing status.

**Response** `200`
```json
{
  "success": true,
  "data": { "documentId": "abc123", "status": "processing", "stage": "embedding", "chunkCount": 128 }
}
```

`status` ∈ `uploaded · processing · ready · failed`.

**Status codes** — `200` OK · `401` unauthenticated · `404` not found.

---

# Chat

## `POST /api/chat`

**Purpose** — ask a question. The backend appends the message, calls the AI service, persists the answer with sources, and returns the grounded answer.

**Request body**
```json
{
  "question": "What was the penalty clause in the vendor contract?",
  "documentIds": ["abc123"],
  "conversationId": "conv_1"
}
```
`conversationId` is optional — omit it to start a new conversation.

**Response** `200`
```json
{
  "success": true,
  "data": {
    "conversationId": "conv_1",
    "answer": "The penalty clause states that late delivery incurs a 2% fee per week, capped at 10% of the contract value.",
    "sources": [
      { "documentId": "abc123", "page": 3, "snippet": "In the event of late delivery, a penalty of 2%..." }
    ]
  }
}
```

If nothing relevant is retrieved, `answer` is an honest refusal and `sources` is empty:
```json
{ "success": true, "data": { "answer": "I couldn't find anything about that in your documents.", "sources": [] } }
```

**Status codes** — `200` OK · `400` validation · `401` unauthenticated · `404` document/conversation not found · `503` AI service unavailable.

---

# Conversations

## `GET /api/conversations`

**Purpose** — list the user's conversations (most recently updated first).

**Response** `200`
```json
{
  "success": true,
  "data": [
    { "conversationId": "conv_1", "title": "Vendor contract questions", "documentIds": ["abc123"], "updatedAt": "2026-07-14T11:00:00Z" }
  ]
}
```

**Status codes** — `200` OK · `401` unauthenticated.

---

## `GET /api/conversations/:id`

**Purpose** — fetch one conversation with its full message thread.

**Response** `200`
```json
{
  "success": true,
  "data": {
    "conversationId": "conv_1",
    "title": "Vendor contract questions",
    "documentIds": ["abc123"],
    "messages": [
      { "role": "user", "content": "What was the penalty clause?", "createdAt": "..." },
      { "role": "assistant", "content": "The penalty clause states...", "sources": [ { "documentId": "abc123", "page": 3, "snippet": "..." } ], "createdAt": "..." }
    ]
  }
}
```

**Status codes** — `200` OK · `401` unauthenticated · `403` not owner · `404` not found.

---

## `DELETE /api/conversations/:id`

**Purpose** — delete a conversation and its messages.

**Response** `200`
```json
{ "success": true, "data": { "conversationId": "conv_1", "deleted": true } }
```

**Status codes** — `200` OK · `401` unauthenticated · `403` not owner · `404` not found.

---

# AI Service (internal)

> These endpoints are called by the backend only. They are not exposed to the browser.

## `POST /ingest`

**Purpose** — extract, clean, chunk, embed, and store a document's vectors.

**Request body**
```json
{ "documentId": "abc123", "fileUrl": "https://res.cloudinary.com/.../contract.pdf" }
```

**Response** `202`
```json
{ "documentId": "abc123", "status": "processing" }
```

The AI service processes asynchronously and reports completion back to the backend (`POST /api/documents/:id/status`) or via a callback.

**Status codes** — `202` accepted · `400` validation · `422` unreadable file · `500` pipeline error.

---

## `POST /chat`

**Purpose** — embed the question, retrieve top-k chunks (filtered by `documentIds`), construct a grounding prompt, and generate a grounded answer.

**Request body**
```json
{ "userId": "u_1", "question": "What was the penalty clause?", "documentIds": ["abc123"] }
```

**Response** `200`
```json
{
  "answer": "The penalty clause states that...",
  "sources": [
    { "documentId": "abc123", "page": 3, "snippet": "In the event of late delivery..." }
  ]
}
```

**Status codes** — `200` OK · `400` validation · `404` no vectors for those documents · `500` generation error.

---

## `GET /health`

**Purpose** — liveness/readiness probe.

**Response** `200`
```json
{ "status": "ok", "vectorStore": "connected", "llmProvider": "openai" }
```

---

> Endpoints marked (auth) require `Authorization: Bearer <JWT>`. Machine-readable OpenAPI specs live in [`shared/api-contracts/`](../shared/api-contracts/).
