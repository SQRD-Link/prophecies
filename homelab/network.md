---
title: Network
tags: [homelab, network, vlan, dns, omada]
created: 2026-03-30
updated: 2026-09-27
source: "[[Network Reference]] (live-state summary, updated 2026-09-26)"
---

# Network

> **Current state:** VLAN segmentation is complete. This replaces the old flat-network/planned-VLAN description. For the compact authoritative topology, ACL facts and naming rules, see [[Network Reference]]. Older plans in this note's history should not be treated as live configuration.

## VLANs

| VLAN | ID | Subnet | Purpose | Advertised via Tailscale |
|---|---:|---|---|---|
| Management | 10 | `10.10.10.0/24` | Proxmox, TrueNAS, PBS, switch, APs | Yes |
| Servers | 100 | `10.10.100.0/24` | VMs, LXCs and Docker services | Yes |
| IoT | 20 | `10.10.20.0/24` | Smart-home devices | No |
| Trusted | 30 | `10.10.30.0/24` | Personal and casting/media devices | No |
| Guest | 40 | `10.10.40.0/24` | Guest Wi-Fi; internet only | No |
| Leo | 50 | `10.10.50.0/24` | Leo's devices; scheduled SSID | No |

## Network infrastructure

| Device | Address | Role |
|---|---|---|
| `la-porta` | `.254` in each VLAN | TP-Link ER605 gateway; first-match-wins inter-VLAN ACL |
| `il-cortile` | `10.10.10.201` | TP-Link TL-SG2008P switch; PoE for APs |
| `piano-terra` | `10.10.10.202` | EAP245, downstairs |
| `piano-nobile` | `10.10.10.203` | EAP225, upstairs |
| `la-rocca` | Omada/NetBox site | Site name; refer to Omada site by `siteId` in integrations |

Hosts and infrastructure use separate naming themes: hosts/storage follow the Nostradamus theme; network/site names use the Italian/Da Vinci map theme. Rename a device in its owning system (Omada for network gear, Proxmox for guests), not only in NetBox.

## Important hosts

| Host | Address | Role |
|---|---|---|
| `grimoire` | `10.10.10.10` | Standalone Proxmox node; renamed from `prox`; old `10.10.100.8` is retired |
| `nastradamus` | `10.10.10.20`; legacy `10.10.100.12` remains in use | TrueNAS Scale; legacy address supports macvlan apps and related services |
| `pbs` | `10.10.10.30` | Proxmox Backup Server VM on TrueNAS |
| `centuries` | `10.10.100.75` | Main Docker VM and Traefik |
| `pangolin` | `10.10.100.252` | Internal and public reverse-proxy edge |
| `adguard` | `10.10.100.1` | Primary AdGuard Home |
| `teelskeel` | `10.10.100.13` | Tailscale subnet router, native `tailscaled` on Ubuntu LXC |
| `plex` | `10.10.100.15` | Plex LXC with GPU passthrough |
| `modcaves` | `10.10.100.40` | Minecraft services in Docker-in-LXC |
| `hassanova` | `10.10.100.55` | Home Assistant; its managed IoT devices are on VLAN 20 |

## DNS and public access

- AdGuard Home 1 (`10.10.100.1`) is primary; AdGuard Home 2 (`10.10.100.2`) is secondary and synced via `adguardhome-sync`.
- `*.sqrd.link` resolves to Pangolin at `10.10.100.252`.
- `grimoire.sqrd.link` and `nastradamus.sqrd.link` are deliberate AdGuard rewrites to centuries' Traefik at `10.10.100.75`.
- `omada.sqrd.link` reaches the controller at `10.10.100.12:30077`.
- `prtsr.nl` is public DNS in Cloudflare, DNS-only (not proxied). The static home IP and router port-forward expose Pangolin as the public edge. The Hetzner VPS/tunnel path has been decommissioned.

## Inter-VLAN policy (summary)

The ER605 gateway `la-porta` enforces ACLs for routed traffic; rule order is first-match-wins. Servers, IoT, Trusted, Leo and Guest are denied access to Management by default, with narrow exceptions for `centuries` and `teelskeel` to specific management services on `grimoire` and `nastradamus`. Client VLANs cannot directly reach Servers; approved application access goes through Pangolin. Home Assistant has specific IoT/Trusted access, and Leo has narrow service exceptions. Consult [[Network Reference]] for the documented rule summary; verify the live Omada UI before changing ACLs.

## Tailscale

Tailscale runs natively on `teelskeel` (`10.10.100.13`), not in Docker. It advertises the Servers (`10.10.100.0/24`) and Management (`10.10.10.0/24`) networks and acts as an exit node. IoT, Trusted, Leo and Guest routes are intentionally not advertised. Because DNS rewrites for `grimoire.sqrd.link` and `nastradamus.sqrd.link` point to centuries, use raw management IPs when testing the Management route.

For setup and safe verification details, see [[tailscale-setup]]. For current ACL details and the network diagram, see [[Network Reference]].