---
title: Network
tags: [homelab, network, vlan, dns, omada]
created: 2026-03-30
updated: 2026-03-30
---

# Network

## Current Setup

Single flat subnet: `10.10.100.0/24`

- **Gateway:** ISP router
- **Switch/AP:** TP-Link Omada (`omada.sqrd.link`)
- **DNS:** AdGuard Home (primary `.1`, secondary `.2`)
- **Wildcard DNS:** `*.sqrd.link → 10.10.100.253` (Pangolin handles routing from there)

## Planned: VLAN Segmentation

Since the Omada switch supports VLANs, this is the recommended layout. Keeps IoT devices isolated, limits blast radius, and lets Tailscale do clean subnet routing per segment.

| VLAN | ID | Subnet | Purpose |
|---|---|---|---|
| Management | 10 | `10.10.10.0/24` | Proxmox, TrueNAS, switches, APs |
| Servers | 100 | `10.10.100.0/24` | Current flat network — LXC, VMs, Docker |
| IoT | 20 | `10.10.20.0/24` | Home Assistant devices, smart plugs |
| Trusted | 30 | `10.10.30.0/24` | Personal devices (laptops, phones) |
| Guest | 40 | `10.10.40.0/24` | Guest WiFi — internet only, no LAN access |

### VLAN inter-routing rules (recommended)

- Trusted → Servers: allowed
- Management → Servers: allowed
- IoT → Servers: blocked (Home Assistant is the exception — allow `.55` only)
- Guest → everything: blocked

### Implementation order

1. Create VLANs in Omada controller
2. Tag trunk ports to Proxmox and nastradamus
3. Add VLAN interfaces in Proxmox (Linux bridges per VLAN)
4. Migrate management interfaces to VLAN 10
5. Move IoT devices to VLAN 20
6. Update AdGuard to handle DNS per VLAN
7. Update Tailscale subnet routes to cover all new subnets

## DNS

| Server | IP | Role |
|---|---|---|
| AdGuard Home 1 | `10.10.100.1` | Primary — LXC on Proxmox |
| AdGuard Home 2 | `10.10.100.2` | Secondary — Docker on nastradamus, synced via adguardhome-sync |

### Key DNS entries

| Record | Target | Notes |
|---|---|---|
| `*.sqrd.link` | `10.10.100.253` | Wildcard → Pangolin |
| `omada.sqrd.link` | `10.10.100.12:30077` | Omada controller |

## Reverse Proxy

**Internal:** Pangolin at `10.10.100.253` — all `*.sqrd.link` routes terminate here. New service = new route in Pangolin, no DNS change needed.

**Public:** Pangolin on Hetzner VPS (`proxy.prtsr.nl`) — tunnels inbound traffic back to internal Pangolin via Gerbil/Newt.
