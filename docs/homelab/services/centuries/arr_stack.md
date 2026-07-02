---
service: arr_stack
host: centuries
status: running
compose: /srv/docker/compose/hosts/centuries/arr_stack/docker-compose.yml
last_updated: 2026-06-07
tags:
  - homelab/services
  - homelab/centuries
  - homelab/media
---

## Overview

Full media automation stack — download, manage, and stream movies, TV, and music.

## Services

| Container | URL | Port |
|-----------|-----|------|
| jellyfin | https://jellyfin.sqrd.link | 8096 |
| sonarr | https://sonarr.sqrd.link | 8989 |
| radarr | https://radarr.sqrd.link | 7878 |
| lidarr | https://lidarr.sqrd.link | 8686 |
| bazarr | https://bazarr.sqrd.link | 6767 |
| prowlarr | https://prowlarr.sqrd.link | 9696 |
| seerr | https://seer.sqrd.link / https://verzoekjes.sqrd.link | 5055 |
| sabnzbd | https://sabnzbd.sqrd.link | 8080 |
| qbittorrent | https://qbittorrent.sqrd.link | 8080 |
| recyclarr | internal only | — |
| flaresolverr | internal only | — |

## Volumes

| Host path | Purpose |
|-----------|---------|
| `/srv/docker/volumes/jellyfin/config` | Jellyfin config |
| `/srv/docker/volumes/sonarr/config` | Sonarr config |
| `/srv/docker/volumes/radarr/config` | Radarr config |
| `/srv/docker/volumes/lidarr/config` | Lidarr config |
| `/srv/docker/volumes/bazarr/config` | Bazarr config |
| `/srv/docker/volumes/prowlarr/config` | Prowlarr config |
| `/srv/docker/volumes/seerr/config` | Seerr config |
| `/srv/docker/volumes/sabnzbd/config` | SABnzbd config |
| `/srv/docker/volumes/qbittorrent/config` | qBittorrent config |
| `/srv/docker/volumes/recyclarr/config` | Recyclarr config |
| `/data/media/movies` | Movies library |
| `/data/media/TV` | TV library |
| `/data/media/music` | Music library |
| `/data/media/downloads` | Download staging |
| `/data/media/tempdl` | SABnzbd temp downloads |

## Key config

- All *arr services run as PUID/PGID 3001
- TLS via Cloudflare cert resolver (`cf`)
- Recyclarr syncs quality profiles via `RADARR_API_KEY` / `SONARR_API_KEY`

## Dependencies

- Traefik (proxy network)

## Known issues

- `.env` lost during sparse checkout migration on 2026-06-07, restored from backup → [[ops-journal/2026-06-07 - Sparse Checkout Migration]]

## Notes

<!-- Indexer config, download client connections, quality profiles -->
