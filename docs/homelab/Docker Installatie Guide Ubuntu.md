---
title: "Docker Installatie Guide — Ubuntu"
tags: [homelab, docker, ubuntu, linux, installatie]
created: 2026-04-11
verwant: "[[🚀 Docker Host Migration Plan LXC to VM]]"
---

# Docker Installatie Guide — Ubuntu

> Volledig script + handmatige stappen voor het installeren van Docker Engine en Docker Compose op Ubuntu.

## Snelle installatie (script)

```bash
chmod +x install-docker-compose.sh
./install-docker-compose.sh
```

Het script doet achtereenvolgens:
1. Oude Docker versies verwijderen
2. Systeem updaten + prerequisites installeren
3. Officiële Docker repository toevoegen (incl. GPG key verificatie)
4. Docker Engine + Compose plugin installeren
5. Docker service configureren met log rotatie en overlay2 driver
6. Standalone `docker-compose` binary installeren voor legacy compatibiliteit
7. Voorbeeldconfigs aanmaken in `~/docker-examples/`

## Handmatige installatie

### 1. Opruimen
```bash
sudo apt remove docker docker-engine docker.io containerd runc
```

### 2. Prerequisites
```bash
sudo apt install -y apt-transport-https ca-certificates curl gnupg lsb-release
```

### 3. Docker repository toevoegen
```bash
sudo mkdir -p /usr/share/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### 4. Installeren
```bash
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### 5. Configureren
```bash
sudo systemctl enable --now docker
sudo usermod -aG docker $USER
newgrp docker  # of uitloggen/inloggen
```

### 6. Daemon configuratie (`/etc/docker/daemon.json`)
```json
{
    "log-driver": "json-file",
    "log-opts": { "max-size": "10m", "max-file": "3" },
    "storage-driver": "overlay2",
    "userland-proxy": false
}
```

## Troubleshooting

| Probleem | Oplossing |
|---|---|
| Permission denied | `newgrp docker` of uitloggen/inloggen |
| `docker compose` niet gevonden | `sudo apt install --reinstall docker-compose-plugin` |
| Service start niet | `sudo journalctl -u docker.service -f` |

## Uninstall
```bash
sudo apt remove -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo rm -rf /var/lib/docker /var/lib/containerd /etc/docker
sudo rm -f /etc/apt/sources.list.d/docker.list /usr/share/keyrings/docker-archive-keyring.gpg
sudo deluser $USER docker
```

## Verwant
- [[🚀 Docker Host Migration Plan LXC to VM]]
- [[🎓 Lessons Learned from NetBox to Portainer Migration]]
