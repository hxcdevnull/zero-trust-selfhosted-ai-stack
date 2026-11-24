# Zero-Trust Self-Hosted AI & Automation Stack

Containerized stack for `n8n`, `Ollama`, `PostgreSQL 16 + pgvector`, `NocoDB`, and `Metabase` behind `Caddy` and `Cloudflare Tunnel` with **zero published host ports**.

## Architecture

```text
Internet -> Cloudflare Edge -> cloudflared -> Caddy (:80, ai_internal_net)
  |-- n8n:5678
  |-- nocodb:8080
  |-- metabase:3000
  `-- Backend: PostgreSQL 16 (pgvector) + Ollama (:11434)
```

## Security & Design Controls
- **Zero Host Ports:** Ingress flows strictly through `cloudflared` to `http://caddy:80` inside `ai_internal_net`. (If running `cloudflared` on the host, map `127.0.0.1:80:80` on `caddy`.)

## Cloudflare Tunnel Ingress
Route public hostnames in Cloudflare Zero Trust to `http://caddy:80`:
- `n8n.example.com` -> `http://caddy:80`
- `db.example.com` -> `http://caddy:80`
- `bi.example.com` -> `http://caddy:80`

## Quick Start
1. `cp .env.example .env` (generate secrets via `openssl rand -hex 32`).
2. `docker compose config --quiet && docker compose up -d`
3. `docker compose exec ollama ollama pull llama3.1`
4. `docker compose exec ollama ollama pull nomic-embed-text`
