# portfolio-stack — Project Context

## What this is
Docker Compose orchestration for the full AI portfolio stack. Brings up doc-rag, smart-api, and task-agent as coordinated services with health checks and dependency ordering.

## Stack
- Docker Compose
- All images from GHCR (ghcr.io/danieldrysdale/*)

## Services
| Service | Port | Image |
|---|---|---|
| doc-rag | 8001 | ghcr.io/danieldrysdale/doc-rag:latest |
| smart-api | 8002 | ghcr.io/danieldrysdale/smart-api:latest |
| task-agent | 8080 | ghcr.io/danieldrysdale/task-agent:latest |

## Startup order
1. doc-rag and smart-api start first
2. Health checks confirm both are ready (uses python3 urllib, not curl — curl not in slim images)
3. task-agent starts only after both are healthy

## Key configuration
- Ollama runs on the HOST — services use `host.docker.internal:11434`
- ANTHROPIC_API_KEY loaded from `.env` file
- doc-rag ChromaDB data persists via named volume `doc-rag-data`
- sample_docs mounted from host at `~/PycharmProjects/doc-rag/sample_docs`

## Running the stack
```bash
# Start
docker compose up

# Pull latest images first
docker compose pull && docker compose up

# Stop
docker compose down

# Stop and remove volumes (clears ChromaDB)
docker compose down -v
```

## After starting — ingest sample docs
```bash
curl -X POST http://127.0.0.1:8001/ingest \
  -H "Content-Type: application/json" \
  -d '{"path": "/sample_docs"}'
```

## End-to-end test
```bash
curl -X POST http://127.0.0.1:8080/run \
  -H "Content-Type: application/json" \
  -d '{"goal": "Search my documents for the refund policy and summarise the key points"}' \
  | python3 -m json.tool
```

## .env file (not committed)
```
ANTHROPIC_API_KEY=your_key_here
```

## Notes
- doc-rag image is ~3GB due to sentence-transformers model weights
- First pull takes a while on slow connections
- Health checks use `python3 -c "import urllib.request; urllib.request.urlopen(...)"` — curl not available in slim images
