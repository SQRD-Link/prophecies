---
service: n8n
host: centuries
status: running
url: https://n8n.sqrd.link
compose: /srv/docker/compose/hosts/centuries/n8n/docker-compose.yml
last_updated: 2026-06-07
tags:
  - homelab/services
  - homelab/centuries
  - homelab/automation
---

## Overview

n8n workflow automation platform with PostgreSQL backend and n8n-MCP sidecar for Claude integration.

## Services

| Container | URL | Port |
|-----------|-----|------|
| n8n-app | https://n8n.sqrd.link | 5678 |
| n8n-mcp | internal (host port 3001) | 3000 |
| n8n-db | internal (postgres) | 5432 |

## Volumes

| Host path | Container path | Purpose |
|-----------|---------------|---------|
| `/srv/docker/volumes/n8n/n8n_data` | `/home/node/.n8n` | n8n data & workflows |
| `/srv/docker/volumes/n8n/n8n_db_data` | `/var/lib/postgresql/data` | PostgreSQL data |

## Key config

- DB: PostgreSQL 16
- `WEBHOOK_URL`: https://n8n.sqrd.link/
- Diagnostics & version notifications: disabled
- n8n-MCP: HTTP mode, auth via `MCP_AUTH_TOKEN`, security mode: moderate

## Dependencies

- Traefik (proxy network)
- Internal `n8n-net` network between app, db, and MCP

## Related workflows

- [[n8n/opdrachten/01-arr-stack-monitoring]]
- [[n8n/opdrachten/02-telegram-inbox-bot]]
- [[n8n MCP Homelab Setup]]

## Notes

<!-- Credential backup, webhook URLs, MCP endpoint config -->
