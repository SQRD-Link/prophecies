---
service: newt
host: centuries
status: running
compose: /srv/docker/compose/hosts/centuries/newt/docker-compose.yml
last_updated: 2026-06-07
tags:
  - homelab/services
  - homelab/centuries
  - homelab/networking
---

## Overview

Newt — Pangolin tunnel client. Connects centuries to the Pangolin server at `pangolin.prtsr.nl` for external access routing.

## Volumes

| Host path | Container path | Purpose |
|-----------|---------------|---------|
| `/var/run/docker.sock` | `/var/run/docker.sock` | Docker socket (read-only) |

## Key config

- `PANGOLIN_ENDPOINT`: https://pangolin.prtsr.nl
- `NEWT_ID` / `NEWT_SECRET`: from .env

## Dependencies

- Pangolin on harbinger

## Notes

<!-- Tunnel targets, which services are exposed via Pangolin -->
