---
title: Hardware
tags: [homelab, hardware]
created: 2026-03-30
updated: 2026-09-27
source: "[[Network Reference]] (topology updated 2026-09-26)"
---

# Hardware and hosts

> Addresses and guest roles below reflect the current homelab notes, not a live hardware probe. Use NetBox/Proxmox/TrueNAS to verify before maintenance. See [[Network Reference]] for current VLAN topology.

## nastradamus · TrueNAS Scale

- **Management:** `10.10.10.20` (VLAN 10)
- **Legacy service address:** `10.10.100.12` (VLAN 100), still used by macvlan apps and some integrations/configuration
- **CPU / RAM:** Intel Core i5-10500 / 16 GB
- **Storage:** documented as a 12 TB pool
- **Role:** NAS, primary storage, Docker host, and host for the PBS VM
- **Datasets documented:** `dataverse` (main pool), `almanac` (backups), `automatons` (TrueNAS apps), `chronicles` (Immich photos), `visions` (media/arr suite)

### Documented services

| Service | Address / placement | Notes |
|---|---|---|
| Proxmox Backup Server (`pbs`) | `10.10.10.30` | VM hosted on TrueNAS |
| AdGuard Home 2 | `10.10.100.2` | Secondary DNS; synced from AdGuard 1 |
| Omada Controller | `10.10.100.12:30077` / `omada.sqrd.link` | Docker |
| Arcane | `docker.sqrd.link` | Arcane manager moved here from centuries |
| Immich | — | Previously documented as inactive; verify before relying on this status |
| Tdarr node | — | Previously documented as inactive; verify before relying on this status |

## grimoire · Proxmox VE

- **Management:** `10.10.10.10` (VLAN 10)
- **Status:** standalone Proxmox node; renamed from `prox` on 2026-09-26; former single-node cluster was dissolved. The old `10.10.100.8` address is retired.
- **CPU / RAM:** Intel Core i5-10500T / 32 GB
- **Storage documented:** 256 GB SSD for OS and 1 TB NVMe for data
- **Role:** hypervisor for VMs and LXC containers

### Documented guests

| Guest | IP | Role |
|---|---|---|
| `centuries` | `10.10.100.75` | Main Docker VM: Traefik, NetBox, n8n, Semaphore and other services |
| `pangolin` | `10.10.100.252` | Internal and public reverse-proxy edge |
| `adguard` | `10.10.100.1` | AdGuard Home 1, primary DNS |
| `teelskeel` | `10.10.100.13` | Ubuntu LXC; native Tailscale subnet router and exit node |
| `plex` | `10.10.100.15` | Plex LXC with GPU passthrough |
| `modcaves` | `10.10.100.40` | Minecraft servers (Docker inside LXC) |
| `hassanova` | `10.10.100.55` | Home Assistant; intentionally tagged `no_ansible` |

`grimoire`, `nastradamus` and `hassanova` are intentionally excluded from bulk Ansible runs (`no_ansible`). Playbooks are for the designated Linux server hosts, not all Proxmox guests or appliances.

## Retired infrastructure

- **Hetzner `harbinger`:** VPS decommissioned on 2026-09-05. It is not a live host and must not be used as a deployment target. Public-edge duties moved to Pangolin on the home connection.

## Maintenance notes

The older TODO about enabling Plex GPU passthrough is obsolete: Plex already has documented GPU passthrough. The current known gap is that `centuries` does not have GPU passthrough. Verify actual hardware/guest configuration in Proxmox before changing passthrough settings.