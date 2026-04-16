---
title: "Ottolenghi Recipe Slot Machine - Build Log"
tags: [n8n, homelab, recepten, build-log]
created: 2025-04-11
workflow-id: zdaPxFeoLW55eucE
webhook: https://n8n.prtsr.nl/webhook/recipe-slot-machine
---

# 🎰 Ottolenghi Recipe Slot Machine — Build Log

> Ruwe build log van het gesprek waarin dit workflow is gebouwd. Voor de nette documentatie, zie [[Ottolenghi Recipe Slot Machine - Documentatie]].

## Originele prompt

> "I need you to create an n8n workflow that works like a 'recipe slot machine' - when triggered, it randomly generates a dinner recipe suitable for a cooking-enthusiast couple..."

## Gebouwde nodes

1. **Recipe Slot Machine Webhook** — GET trigger op `/recipe-slot-machine`
2. **Ottolenghi Style Randomizer** — JavaScript randomisatie logica (keukens, ingrediënten, kooktijden, Ottolenghi-elementen)
3. **GPT-4o Recipe Creator** — OpenAI node, temperature 0.85, max 2500 tokens
4. **Beautiful HTML Formatter** — JavaScript node die JSON omzet naar gestyled HTML
5. **Respond to Webhook** — Stuurt HTML terug naar browser

## Workflow ID
`zdaPxFeoLW55eucE`

## Status bij afsluiten
Workflow aangemaakt maar nog niet volledig geactiveerd. HTML formatter node was nog niet compleet bij het opslaan van dit log.
