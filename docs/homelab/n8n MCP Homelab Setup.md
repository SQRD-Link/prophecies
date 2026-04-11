---
title: "n8n MCP Homelab Setup"
tags: [homelab, n8n, mcp, obsidian, docker, portainer]
created: 2026-04-11
verwant: "[[Lokale LLMs op Mac met LM Studio]]"
---

# n8n MCP Homelab Setup

> Hoe je een MCP (Model Context Protocol) server in Portainer draait en n8n workflows laat communiceren met je Obsidian vault.

## Vereisten
- Docker + Portainer
- Obsidian met **Local REST API** plugin
- n8n (Docker)

---

## Stap 1 — Obsidian Local REST API plugin

1. Obsidian → Settings → Community Plugins → zoek "Local REST API"
2. Installeer en activeer
3. Kopieer je **API Key** uit de plugin settings

---

## Stap 2 — MCP Obsidian server in Portainer

```yaml
version: '3.8'
services:
  mcp-obsidian:
    image: ghcr.io/kmackett/mcp-obsidian-docker:latest
    container_name: mcp-obsidian
    environment:
      - OBSIDIAN_API_KEY=jouw_obsidian_api_key_hier
    ports:
      - "27124:27124"
    volumes:
      - ./logs:/app/logs
    restart: unless-stopped
    networks:
      - homelab

networks:
  homelab:
    external: true
```

Portainer → Stacks → Add Stack → naam `mcp-obsidian` → plak YAML → vervang API key → Deploy.

---

## Stap 3 — n8n MCP Client node installeren

```
Settings → Community Nodes → installeer: @nerding-io/n8n-nodes-mcp
```

Voeg toe aan n8n Docker Compose environment:
```yaml
N8N_COMMUNITY_PACKAGES_ALLOW_TOOL_USAGE=true
```

---

## Stap 4 — Credential instellen in n8n

**HTTP Streamable (aanbevolen):**
- Type: MCP Client (HTTP Streamable) API
- URL: `http://mcp-obsidian:27124/stream`

---

## Beschikbare Obsidian MCP tools

| Tool | Functie |
|---|---|
| `create_note` | Nieuwe notitie aanmaken |
| `update_note` | Bestaande notitie bijwerken |
| `delete_note` | Notitie verwijderen |
| `search_notes` | Zoeken in vault |
| `list_notes` | Alle notities weergeven |
| `get_note` | Specifieke notitie ophalen |

---

## Meerdere MCP servers combineren

```yaml
services:
  mcp-obsidian:
    image: ghcr.io/kmackett/mcp-obsidian-docker:latest
    environment:
      - OBSIDIAN_API_KEY=jouw_obsidian_key
    ports:
      - "27124:27124"

  mcp-brave-search:
    image: mcp/brave-search:latest
    environment:
      - BRAVE_API_KEY=jouw_brave_key
    ports:
      - "27125:27124"
```

---

## Troubleshooting

| Probleem | Oplossing |
|---|---|
| Authentication Failed | Controleer API key in Obsidian én environment variabele |
| Connection Refused | Zorg dat Obsidian draait en port 27124 open is |
| MCP node werkt niet als tool | Zet `N8N_COMMUNITY_PACKAGES_ALLOW_TOOL_USAGE=true` |

```bash
docker logs mcp-obsidian   # MCP server logs
docker logs n8n            # n8n logs
```

## Verwant
- [[Opdracht 02 — Telegram Inbox Bot]]
- [[Lokale LLMs op Mac met LM Studio]]
