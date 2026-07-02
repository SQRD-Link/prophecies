---
service: traefik
host: centuries
status: running
url: http://centuries-ip:8080
compose: /srv/docker/compose/hosts/centuries/traefik/docker-compose.yml
last_updated: 2026-06-07
tags:
  - homelab/services
  - homelab/centuries
  - homelab/networking
---

## Overview

Traefik v3.0 — reverse proxy handling TLS termination and routing for all services on centuries.

## Access

- Dashboard: port 8080 (insecure, internal only)
- Entrypoints: 80 (redirects to HTTPS), 443

## Volumes

| Host path | Container path | Purpose |
|-----------|---------------|---------|
| `/var/run/docker.sock` | `/var/run/docker.sock` | Docker label discovery (read-only) |
| `traefik_data` (named volume) | `/data` | ACME certs (acme.json) |

## Key config

- Docker provider: auto-discovers containers via labels
- Default network: `proxy`
- TLS: Cloudflare DNS challenge (`cf` resolver)
- HTTP → HTTPS redirect: enabled
- `exposedbydefault`: false (opt-in per service)

## Dependencies

- All services on the `proxy` network
- Cloudflare API token (`CF_DNS_API_TOKEN`) for cert issuance

## Known issues

- Config was locally modified before sparse checkout migration on 2026-06-07 → [[ops-journal/2026-06-07 - Sparse Checkout Migration]]

## Notes

<!-- Middleware configs, additional entrypoints, dashboard auth -->
