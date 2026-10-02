---
title: Tailscale Setup
tags: [homelab, tailscale, networking, remote-access]
created: 2026-03-30
updated: 2026-09-27
source: "[[Network Reference]] (current topology updated 2026-09-26)"
---

# Tailscale Setup and current topology

> The original Docker-on-centuries guide below is obsolete. Current Tailscale runs as native `tailscaled` on the Ubuntu LXC **teelskeel** (`10.10.100.13`). Do not deploy another subnet router from this old recipe without first checking the live Tailscale admin console and host configuration.

## Current documented configuration

- **Subnet router:** `teelskeel`, an LXC on Proxmox `grimoire`.
- **Implementation:** native Ubuntu `tailscaled`, not Docker.
- **Advertised routes:** Servers `10.10.100.0/24` and Management `10.10.10.0/24`.
- **Role:** remote access to those homelab subnets; also configured as an exit node.
- **Intentionally not advertised:** IoT (`10.10.20.0/24`), Trusted (`10.10.30.0/24`), Guest (`10.10.40.0/24`) and Leo (`10.10.50.0/24`).
- **Firewall:** inter-VLAN traffic, including Tailscale-forwarded traffic, is subject to `la-porta`'s first-match-wins gateway ACL. A subnet route alone does not imply the ACL permits access.

See [[Network Reference]] for the VLANs, documented ACL exceptions and names/IPs. Tailscale and the work Twingate client are separate systems.

## Read-only verification

Use these checks before troubleshooting or making changes; they inspect local status without editing configuration:

```bash
# On teelskeel
tailscale status
ip -brief address
ip route

# From a remote Tailscale-connected device, test Management by literal IP
curl -kI --connect-timeout 5 https://10.10.10.10:8006
curl -kI --connect-timeout 5 https://10.10.10.20:4443
```

The hostname rewrites `grimoire.sqrd.link` and `nastradamus.sqrd.link` resolve to centuries (`10.10.100.75`), not to the Management interfaces. Use raw IPs when verifying the Management route. Check the actual service port and certificate behavior before interpreting an HTTP/TLS result.

## When changing routes or ACLs

1. Confirm the live advertised routes and route approval in the Tailscale admin console.
2. Identify source, destination, protocol and port; trace both request and return paths through `la-porta` ACLs.
3. Do not advertise additional VLANs or loosen broad inter-VLAN policy without explicit need and approval.
4. After an authorized change, re-run the same connectivity test and verify the effective route from the client.

Do not use the retired recipe that installs `tailscale/tailscale` in Docker on `docker.sqrd.link`, or its old `TS_ROUTES=10.10.100.0/24`-only assumption. The former Tailscale LXC was `teelskeel`; it is the current router again per the current network reference.