# Architecture Overview

A minimal, modular approach that fits a one-day build.

## Suggested Components
- Web/API service: handles chat UI and routes API calls
- LiteMAAS client: calls LLM backend with safe defaults
- (Optional) Docs ingestion: simple loader + retrieval strategy

## Data Flow
1. User submits a question in the UI
2. API calls LiteMAAS with prompt + context
3. Response is rendered back to the user

## Environment
- `LITEMAAS_BASE_URL`, `LITEMAAS_API_KEY` for LLM access
- `PORT` for service port (default 8080)

## Non-Goals for Day 1
- Production-grade vector databases or complex sync pipelines
- Multi-service orchestration beyond the primary web/API
