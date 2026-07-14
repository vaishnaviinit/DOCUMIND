# shared/

The single source of truth binding DocuMind's three services. Anything both the backend and AI service must agree on lives here — and changes require sign-off from both developers.

```
shared/
├── api-contracts/   # OpenAPI specs + human-readable endpoint contracts
├── constants/       # Canonical enums shared across services
└── schemas/         # JSON Schemas for cross-service payloads
```

## api-contracts/
Versioned API definitions. The `/ingest` and `/chat` contracts here are the interface between the MERN and GenAI halves — build against these, integrate at the end.

## constants/
Canonical values neither side may redefine locally:
- **Processing statuses** — `uploaded · processing · ready · failed`
- **Ingestion stages** — `extracting · cleaning · chunking · embedding · storing · done · failed`
- **Message roles** — `user · assistant`
- **Error codes** — the machine-readable `code` values in error responses

## schemas/
JSON Schemas describing the exact shape of the `/ingest` and `/chat` request/response bodies, used to validate on both sides and prevent contract drift.
