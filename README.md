# Scientific Literature Knowledge Graph

An end-to-end app for exploring connections between **papers**, **ML models**, and **datasets**.

- **Frontend:** React + Vite + D3 graph explorer
- **Backend:** FastAPI pipeline
- **Data store:** Neo4j
- **External services:** Semantic Scholar API, arXiv HTML (via ar5iv), Google GenAI

## What works right now

### Frontend (`/Frontend`)
- Landing page with product overview and quick navigation.
- Graph explorer page (`/graph`) with:
  - backend search input (`POST /search/?query=...`)
  - force-directed D3 graph rendering
  - node type filters (paper/model/dataset)
  - year range + minimum citations filters (paper nodes)
  - local node-title search
  - sorting (citations/year/degree)
  - max node cap
  - Top-K most-cited paper view
  - node detail side panel (metadata, abstract, neighbors)
  - export visible graph as JSON
  - dark/light mode toggle persisted in localStorage
- Fallback to bundled demo data when backend is unavailable.

### Backend (`/Backend`)
- FastAPI app with startup/shutdown lifespan hooks.
- On startup:
  - connects to Neo4j
  - starts background worker tasks (`CONCURRENCY_LIMIT = 3`)
- Main API flow (`POST /search/`):
  1. Translate user query to Semantic Scholar keywords (`generate_semantic_scholar_query`)
  2. Fetch candidate papers from Semantic Scholar (`search_papers`)
  3. Keep papers that contain arXiv IDs
  4. For each paper: fetch arXiv HTML (via ar5iv), extract models/datasets via GenAI, upsert into Neo4j
  5. Read graph from Neo4j and return `{ nodes, edges }`
- Test/support endpoints:
  - `GET /`
  - `POST /test-queue/{paper_id}`
  - `POST /test-translate/?query=...`

## Repository structure

- `Frontend/` — React client
  - `src/pages/ResearchGraph.jsx` — main graph page orchestration
  - `src/components/Graph.jsx` — D3 force simulation + rendering
  - `src/services/api.js` — API client, dummy data, normalization
- `Backend/` — FastAPI service
  - `main.py` — app bootstrap + CORS + worker startup
  - `api/routes.py` — HTTP endpoints
  - `core/database.py` — Neo4j connectivity and graph queries
  - `services/` — Semantic Scholar, arXiv HTML parsing (via ar5iv), LLM integration, paper processing
  - `worker/` — async queue + background processor

## Prerequisites

- Node.js 20+
- Python 3.10+
- Neo4j database (Aura or local)
- Google GenAI API key
- (Optional) Semantic Scholar API key

## Quick start

### 1) Backend setup

```bash
cd Backend
python -m venv .venv
source .venv/bin/activate
pip install fastapi uvicorn neo4j python-dotenv google-genai httpx beautifulsoup4 lxml pydantic
```

Create `Backend/.env` (or repo-root `.env`) with:

```env
NEO4J_URI=bolt+s://<host>
NEO4J_USER=neo4j
NEO4J_PASSWORD=<password>
GEMINI_API_KEY=<your_key>
SEMANTIC_SCHOLAR_API_KEY=<optional>
```

Run backend:

```bash
cd Backend
uvicorn main:app --reload --host 127.0.0.1 --port 8000
```

### 2) Frontend setup

```bash
cd Frontend
npm ci
npm run dev
```

Default frontend URL: `http://127.0.0.1:5173`

If needed, set API base URL:

```bash
# Frontend/.env
VITE_API_BASE_URL=http://127.0.0.1:8000
```

## API summary

| Method | Route | Purpose |
|---|---|---|
| GET | `/` | Health/info message |
| POST | `/search/?query=<text>` | Main search + extraction + graph response |
| POST | `/test-translate/?query=<text>` | Query translation check |
| POST | `/test-queue/{paper_id}` | Queue worker test |

Example `/search/` response shape:

```json
{
  "search_query": "graph neural networks",
  "results_found": 10,
  "graph": {
    "nodes": [],
    "edges": []
  }
}
```

## Current limitations

See [`TODO.md`](./TODO.md) for prioritized improvements and cleanup items.
