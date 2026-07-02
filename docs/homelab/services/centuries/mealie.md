---
service: mealie
host: centuries
status: running
url: https://mealie.sqrd.link
compose: /srv/docker/compose/hosts/centuries/mealie/docker-compose.yml
last_updated: 2026-06-07
tags:
  - homelab/services
  - homelab/centuries
---

## Overview

Mealie — self-hosted recipe manager.

## Access

- URL: https://mealie.sqrd.link
- Signups: disabled

## Volumes

| Host path | Container path | Purpose |
|-----------|---------------|---------|
| `/srv/docker/volumes/mealie` | `/app/data` | All app data |

## Dependencies

- Traefik (proxy network)

## Notes

<!-- API key, household setup, integrations -->
