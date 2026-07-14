# Contract: `/ingest`  (v1)

Called by the backend once a document's raw file is stored, to run the ingestion pipeline.

## Request

```json
{
  "documentId": "string (required)",
  "fileUrl": "string (required, public or signed URL)"
}
```

## Response `202`

```json
{ "documentId": "string", "status": "processing" }
```

## Completion callback

The AI service reports completion asynchronously back to the backend:

```
POST /api/documents/:id/status
{ "status": "ready" | "failed", "chunkCount": 128, "error": null }
```

## Errors

| Code | HTTP | Meaning |
|------|------|---------|
| `VALIDATION_ERROR` | 400 | Missing documentId or fileUrl |
| `UNREADABLE_FILE` | 422 | File could not be parsed |
| `PIPELINE_ERROR` | 500 | Extraction/embedding/storage failed |

> Change this contract only by bumping the version and agreeing across both services.
