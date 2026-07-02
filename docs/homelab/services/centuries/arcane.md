---
service: arcane
host: centuries
status: running
url: https://centuries.sqrd.link
compose: /srv/docker/compose/hosts/centuries/arcane/docker-compose.yml
last_updated: 2026-06-07
tags:
  - homelab/services
  - homelab/centuries
  - homelab/management
---

## Overview

Arcane — Docker management UI. Mounts the Docker socket to manage containers on centuries.

## Access

- URL: https://centuries.sqrd.link
- Routed via: Traefik

## Volumes

| Host path | Container path | Purpose |
|-----------|---------------|---------|
| `/var/run/docker.sock` | `/var/run/docker.sock` | Docker socket (read-only) |
| `/srv/docker/volumes/arcane/data` | `/app/data` | App data (SQLite DB) |

## Key config

- `TZ`: Europe/Amsterdam
- `APP_URL`: https://centuries.sqrd.link
- DB: SQLite with WAL mode

## Dependencies

- Traefik (proxy network)
- Docker socket access

## Notes

<!-- Admin setup, user accounts -->
