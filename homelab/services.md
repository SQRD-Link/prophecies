---
title: Services
tags: [homelab, services, docker]
created: 2026-03-30
updated: 2026-09-27
source: "[[Homie System Prompt]] and [[Network Reference]] (updated 2026-09-26); runtime status not queried"
---

# Services

> **Inventory status:** this is a documentation-based inventory, not a live container check. The former list mixed hosts and included a decommissioned VPS. Verify current container/service state in Arcane, Docker or the owning application before maintenance. Host/IP topology: [[Network Reference]].

## Main Docker VM — centuries · `10.10.100.75`

Runs on Proxmox host `grimoire`. Documented core services include:

| Service | Role / notes |
|---|---|
| Traefik | Reverse proxy for internal services; Cloudflare DNS challenge for TLS |
| NetBox | IPAM and Ansible inventory; source of truth for hosts/IPs |
| n8n | Workflow automation; database is PostgreSQL |
| Semaphore | Ansible UI and scheduler; uses `SQRD-Link/the_codex` |
| Media / app stack | The older inventory lists Jellyfin, Sonarr, Radarr, Bazarr, Lidarr, Prowlarr, SABnzbd, qBittorrent, Overseerr/Seerr, Mealie and Immich components. Treat these as previously documented workloads, not a verified current runtime list. |
| Other previously listed services | Open WebUI, Vaultwarden, AdGuard sync, Newt and supporting PostgreSQL/Valkey containers; reconcile against the current Compose repo/Arcane before changing. |

Compose and Ansible source of truth: `SQRD-Link/the_codex`, under `hosts/centuries/` and `ansible/`. Server-side compose paths are documented as `/srv/docker/compose/<app>/docker-compose.yml`; volumes are under `/srv/docker/volumes/<app>/`. Fetch current repository files before editing or deploying.

## nastradamus · TrueNAS · `10.10.10.20`

Legacy service/macvlan address `10.10.100.12` remains in use.

| Service | Address / notes |
|---|---|
| Proxmox Backup Server (`pbs`) | VM at `10.10.10.30` |
| AdGuard Home 2 | `10.10.100.2`; secondary DNS, synced from AdGuard 1 |
| Omada Controller | `10.10.100.12:30077`, also `omada.sqrd.link` |
| Arcane | Docker manager, moved here from centuries |
| Immich / Tdarr node | Older notes say inactive; status not live-verified |

## Proxmox guests on grimoire

| Guest | IP | Service |
|---|---|---|
| `centuries` | `10.10.100.75` | Main Docker VM |
| `pangolin` | `10.10.100.252` | Internal and public reverse-proxy edge |
| `adguard` | `10.10.100.1` | Primary DNS |
| `teelskeel` | `10.10.100.13` | Native Tailscale subnet router + exit node |
| `plex` | `10.10.100.15` | Plex with GPU passthrough |
| `modcaves` | `10.10.100.40` | Minecraft; Docker in LXC |
| `hassanova` | `10.10.100.55` | Home Assistant; managed IoT devices are on VLAN 20 |

`grimoire`, `nastradamus` and `hassanova` are intentionally tagged `no_ansible`; exclude them from bulk Ansible runs.

## Edge, DNS and domains

- Internal service names use `*.sqrd.link`; AdGuard's wildcard resolves to Pangolin (`10.10.100.252`). Pangolin routes internal requests to services such as centuries' Traefik.
- `grimoire.sqrd.link` and `nastradamus.sqrd.link` are exceptions that resolve to centuries' Traefik (`10.10.100.75`).
- Public DNS for `prtsr.nl` is in Cloudflare and DNS-only. Pangolin on the home network is the public edge through the static home IP and router port-forwarding.
- The Hetzner VPS `harbinger`, its tunnel stack, and its hosted services were decommissioned on 2026-09-05. Do not treat the old VPS service table as current or deploy there.

## Deployment conventions

- Prefer Docker Compose maintained in `SQRD-Link/the_codex`; read the current compose file before modifying it.
- For new services on centuries, use the shared `proxy` network and Traefik labels where appropriate; a service name under `*.sqrd.link` also needs the relevant Pangolin route.
- Keep secrets out of committed compose files; use the repo's documented secret/environment handling and include `.env.example` as appropriate.
- For changes spanning hosts, prefer Ansible playbooks. Respect the `no_ansible` exclusions above.