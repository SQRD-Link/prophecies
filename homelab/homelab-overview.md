---
title: Homelab Overview
tags: [homelab, infrastructure, index]
created: 2026-03-30
updated: 2026-03-30
---

# Homelab Overview

Personal homelab running self-hosted services, automation, and media. Built around two physical servers, a Proxmox hypervisor, and a growing collection of Docker containers.

## Domains

| Domain | Purpose |
|---|---|
| `sqrd.link` | Internal / local services |
| `prtsr.nl` | Public-facing services |

DNS is handled by AdGuard Home with a wildcard `*.sqrd.link → 10.10.100.253` (pangolin) so new services are reachable immediately after adding a route in Pangolin.

## Network

- **Subnet:** `10.10.100.0/24`
- **Switch/AP:** TP-Link Omada
- **DNS primary:** AdGuard Home 1 — `10.10.100.1` (LXC on Proxmox)
- **DNS secondary:** AdGuard Home 2 — `10.10.100.2` (Docker on nastradamus, synced via adguardhome-sync)
- **Reverse proxy (internal):** Pangolin — `10.10.100.253` (`pangolin.sqrd.link`)
- **Reverse proxy (public):** Pangolin on Hetzner VPS — `proxy.prtsr.nl`, tunnels to internal pangolin

## Servers

| Host | IP | Role | Hardware |
|---|---|---|---|
| `nastradamus.sqrd.link` | `10.10.100.12` | NAS (TrueNAS Scale) | i5-10500, 16 GB RAM, 12 TB |
| `prox.sqrd.link` | `10.10.100.8` | Hypervisor (Proxmox) | i5-10500T, 32 GB RAM, 1 TB NVMe |
| Hetzner VPS | — | Public edge (Pangolin) | Cloud |

## Related Notes

- [[hardware]] — detailed hardware specs
- [[network]] — VLAN plan, Omada config
- [[services]] — full service inventory
- [[tailscale-setup]] — remote access setup
- [[ansible-workflow]] — IaC with Netbox + Semaphore
