# Deploy checklist (Render / Railway)

## Required services (3)

| Service | Root directory | Type |
|---------|----------------|------|
| Redis | — | Managed Redis |
| API | `backend` | Docker |
| UI | `frontend` | **Static Site** (recommended) or Docker |

## Backend (`backend/`)

- **Root Directory:** `backend` (mandatory on monorepo hosts)
- **Builder:** Dockerfile (uses `PORT` automatically)
- **Health check:** `/api/health/`
- **Env:** `REDIS_URL`, `SERPAPI_API_KEY`, `FLIGHT_PRICE_PROVIDER=google_flights`, `DEBUG=False`
- **CORS:** `ALLOWED_ORIGINS=["https://YOUR-FRONTEND-URL"]` (valid JSON, exact URL)
- **Model:** `models/lgbm_model.pkl` is not in git — run once: `python scripts/train_model.py` (optional; app falls back to simulation)

## Frontend (`frontend/`)

- **Root Directory:** `frontend`
- **Recommended on Render:** Static Site — Build: `npm install && npm run build`, Publish: `dist`
- **Required build env:** `VITE_API_BASE_URL=https://YOUR-BACKEND-URL` (no trailing slash)
- Without `VITE_API_BASE_URL`, the UI calls `/api/...` on the frontend host → **404 / network errors**

## Do not

- Deploy from repo root without setting Root Directory
- Use root `railway.toml` `[build]` section (no Dockerfile at repo root)
- Set `VITE_API_BASE_URL` to `http://backend:8000` (Docker internal hostname)
