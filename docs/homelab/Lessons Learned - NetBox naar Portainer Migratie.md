---
title: "Lessons Learned — NetBox naar Portainer Migratie"
tags: [homelab, docker, portainer, netbox, lessons-learned]
created: 2026-04-11
verwant: "[[🚀 Docker Host Migration Plan LXC to VM]]"
---

# 🎓 Lessons Learned — NetBox naar Portainer Migratie

> Migratie van een bestaande Docker Compose setup naar een Portainer Stack. Portainer is strikter dan de CLI bij paden en volumes.

---

## 1. Absolute vs. relatieve paden

Portainer voert compose bestanden uit vanuit een eigen interne directory — relatieve paden werken daarin niet.

| Component | Probleem | Oplossing |
|---|---|---|
| Environment files | Portainer vond `.env` bestanden niet | Variabelen inlinegemaakt in `environment:` blok |
| Config volume | `./configuration` mount mislukte | Absoluut pad gebruikt: `/data/dockerdata/netbox/netbox-docker/configuration` |

---

## 2. Volume name mismatch

Een veelvoorkomende val bij het verplaatsen van Compose projecten.

- **Probleem:** Originele install gebruikte project naam `netbox-docker` → volumes heetten `netbox-docker_netbox-postgres-data`. Nieuwe Portainer stack maakte lege volumes aan met naam `netbox_netbox-postgres-data`.
- **Oplossing:** Expliciete `name:` property in het `volumes:` blok om logische naam te koppelen aan de historische volume naam:

```yaml
volumes:
  netbox-postgres-data:
    name: netbox-docker_netbox-postgres-data
```

---

## 3. YAML anchor overerving conflict

- **Probleem:** `netbox-worker` en `netbox-housekeeping` erfden `ports:` van het `netbox: &netbox` anchor → "port is already allocated" fout.
- **Oplossing:** Expliciet overschrijven met een lege lijst:

```yaml
netbox-worker:
  ports: []
```

---

## 4. Port conflicten

| Conflict | Oorzaak | Oplossing |
|---|---|---|
| Externe port 9999 | Verouderd container hield port vast | Overgestapt naar port 9998, stale containers verwijderd |
| Geen port mapping | Originele YAML had geen `ports:` sectie | `9998:8080` mapping toegevoegd aan netbox service |

---

## Reproduceerbare checklist voor Portainer migraties

1. Stop oude project: `docker compose down`
2. Noteer exacte namen van bestaande volumes: `docker volume ls`
3. Maak nieuwe `docker-compose.yml` voor Portainer:
   - Embed alle secrets/variabelen in `environment:` blokken
   - Gebruik absolute paden voor host-gemounte volumes
4. Voeg `ports:` toe aan hoofdservice, `ports: []` aan worker/secondary services
5. Map volumes in `volumes:` blok met `name:` property naar historische namen
6. Deploy in Portainer, update proxy naar nieuwe port

## Verwant
- [[🚀 Docker Host Migration Plan LXC to VM]]
- [[Docker Installatie Guide Ubuntu]]
