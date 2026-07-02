---
service: adguard-sync
host: centuries
status: running
compose: /srv/docker/compose/hosts/centuries/adguard-sync/docker-compose.yml
last_updated: 2026-06-07
tags:
  - homelab/services
  - homelab/centuries
  - homelab/networking
---

## Overview

AdGuard Home Sync — keeps AdGuard Home config in sync between a primary (origin) and replica instance.

## Key config

- Sync interval: `CRON` (default: every 10 minutes)
- `ORIGIN_URL` / `REPLICA_URL`: from .env
- Log level: info (default)

## Dependencies

- Two AdGuard Home instances (origin + replica, URLs in .env)

## Notes

<!-- Origin and replica URLs, which host is primary -->
