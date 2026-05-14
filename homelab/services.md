---
title: Services
tags: [homelab, services, docker]
created: 2026-03-30
updated: 2026-03-30
---

# Services

## docker.sqrd.link · `10.10.100.75`

Main Docker VM on Proxmox. All containers have Traefik labels for automatic routing via `*.sqrd.link`.

### Reverse proxy & networking

| Container | Image | Notes |
|---|---|---|
| traefik | `traefik:latest` | Reverse proxy, ports 80/443/8080 exposed |

### Media

| Container | Image | Notes |
|---|---|---|
| jellyfin | linuxserver/jellyfin | Media server |
| plex | LXC on `.15` | GPU passthrough for HW transcode |
| sonarr | linuxserver/sonarr | TV show management |
| radarr | linuxserver/radarr | Movie management |
| bazarr | linuxserver/bazarr | Subtitles |
| lidarr | linuxserver/lidarr | Music management |
| prowlarr | linuxserver/prowlarr | Indexer manager |
| sabnzbd | linuxserver/sabnzbd | Usenet downloader |
| qbittorrent | linuxserver/qbittorrent | Torrent client |
| overseerr | sctx/overseerr | Request management |

### AI & automation

| Container | Image | Notes |
|---|---|---|
| n8n-app | n8nio/n8n | Workflow automation |
| n8n-mcp | ghcr.io/czlonkowski/n8n-mcp | MCP server for n8n |
| n8n-db | postgres:16-alpine | n8n database |
| openwebui | open-webui/open-webui | Ollama/LLM frontend |
| semaphore | semaphore-custom:local | Ansible UI |

### Infrastructure

| Container | Image | Notes |
|---|---|---|
| netbox | netboxcommunity/netbox | IPAM + Ansible inventory |
| netbox_postgres | postgres:17-alpine | Netbox database |
| netbox_redis_cache | valkey/valkey:8.1-alpine | Netbox cache |
| netbox_redis_task | valkey/valkey:8.1-alpine | Netbox task queue |
| arcane | getarcaneapp/arcane | Docker management UI |
| vaultwarden | vaultwarden/server | Password manager |
| adguard-sync | bakito/adguardhome-sync | Syncs AdGuard 1→2 |
| newt | fosrl/newt | Pangolin tunnel client |

### Apps

| Container | Image | Notes |
|---|---|---|
| immich_server | immich-app/immich-server | Photo management |
| immich_machine_learning | immich-app/immich-machine-learning | ML for Immich |
| immich_redis | redis:6.2-alpine | Immich cache |
| immich_postgres | tensorchord/pgvecto-rs:pg14 | Immich database |
| mealie | mealie-recipes/mealie | Recipe manager / meal planner |
| seerr | seerr-team/seerr | — |

---

## nastradamus · `10.10.100.12`

| Service | Notes |
|---|---|
| Proxmox Backup Server | VM at `.7` |
| AdGuard Home 2 | Docker, secondary DNS at `.2` |
| Omada Controller | Docker, `omada.sqrd.link` |
| Arcane | Docker management |
| Immich | Not in active use |
| Tdarr node | Not in active use |

---

## Hetzner VPS

| Container | Notes |
|---|---|
| Pangolin | Public reverse proxy |
| Gerbil | Tunnel server |
| Newt | Tunnel client |
| Arcane | Docker management |
| n8n | Separate instance |
| Waha | WhatsApp HTTP API |
| Uptime Kuma | Public status page |
| nginx website | prtsr.nl |

---

## Other LXC services (Proxmox)

| Host | IP | Service |
|---|---|---|
| hassanova | `.55` | Home Assistant |
| plex | `.15` | Plex (GPU passthrough) |
| modcaves | `.40` | Minecraft (Docker in LXC) |
| pangolin | `.253` | Pangolin reverse proxy |
| teelskeel | `.13` | Tailscale (being migrated) |

## Codex

All Docker Compose files are maintained in the GitHub repo `SQRD-Link/codex`.
