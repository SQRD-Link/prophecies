---
title: "Opdracht 02 — Telegram Inbox Bot"
tags: [homelab, n8n, opdracht, telegram, obsidian, second-brain]
created: 2026-04-11
updated: 2026-04-11
verwant: "[[inbox]]"
---

# Opdracht 02 — Telegram Inbox Bot

## Doel
Bouw een n8n workflow die berichten, links en ideeën die je naar een Telegram bot stuurt automatisch toevoegt aan je Obsidian inbox (`docs/inbox.md`). Zo vervang je WhatsApp als dump-kanaal door iets dat wél aansluit op je second brain.

Als je deze opdracht afgerond hebt, heb je een frictionloze capture-workflow die direct in je vault belandt — zonder dat je een app hoeft te openen.

---

## Wat je leert
- Telegram Trigger node gebruiken
- Tekst en URLs parsen in een Code node
- Obsidian MCP gebruiken vanuit n8n om notes te schrijven
- Datum/tijd formatting in n8n expressions
- Een webhook-loze, event-driven workflow bouwen

---

## Architectuur

```
Telegram Bot (jij stuurt bericht)
    ↓
n8n Telegram Trigger
    ↓
Code node (bericht opmaken als inbox-entry)
    ↓
Obsidian MCP node (append naar docs/inbox.md)
    ↓
Telegram antwoord: "✅ Toegevoegd aan inbox"
```

---

## Stap 1 — Telegram Bot aanmaken

1. Open Telegram en zoek **@BotFather**
2. Stuur `/newbot` en volg de stappen
3. Geef de bot een naam, bijv. `Richard's Brain` en username bijv. `sqrd_brain_bot`
4. Kopieer het **API token** dat je krijgt
5. Stuur je bot een eerste bericht (dit is nodig om een chat ID te krijgen)
6. Haal je chat ID op via: `https://api.telegram.org/bot<TOKEN>/getUpdates`

---

## Stap 2 — Telegram credential in n8n

1. Ga naar `Settings → Credentials → New`
2. Kies **Telegram API**
3. Plak je bot token
4. Sla op

---

## Stap 3 — Telegram Trigger node

Maak een nieuwe workflow aan met een **Telegram Trigger** node.

- Credential: jouw bot
- Updates: `message`

**Leervraag:** Wat is het verschil tussen een Telegram Trigger en een gewone Webhook node voor Telegram?

---

## Stap 4 — Code node: inbox entry opmaken

Voeg een **Code node** toe na de Trigger.

Doel: de binnenkomende tekst omzetten naar een nette inbox-regel met datum.

```javascript
const message = $input.first().json.message;
const text = message.text || '';
const date = new Date().toISOString().split('T')[0]; // YYYY-MM-DD

// Detecteer of het een URL is
const urlRegex = /https?:\/\/[^\s]+/g;
const urls = text.match(urlRegex) || [];
const cleanText = text.replace(urlRegex, '').trim();

let entry;
if (urls.length > 0 && cleanText === '') {
  // Alleen een link
  entry = `- ${date} — ${urls[0]}`;
} else if (urls.length > 0) {
  // Tekst met link
  entry = `- ${date} — [${cleanText}](${urls[0]})`;
} else {
  // Alleen tekst
  entry = `- ${date} — ${text}`;
}

return [{ json: { entry, originalText: text } }];
```

**Leervraag:** Waarom gebruiken we `.split('T')[0]` om de datum te krijgen?

---

## Stap 5 — Obsidian MCP node: toevoegen aan inbox

Voeg een **n8n MCP Client** node toe (of gebruik een HTTP Request naar je lokale Obsidian MCP server als je die hebt draaien).

Als je de Obsidian MCP server lokaal draait via `mcp-obsidian`:

- Method: `POST`  
- URL: jouw lokale MCP endpoint
- Tool: `obsidian_update_note`
- Parameters:
  - `targetType`: `filePath`
  - `targetIdentifier`: `docs/inbox.md`
  - `modificationType`: `wholeFile`
  - `wholeFileMode`: `append`
  - `content`: `\n{{ $json.entry }}`

**Leervraag:** Waarom `append` en niet `overwrite`?

---

## Stap 6 — Bevestiging terugsturen

Voeg een **Telegram node** toe na de MCP node.

- Operation: `Send Message`
- Chat ID: `{{ $('Telegram Trigger').item.json.message.chat.id }}`
- Text:
```
✅ Toegevoegd aan inbox!

{{ $('Code').item.json.entry }}
```

---

## Stap 7 — Testen

1. Stuur een losse link naar je bot → verschijnt als entry in inbox.md
2. Stuur een tekst + link → verschijnt met tekst als link label
3. Stuur alleen tekst → verschijnt als gewone tekst entry
4. Open Obsidian → verifieer dat de entries correct zijn toegevoegd

---

## Uitbreidingen

- [ ] **Tags herkennen:** Als je `#homelab` meestuurt, voeg je de tag automatisch toe aan de entry
- [ ] **Categorieën:** `/idee`, `/artikel`, `/video` als commando's → andere prefix in inbox
- [ ] **Herinnering:** Elke zondag om 10:00 een Telegram bericht als de inbox meer dan 5 entries heeft
- [ ] **Verwerkt markeren:** Stuur `/done` om de oudste entry te markeren als verwerkt

---

## Hints bij problemen

**Telegram Trigger ontvangt niks:**
Zorg dat de workflow actief is (toggle aan) en dat je bot een bericht hebt gestuurd ná activatie.

**Chat ID klopt niet:**
Haal het op via `https://api.telegram.org/bot<TOKEN>/getUpdates` na een bericht te hebben gestuurd.

**Obsidian note wordt niet bijgewerkt:**
Check of je MCP server draait en of het pad `docs/inbox.md` klopt (vault-relatief).

---

## Referentie

- [n8n Telegram Trigger docs](https://docs.n8n.io/integrations/builtin/trigger-nodes/n8n-nodes-base.telegramtrigger/)
- [n8n Telegram node docs](https://docs.n8n.io/integrations/builtin/app-nodes/n8n-nodes-base.telegram/)
- [BotFather](https://t.me/BotFather)
- [[inbox]] — de note waar alles naartoe gaat
- [[Opdracht 01 — Arr Stack Monitoring]] — vorige opdracht
