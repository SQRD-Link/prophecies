---
title: Homelab Overview
tags: [homelab, infrastructure, index]
created: 2026-03-30
updated: 2026-09-26
source: Homie project doc homelab-overview.md
---

# Homelab Overview

This is Richard's personal homelab, running self-hosted services, automation and media. It is built around two physical servers (a TrueNAS box and a Proxmox hypervisor), a growing collection of Docker containers, and a static home IP as the public edge. The old Hetzner VPS was decommissioned for cost on 2026-09-05, permanently.

VLAN segmentation is complete (2026-09-25), and the Servers → Management boundary is hardened. See [[Network Reference]] for the compact current state. The network is also drawn as a live map at `mappa.sqrd.link`: [[Pianta della Rete]].

## Domains

| Domain | Purpose |
|---|---|
| `sqrd.link` | Internal / local services |
| `prtsr.nl` | Public-facing services |

- **Internal DNS** is handled by AdGuard Home, with a wildcard `*.sqrd.link → 10.10.100.252` (Pangolin). A new internal service is reachable as soon as it has a Pangolin route.
- **Two exceptions:** `grimoire.sqrd.link` and `nastradamus.sqrd.link` bypass Pangolin via AdGuard rewrites and go to centuries' Traefik instead.
- **Public DNS** (`prtsr.nl`) is in Cloudflare, DNS-only. It is not proxied, because Cloudflare's proxy is HTTP(S)-only and can't forward raw TCP like Minecraft.

## Network

- **Site:** `la-rocca` in Omada and NetBox (was `SQRD-Home`).
- **VLANs:**
  - Management 10: `10.10.10.0/24`
  - Servers 100: `10.10.100.0/24`
  - IoT 20
  - Trusted 30
  - Guest 40
  - Leo 50
- **Gateway:** `la-porta`, a TP-Link ER605 v2.0 managed by Omada. It is `.254` in every VLAN, was called `fw` until 2026-09-26, and enforces the gateway ACL.
- **Switch:** `il-cortile`, a TP-Link TL-SG2008P v1.0, at `10.10.10.201` (was `switch`). Powers both APs over PoE.
- **APs:**
  - `piano-terra`: EAP245, downstairs, `10.10.10.202` (was `beneden`)
  - `piano-nobile`: EAP225, upstairs, `10.10.10.203` (was `boven`)
- **DNS primary:** AdGuard Home 1 at `10.10.100.1`, an LXC on grimoire.
- **DNS secondary:** AdGuard Home 2 at `10.10.100.2`, Docker on nastradamus, synced via adguardhome-sync.
- **Reverse proxy:** Pangolin at `10.10.100.252`. It is both the internal edge and the public edge, via router port forwarding over the static home IP.

Naming: hosts follow the Nostradamus theme, while the site and network gear use Italian names that match the map (the site is the fortress, the gear its palazzo). Always rename in the owning system (Omada for network gear, Proxmox for guests), never only in NetBox. See the Naming section in [[Network Reference]].

## Servers

| Host | Management IP | Other IP | Role | Hardware |
|---|---|---|---|---|
| `nastradamus` | `10.10.10.20` | `10.10.100.12` (legacy, Docker macvlan apps) | NAS (TrueNAS Scale), also runs pbs as a VM | i5-10500, 16 GB RAM, 12 TB |
| `grimoire` | `10.10.10.10` | none | Hypervisor (Proxmox), standalone node, renamed from `prox` on 2026-09-26 | i5-10500T, 32 GB RAM, 1 TB NVMe |

Proxmox Backup Server (`pbs`) runs on the Management VLAN at `10.10.10.30`, as a VM hosted on TrueNAS.

~~Hetzner VPS: public edge (Pangolin)~~ was decommissioned on 2026-09-05, permanently.

## MCP servers connected

- **Omada MCP:** live queries against the Omada controller (devices, clients, traffic, PoE, firmware).
- **Home Assistant MCP:** controls and queries hassanova (`10.10.100.55`).
- **NetBox MCP:** read-only. Writes go through n8n.
- **Proxmox MCP:** reaches grimoire.
- **homie-mcp:** n8n workflow and execution management.

## Related notes

- [[Network Reference]]: compact live reference, including naming.
- [[Pianta della Rete]]: the network map project and its NetBox sync.
- [[Proxmox Rename - prox naar grimoire]]: the rename procedure and its lessons.
- [[homie-system-prompt]]: Homie's project instructions.
- Homie project docs (not in the vault): `hardware.md`, `network.md`, `services.md`, `tailscale-setup.md`, `ansible-workflow.md`.
