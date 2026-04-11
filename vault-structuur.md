---
title: "Vault Structuur"
tags: [meta, vault]
created: 2026-04-11
updated: 2026-04-11
---

# 🧠 Prophecies — Vault Structuur

Dit is je personal second brain. De vault is opgezet volgens een simpel principe: **makkelijk in, makkelijk terug te vinden.**

---

## Structuur

```
docs/
├── inbox.md                  ← 📥 Dump alles wat nog verwerkt moet worden
│
├── homelab/                  ← 🖥️ Alles over je homelab
│   ├── ai/                   ← LLMs, GPU's, AI tools lokaal draaien
│   ├── n8n/                  ← n8n workflows en documentatie
│   │   └── opdrachten/       ← Leer-opdrachten voor n8n (01, 02, ...)
│   ├── Docker Installatie Guide Ubuntu.md
│   ├── Docker Host Migratie Plan LXC naar VM.md
│   ├── Lessons Learned - NetBox naar Portainer Migratie.md
│   ├── n8n MCP Homelab Setup.md
│   ├── Kubernetes Chainsaw Issues Referentie.md
│   └── Tuya Local - Eetkamer Lampen.md
│
├── kennis/                   ← 📚 Externe kennisartikelen & referentiemateriaal
│   └── DNS Crash Course.md
│
├── prive/                    ← 🏠 Persoonlijke documenten
│   └── huizenjacht/          ← Biedingsbrieven, bezichtigingen
│
├── werk/                     ← 💼 Werk-gerelateerde notes
│   └── Plesk Documentatie Links.md
│
├── Email Deliverability/     ← 📧 Email deliverability kennisbank (Cloud86)
├── meal planner/             ← 🍽️ jAImie meal planner (incl. system prompt)
├── antigravity/              ← ✏️ Antigravity IDE notities
├── copilot-custom-prompts/   ← 🤖 Copilot prompt templates
├── Markdown/                 ← 📝 Markdown referentie
│
├── Questy - Onboarding AI Agent Persona.md  ← AI agent concept
└── Home/                     ← 🏠 Dashboard & homepage (toekomstig project)
```

---

## Werkwijze

### Capture (dagelijks)
Stuur links, ideeën en notities naar je **Telegram inbox bot** → belandt automatisch in `inbox.md`. Of open Obsidian op mobiel en plak het direct in `inbox.md`.

### Verwerken (wekelijks)
Ga wekelijks door `inbox.md`. Per entry: verplaatsen naar de juiste map als echte note, of verwijderen als het niet meer relevant is.

### Schrijven (wanneer nodig)
Maak notes aan in de juiste map. Gebruik frontmatter met `tags`, `created` en `verwant` voor links tussen notes.

---

## Conventies

- **Frontmatter:** altijd `title`, `tags`, `created`
- **Links:** gebruik `[[note naam]]` om notes aan elkaar te koppelen
- **Taal:** Nederlands voor persoonlijke notes, Engels voor technische documentatie
- **Bestandsnamen:** Beschrijvend, geen underscores — gebruik spaties of koppeltekens

---

## Toekomstige projecten

- [ ] `docs/Home/` uitbouwen als Obsidian dashboard met MOC (Map of Content)
- [ ] Telegram inbox bot live krijgen → zie [[homelab/n8n/opdrachten/02-telegram-inbox-bot]]
- [ ] Weekly review routine instellen (vrijdag of zondag, 10 min)
- [ ] `kennis/` verder uitbouwen met eigen samenvattingen van artikelen
