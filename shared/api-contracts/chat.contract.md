# Contract: `/chat`  (v1)

The heart of DocuMind. The backend calls this on every question; the AI service returns a grounded answer with sources.

## Request

```json
{
  "userId": "string (required)",
  "question": "string (required)",
  "documentIds": ["string (required, >= 1)"]
}
```

## Response `200`

```json
{
  "answer": "string",
  "sources": [
    { "documentId": "string", "page": "number", "snippet": "string" }
  ]
}
```

## Grounding guarantees

- `answer` is derived **only** from retrieved chunks of the given `documentIds`.
- If no relevant context is found, `answer` is an honest refusal and `sources` is `[]`.
- Every factual `answer` is accompanied by the `sources` it was built from.

## Errors

| Code | HTTP | Meaning |
|------|------|---------|
| `VALIDATION_ERROR` | 400 | Missing question or documentIds |
| `NO_VECTORS_FOUND` | 404 | No embeddings exist for those documents |
| `GENERATION_ERROR` | 500 | LLM call failed |

> Change this contract only by bumping the version and agreeing across both services.
