# Finwise RAG Chatbot

Global financial advertising compliance assistant with document upload, hybrid retrieval (pgvector + regulations), Google embeddings, and Groq chat.

## Stack
- Docker Compose: Postgres 16 + pgvector, FastAPI API, Next.js frontend
- External APIs: Google `text-embedding-004`, Groq `llama-3.1-70b-versatile`

## Quick start
1. Copy `.env.example` to `.env` and set `GOOGLE_API_KEY` and `GROQ_API_KEY`.
2. `docker compose up --build` from `finwise/`.
3. Frontend at http://localhost:13000, API at http://localhost:18000, DB at localhost:55432.

## Services
- **API** (`/upload`, `/chat`, `/conversations/{id}`, `/regulations/refresh`, `/health`).
- **Frontend**: chat, upload, sources, settings.
- **DB**: Postgres with pgvector and pg_trgm extensions enabled via `db/init`.

## Data flow
- Upload → text extraction (pdf/docx/txt) → chunk (512 words, 50 overlap) → Google embeddings → stored in `chunks` + `embeddings` tables.
- Chat → embed query → hybrid search across user docs + `regulations` table → Groq completion with citations.

## Testing targets
- API unit tests (`pytest`) for chunking, embedding fallback, retrieval scoring.
- Integration: `docker compose up`, then use `scripts/smoke.http` examples (not yet created) or curl.
- Frontend: Cypress happy path (upload + chat) planned; scaffold only.

## Notes
- Sample regulations are seeded on startup (SEC/FCA/ESMA) to make chat usable immediately.
- If keys are missing, API runs in offline mode (deterministic fake embeddings + placeholder answers).
