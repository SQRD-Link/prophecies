---
title: "Ottolenghi Recipe Slot Machine - Documentatie"
tags: [n8n, homelab, recepten, documentatie]
created: 2025-04-11
workflow-id: zdaPxFeoLW55eucE
webhook: https://n8n.prtsr.nl/webhook/recipe-slot-machine
---

# 🎰 Ottolenghi Recipe Slot Machine — Documentatie

> Zie ook: [[Ottolenghi Recipe Slot Machine - Build Log]]

## Webhook URLs

| Modus | URL |
|---|---|
| Standaard | `https://n8n.prtsr.nl/webhook/recipe-slot-machine` |
| Snel (30-40 min) | `https://n8n.prtsr.nl/webhook/recipe-slot-machine?quick=true` |
| Vegetarisch | `https://n8n.prtsr.nl/webhook/recipe-slot-machine?vegetarian=true` |
| Show-stopper | `https://n8n.prtsr.nl/webhook/recipe-slot-machine?showstopper=true` |

## Hoe het werkt

1. Gebruiker triggert webhook (browser of mobiel)
2. **Randomizer** kiest stijl, ingrediënt, kooktijd en Ottolenghi-elementen
3. **GPT-4o** genereert een volledig recept als JSON
4. **HTML Formatter** maakt er een mooi opgemaakt recept van
5. Browser toont het resultaat direct

## Randomisatie logica

**Keukens (even verdeeld):** Middle Eastern, Mediterranean, North African, Persian, Turkish, Israeli, Levantine

**Kooktijden (gewogen):**
- 40% Snel (30-40 min)
- 40% Weekend (60-90 min)
- 20% Show-stopper (90+ min)

**Voedingspatroon (gewogen):**
- 50% Vegetarisch/Vegan
- 20% Vis/Zeevruchten
- 20% Kip
- 10% Lam/Rund

## Onderhoud

- **Activeren:** n8n → workflow zoeken → toggle aanzetten
- **Testen:** `curl https://n8n.prtsr.nl/webhook/recipe-slot-machine`
- **OpenAI credentials** nodig: `Settings → Credentials` in n8n
