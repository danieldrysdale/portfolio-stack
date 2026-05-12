# portfolio-stack

Docker Compose orchestration for the full AI portfolio stack.

## Services

| Service | Port | Description |
|---|---|---|
| doc-rag | 8001 | Document RAG pipeline — semantic search over your documents |
| smart-api | 8002 | AI text classification, summarisation, and entity extraction |
| task-agent | 8080 | ReAct pattern AI agent with tool use |

## Quick start

```bash
# Set your Anthropic API key
echo "ANTHROPIC_API_KEY=your_key_here" > .env

# Pull and start all services
docker compose up
```

## Requirements

- Docker with Compose
- Ollama running locally (for doc-rag and smart-api)
- Anthropic API key (for task-agent)
