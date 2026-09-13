---
host: centuries
last_updated: 2026-06-07
tags:
  - homelab/services
  - homelab/centuries
---

## Centuries — Service Index

Docker VM running the main homelab stack. All services routed via Traefik on the `proxy` network, exposed externally through Pangolin (newt tunnel → `pangolin.prtsr.nl`).

## Services

| Service | URL | Category |
|---------|-----|----------|
| [[arcane]] | https://centuries.sqrd.link | Docker management |
| [[traefik]] | :8080 (internal dashboard) | Networking |
| [[newt]] | — | Networking |
| [[adguard-sync]] | — | Networking |
| [[openwebui]] | https://chat.sqrd.link | AI |
| [[n8n]] | https://n8n.sqrd.link | Automation |
| [[semaphore]] | https://semaphore.sqrd.link | Automation |
| [[netbox]] | https://netbox.sqrd.link | Documentation |
| [[arr_stack]] | jellyfin / sonarr / radarr / lidarr / bazarr / prowlarr / seerr / sabnzbd / qbittorrent | Media |
| [[vaultwarden]] | https://vault.sqrd.link | Security |
| [[mealie]] | https://mealie.sqrd.link | Home |
| [[odysseus]] | TBD | AI |

## Ops Journal

- [[ops-journal/2026-06-07 - Sparse Checkout Migration]]
