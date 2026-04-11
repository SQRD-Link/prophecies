---
title: "Docker Host Migratie Plan — LXC naar VM"
tags: [homelab, docker, portainer, proxmox, migratie]
created: 2026-04-11
verwant: "[[Lessons Learned - NetBox naar Portainer Migratie]]"
---

# 🚀 Docker Host Migratie Plan — LXC naar VM

> Gefaseerd plan voor het migreren van een bestaande Docker setup van een LXC container naar een VM. Behoudt rollback mogelijkheid tot het einde.

---

## Phase 1 — Voorbereiding & Infrastructuur (nieuwe VM)

### 1. Nieuwe VM provisionen
- Maak VM aan in Proxmox met minimale Ubuntu Server of Debian
- Wijs voldoende RAM, CPU en **SSD opslag** toe voor alle container volumes

### 2. Core software installeren
```bash
sudo apt update && sudo apt upgrade -y
# Docker installeren (zie [[Docker Installatie Guide Ubuntu]])
# rsync installeren voor data transfer
sudo apt install -y rsync
```
- Installeer Portainer als eerste container, maar **nog geen applicaties deployen**

### 3. Netwerk aanmaken
```bash
docker network create traefik_proxy
sudo mkdir -p /data/docker/volumes
```

---

## Phase 2 — Inventaris (oude LXC host)

### 4. Container inventaris opmaken

| Container | Image | Huidig pad (source) | Nieuw pad (target) |
|---|---|---|---|
| `netbox` | `netboxcommunity/netbox` | `/var/lib/docker/volumes/netbox_data/_data` | `/data/docker/volumes/netbox/data` |
| `plex` | `linuxserver/plex` | `/mnt/media/plex/config` | `/data/docker/volumes/plex/config` |
| `portainer` | `portainer/portainer-ce` | `/var/lib/docker/volumes/portainer_data/_data` | `/data/docker/volumes/portainer/data` |

Maak voor elke service een nieuwe `docker-compose.yml` met nieuwe paden en `traefik_proxy` netwerk.

### 5. Traefik labels documenteren
Zorg dat alle labels (rules, entrypoints) in de nieuwe compose bestanden staan.

---

## Phase 3 — Data migratie & validatie

### 6. Data overdragen
```bash
# Stop alle containers op de OUDE host eerst!
docker compose down

# Bind mount kopiëren (vanaf nieuwe VM):
rsync -avz root@<OUD_LXC_IP>:/mnt/media/plex/config /data/docker/volumes/plex/

# Named volume kopiëren — gebruik ephemeral container methode
```

### 7. Deployen op nieuwe VM
```bash
# Eerst Traefik starten
cd /data/docker/traefik && docker compose up -d

# Dan overige services
cd /data/docker/<service> && docker compose up -d
```

### 8. Validatie
- Check Portainer → alle containers running en stabiel
- Test elke publieke URL via Traefik
- Verifieer dat applicaties toegang hebben tot hun data

---

## Phase 4 — Afsluiting

### 9. Decommissioning
- Zodra alles stabiel is op de nieuwe VM: zet de oude LXC container **uit** (niet verwijderen)
- Wacht 1-2 weken voor definitieve verwijdering als veiligheidsmarge

---

## Verwant
- [[Lessons Learned - NetBox naar Portainer Migratie]]
- [[Docker Installatie Guide Ubuntu]]
