---
title: Hardware
tags: [homelab, hardware]
created: 2026-03-30
updated: 2026-03-30
---

# Hardware

## nastradamus · `10.10.100.12`

- **OS:** TrueNAS Scale
- **CPU:** Intel Core i5-10500
- **RAM:** 16 GB
- **Storage:** 12 TB (pool)
- **Role:** NAS, primary storage, container host

### Services on nastradamus

| Service | Address | Notes |
|---|---|---|
| Proxmox Backup Server | `10.10.100.7:8007` | VM |
| AdGuard Home 2 | `10.10.100.2` | Docker, secondary DNS |
| Omada Controller | `10.10.100.12:30077` / `omada.sqrd.link` | Docker |
| Arcane | — | Docker |
| Immich | — | Docker, not in active use |
| Tdarr node | — | Docker, not in active use |

---

## prox.sqrd.link · `10.10.100.8`

- **OS:** Proxmox VE
- **CPU:** Intel Core i5-10500T
- **RAM:** 32 GB
- **Storage:** 256 GB SSD (OS), 1 TB NVMe (data)
- **Role:** Hypervisor — VMs and LXC containers

### LXC Containers

| Name | IP | Role |
|---|---|---|
| AdGuard Home 1 | `10.10.100.1` | Primary DNS |
| teelskeel | `10.10.100.13` | Tailscale (legacy LXC, replaced by Docker) |
| plex | `10.10.100.15` | Plex Media Server (GPU passthrough for HW transcode) |
| modcaves | `10.10.100.40` | Minecraft servers (Docker in LXC) |
| pangolin | `10.10.100.253` | Reverse proxy + auth (Pangolin) |
| hassanova | `10.10.100.55` | Home Assistant |
| docker | `10.10.100.75` | Main Docker VM — see [[services]] |

### Todo

- [ ] GPU passthrough for Plex (to enable hardware transcoding in Docker)
- [ ] Migrate teelskeel Tailscale to Docker on docker.sqrd.link

---

## Hetzner VPS

- **Role:** Public edge node
- **Services:** Pangolin (public), Gerbil, Newt, Waha, Uptime-Kuma, n8n, website (nginx)
- **Domains:** `proxy.prtsr.nl`, `prtsr.nl`
- **Tunnel:** Points back to internal `pangolin.sqrd.link`
