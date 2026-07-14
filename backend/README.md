# DocuMind — Backend

Node.js + Express REST API. The application shell: auth, uploads, storage, and orchestration. Owned by the **Full Stack (MERN)** developer.

## Run

```bash
npm install
npm run dev        # http://localhost:5000
```

## Layered architecture

```
Route → Middleware → Controller → Service → Repository → Model (Mongoose)
```

| Folder | Responsibility |
|--------|----------------|
| `config/` | DB, Cloudinary, env, logger setup. |
| `controllers/` | Thin request handlers — parse, call service, respond. |
| `routes/` | Express route definitions. |
| `middleware/` | Auth (JWT), error handling, upload (Multer), validation. |
| `models/` | Mongoose schemas — User, Document, Conversation, Message, ProcessingStatus. |
| `services/` | Business logic and external calls (incl. the AI service client). |
| `repositories/` | All Mongoose queries — the only layer that touches the DB. |
| `validators/` | Request-body schema validation. |
| `utils/` | Generic helpers — JWT, hashing. |
| `helpers/` | Domain helpers — response formatters. |
| `uploads/` | Temp local upload staging (gitignored). |
| `jobs/` | Background jobs / queue workers. |
| `socket/` | WebSocket handlers — status push, answer streaming. |

## Env

`MONGODB_URI`, `JWT_SECRET`, `CLOUDINARY_*`, `AI_SERVICE_URL`, `CLIENT_URL`. See root README for the full list.
