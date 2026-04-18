# TODO

## High priority
- [ ] Add a backend dependency manifest (`requirements.txt` or `pyproject.toml`) and pin versions.
- [ ] Add backend startup docs for local development and deployment profiles.
- [ ] Fix frontend lint errors currently failing `npm run lint`.
- [ ] Add automated tests for:
  - API routes (`/search`, `/test-translate`, `/test-queue`)
  - graph data normalization in frontend
  - core filtering/sorting logic in `ResearchGraph.jsx`
- [ ] Add robust error handling and user-facing messages for external API failures/rate limits.

## Functionality gaps
- [ ] Implement frontend `openAccess` / `closedAccess` filters in graph filtering logic.
- [ ] Implement frontend edge visibility toggles (`showModelEdges`, `showDatasetEdges`, `showOtherEdges`).
- [ ] Implement alpha sorting option (`sortBy: 'alpha'`) currently listed in UI controls.
- [ ] Wire optional layout controls (`force/radial/cluster`) to actual graph layout behavior.
- [ ] Add pagination / incremental loading for large search result graphs.

## Backend and data quality
- [ ] Add request/response schemas (Pydantic models) for API contracts.
- [ ] Add retry/backoff and observability for Semantic Scholar, ar5iv, and GenAI calls.
- [ ] Improve deduplication and canonicalization of model/dataset entities.
- [ ] Add migration/index strategy for Neo4j labels and relationships.
- [ ] Add configurable concurrency and timeout controls via environment variables.

## UX and product
- [ ] Add loading skeletons and better empty/error states in graph page.
- [ ] Add “save query” and “recent searches” support.
- [ ] Add export CSV option (control exists but is not wired in the current page).
- [ ] Improve accessibility (keyboard navigation, contrast checks, aria labels).

## DevOps
- [ ] Add CI for frontend lint/build and backend tests.
- [ ] Add environment examples (`.env.example`) for frontend and backend.
- [ ] Add Docker setup for one-command local stack (frontend + backend + Neo4j).
