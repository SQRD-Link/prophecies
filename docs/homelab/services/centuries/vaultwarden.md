---
service: vaultwarden
host: centuries
status: running
url: https://vault.sqrd.link
compose: /srv/docker/compose/hosts/centuries/vaultwarden/docker-compose.yml
last_updated: 2026-06-07
tags:
  - homelab/services
  - homelab/centuries
  - homelab/security
---

## Overview

Vaultwarden — self-hosted Bitwarden-compatible password manager.

## Access

- URL: https://vault.sqrd.link
- Signups: disabled

## Volumes

| Host path | Container path | Purpose |
|-----------|---------------|---------|
| `/srv/docker/volumes/vaultwarden/vw-data` | `/data` | Vault data (SQLite) |

## Dependencies

- Traefik (proxy network)

## Notes

<!-- Admin token, backup strategy, client setup -->
