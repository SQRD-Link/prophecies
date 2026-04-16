---
title: Tailscale Setup
tags: [homelab, tailscale, networking, remote-access]
created: 2026-03-30
updated: 2026-03-30
---

# Tailscale Setup

Tailscale provides zero-config remote access to the homelab without opening any inbound ports. It runs as a subnet router on `docker.sqrd.link`, exposing the full `10.10.100.0/24` network.

When VLAN segmentation is in place, add additional subnet routes for each new VLAN.

## Architecture

```
[remote device]
    ↓ Tailscale mesh VPN
[Tailscale subnet router — Docker on docker.sqrd.link]
    ↓ subnet routing
[10.10.100.0/24 — full homelab]
```

No need to install Tailscale on every host. One subnet router exposes everything.

## Prerequisites

- Tailscale account (free Starter plan: 1 user, 100 devices)
- Docker and Docker Compose on `docker.sqrd.link`
- Linux kernel with TUN module enabled (check: `modinfo tun`)

## Setup

### 1. Create a Tailscale auth key

1. Go to [tailscale.com/admin](https://tailscale.com/admin) → Settings → Keys
2. Generate an **Auth Key** — enable "Reusable" and "Pre-authorized"
3. Copy the key

### 2. Enable IP forwarding on the Docker host

```bash
echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
echo 'net.ipv6.conf.all.forwarding = 1' | sudo tee -a /etc/sysctl.d/99-tailscale.conf
sudo sysctl -p /etc/sysctl.d/99-tailscale.conf
```

### 3. Docker Compose

Add to your `docker.sqrd.link` compose stack (in `SQRD-Link/codex`):

```yaml
services:
  tailscale:
    image: tailscale/tailscale:latest
    container_name: tailscale
    hostname: docker-homelab
    environment:
      - TS_AUTHKEY=${TS_AUTHKEY}
      - TS_STATE_DIR=/var/lib/tailscale
      - TS_ROUTES=10.10.100.0/24
      - TS_EXTRA_ARGS=--advertise-exit-node
    volumes:
      - tailscale-data:/var/lib/tailscale
      - /dev/net/tun:/dev/net/tun
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    restart: unless-stopped

volumes:
  tailscale-data:
```

Add `TS_AUTHKEY` to your `.env` file:

```env
TS_AUTHKEY=tskey-auth-xxxxx
```

### 4. Approve the subnet route

After the container starts:

1. Go to [tailscale.com/admin/machines](https://tailscale.com/admin/machines)
2. Find `docker-homelab`
3. Click the three-dot menu → Edit route settings
4. Enable `10.10.100.0/24`
5. Optionally enable it as an exit node

### 5. Enable MagicDNS (optional but recommended)

In the Tailscale admin panel → DNS → enable MagicDNS. Your homelab machines will be reachable via `nastradamus.yourtailnet.ts.net` etc.

## Connecting

Install the Tailscale client on your devices. Sign in with the same account. Your homelab subnet will be accessible automatically — no VPN config needed.

```bash
# Verify routing on a connected device
ping 10.10.100.8       # prox
curl http://10.10.100.253  # pangolin
```

## Future: VLAN subnets

When VLANs are set up, add routes for each segment:

```yaml
- TS_ROUTES=10.10.100.0/24,10.10.10.0/24,10.10.20.0/24,10.10.30.0/24
```

Then approve each new route in the Tailscale admin panel.

## Notes

- Tailscale and Twingate (work) are separate clients — they don't conflict
- The `teelskeel` LXC (`.13`) was the previous Tailscale host — now deprecated in favour of Docker
- Tailscale uses split tunneling by default — only homelab traffic goes through it, everything else uses your normal internet connection
