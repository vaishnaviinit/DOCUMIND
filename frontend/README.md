# DocuMind — Frontend

React (Vite) single-page app. JavaScript, Tailwind CSS, React Router, Axios. Owned by the **Full Stack (MERN)** developer.

## Run

```bash
npm install
npm run dev        # http://localhost:5173
```

## Structure

| Folder | Responsibility |
|--------|----------------|
| `assets/` | Static images, fonts, logos. |
| `components/` | Reusable presentational components (buttons, chips, message bubbles). |
| `layouts/` | Page shells — `AuthLayout`, `AppLayout`. |
| `pages/` | Route-level screens — Login, Signup, Library, Chat. |
| `hooks/` | Custom hooks — `useAuth`, `useChat`, `useDocuments`. |
| `services/` | Axios API clients — `authApi`, `docApi`, `chatApi`. |
| `context/` | React Context providers — `AuthContext`. |
| `utils/` | Pure helpers — formatters, guards. |
| `styles/` | Tailwind config, global CSS. |
| `router/` | Route definitions and protected-route wrappers. |
| `config/` | Env-driven runtime config (`VITE_API_BASE_URL`). |
| `constants/` | Status labels, route names, enums mirrored from `shared/`. |
| `lib/` | Third-party wrappers (configured Axios instance). |
| `icons/` | SVG icon components. |

## Env

`VITE_API_BASE_URL` — base URL of the Express backend. See root README for the full list.
