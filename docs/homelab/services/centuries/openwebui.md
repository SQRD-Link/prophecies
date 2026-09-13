---
service: openwebui
host: centuries
status: running
url: https://chat.sqrd.link
compose: /srv/docker/compose/hosts/centuries/openwebui/docker-compose.yml
last_updated: 2026-06-07
tags:
  - homelab/services
  - homelab/centuries
  - homelab/ai
---

## Overview

Open WebUI — self-hosted AI chat interface. Connects to external LLM APIs (Ollama disabled).

## Access

- Traefik label: `openwebui.sqrd.link`
- Pangolin resource: `chat.sqrd.link` (public-facing)
- Auth: enabled (`WEBUI_AUTH=true`)

## Volumes

| Host path | Container path | Purpose |
|-----------|---------------|---------|
| `/srv/docker/volumes/openwebui` | `/app/backend/data` | App data, user accounts, chat history |

## Key config

- `WEBUI_NAME`: SQRD AI
- `ENABLE_OLLAMA`: false
- Telemetry: fully disabled

## Dependencies

- Traefik (proxy network)
- Pangolin resource: `chat.sqrd.link`

## Known issues

- `.env` lost during sparse checkout migration on 2026-06-07, restored from backup → [[ops-journal/2026-06-07 - Sparse Checkout Migration]]

## Notes

<!-- API keys, model connections -->
