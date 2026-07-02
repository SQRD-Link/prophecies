---
service: odysseus
host: centuries
status: running
url: 
compose: /srv/docker/compose/hosts/centuries/odysseus/docker-compose.yml
last_updated: 2026-06-07
tags:
  - homelab/services
  - homelab/centuries
  - homelab/ai
---

## Overview

Odysseus — self-hosted AI workspace. Includes a web UI, vector search via ChromaDB, web search via SearXNG, and push notifications via ntfy.

Source: https://github.com/pewdiepie-archdaemon/odysseus

## Access

- URL: <!-- fill in — add Traefik labels or direct port -->
- Odysseus app: port 7000

## Services

| Container | Host port | Purpose |
|-----------|-----------|---------|
| odysseus | 7000 | Main app |
| chromadb | 8100 | Vector DB (internal: 8000) |
| searxng | 127.0.0.1:8082 | Web search (remapped from 8080 — conflict with Traefik dashboard) |
| ntfy | 8091 | Push notifications |

## Volumes

| Host path | Container path | Purpose |
|-----------|---------------|---------|
| `./data` | `/app/data` | App data |
| `./logs` | `/app/logs` | Logs |
| `./data/ssh` | `/app/.ssh` | SSH keys for remote server cookbook |
| `./data/huggingface` | `/app/.cache/huggingface` | Local model cache |

## Key config

- `PUID` / `PGID`: 1000 (match to host user if different)
- `SEARXNG_INSTANCE`: http://searxng:8080 (internal, unchanged)
- `CHROMADB_HOST`: chromadb, port 8000 (internal)

## Port conflict fix

SearXNG remapped from default `127.0.0.1:8080` → `127.0.0.1:8082` to avoid clash with Traefik dashboard on 8080.

## Dependencies

- Traefik (proxy network) — if exposed via subdomain
- SearXNG must be healthy before odysseus starts (healthcheck gated)

## Notes

<!-- API keys, model config, subdomain setup -->
