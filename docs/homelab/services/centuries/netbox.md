---
service: netbox
host: centuries
status: running
url: https://netbox.sqrd.link
compose: /srv/docker/compose/hosts/centuries/netbox/docker-compose.yml
last_updated: 2026-06-07
tags:
  - homelab/services
  - homelab/centuries
  - homelab/networking
---

## Overview

NetBox — network source of truth. IPAM, device inventory, and network documentation.

## Services

| Container | Role |
|-----------|------|
| netbox | App (port 8080) |
| netbox_postgres | PostgreSQL 17 |
| netbox_redis_task | Valkey 8.1 (task queue) |
| netbox_redis_cache | Valkey 8.1 (cache) |

## Access

- URL: https://netbox.sqrd.link
- Also accessible internally via `netbox.local`

## Volumes

| Host path | Purpose |
|-----------|---------|
| `/srv/docker/volumes/netbox/config` | NetBox config files |
| `/srv/docker/volumes/netbox/media` | Uploaded media |
| `/srv/docker/volumes/netbox/db` | PostgreSQL data |
| `/srv/docker/volumes/netbox/redis_task` | Task queue persistence |
| `/srv/docker/volumes/netbox/redis_cache` | Cache persistence |

## Key config

- DB: PostgreSQL 17, database `netbox`
- GraphQL: enabled
- Web workers: 2

## Dependencies

- Traefik (proxy network)

## Notes

<!-- API token location, custom fields, scripts -->
