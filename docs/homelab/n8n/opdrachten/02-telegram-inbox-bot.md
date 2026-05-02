---
title: "Opdracht 02 — Telegram Inbox Bot"
tags: [homelab, n8n, opdracht, telegram, obsidian, second-brain]
created: 2026-04-11
updated: 2026-04-16
verwant: "[[inbox]]"
---

# Opdracht 02 — Telegram Inbox Bot

## Doel
Bouw een n8n workflow die berichten, links en ideeën die je naar een Telegram bot stuurt automatisch toevoegt aan je Obsidian inbox (`docs/inbox.md`). Zo vervang je WhatsApp als dump-kanaal door iets dat wél aansluit op je second brain.

Als je deze opdracht afgerond hebt, heb je een frictionloze capture-workflow die direct in je vault belandt — zonder dat je een app hoeft te openen.

---

## Context — je second brain structuur

Je vault gebruikt het **PARA-systeem** (zie [[PARA/00 - PARA Index]]):

| Map | Wat erin gaat |
|---|---|
| `docs/inbox.md` | Alles wat nog verwerkt moet worden — hier dump je naartoe |
| `docs/Home/PARA/Projects/` | Actieve projecten met een eindpunt |
| `docs/Home/PARA/Areas/` | Doorlopende verantwoordelijkheden |
| `docs/Home/PARA/Resources/` | Referentiemateriaal per onderwerp |
| `docs/Home/PARA/Archive/` | Afgerond of inactief |

De inbox is bewust de enige dump-plek. Wekelijks verwerk je de inbox naar de juiste PARA-map. Deze bot voedt de inbox — verwerken doe je zelf (of later automatisch met een AI-workflow).

### Inbox formaat

Entries in de inbox volgen dit formaat:
```
- YYYY-MM-DD — [beschrijving of emoji-prefix] inhoud of URL
```

Emoji-prefixen die al gebruikt worden:
- `💡 **Idee:**` — losse ideeën
- `🎥 **Video:**` — YouTube/Instagram video's
- `📖 **Reddit:**` — Reddit links
- `🛠️ **GitHub:**` — GitHub repos

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

Doel: de binnenkomende tekst omzetten naar een nette inbox-regel met datum én automatische prefix op basis van inhoud.

```javascript
const message = $input.first().json.message;
const text = message.text || '';
const date = new Date().toISOString().split('T')[0]; // YYYY-MM-DD

// Detecteer of het een URL is
const urlRegex = /https?:\/\/[^\s]+/g;
const urls = text.match(urlRegex) || [];
const cleanText = text.replace(urlRegex, '').trim();

// Detecteer type op basis van URL of tekst
function detectPrefix(url, text) {
  if (!url) return '💡 **Idee:**';
  if (url.includes('youtube.com') || url.includes('youtu.be') || url.includes('instagram.com/reel')) return '🎥 **Video:**';
  if (url.includes('reddit.com')) return '📖 **Reddit:**';
  if (url.includes('github.com')) return '🛠️ **GitHub:**';
  return ''; // gewone link, geen prefix
}

let entry;
const prefix = detectPrefix(urls[0], cleanText);

if (urls.length > 0 && cleanText === '') {
  entry = `- ${date} — ${prefix ? prefix + ' ' : ''}${urls[0]}`;
} else if (urls.length > 0) {
  entry = `- ${date} — [${cleanText}](${urls[0]})`;
} else {
  entry = `- ${date} — 💡 **Idee:** ${text}`;
}

return [{ json: { entry, originalText: text } }];
```

**Leervraag:** Waarom gebruiken we `.split('T')[0]` om de datum te krijgen?

---

## Stap 5 — Obsidian MCP node: toevoegen aan inbox

Voeg een **n8n MCP Client** node toe. Zie [[n8n MCP Homelab Setup]] voor de volledige MCP setup.

- Tool: `obsidian_update_note`
- Parameters:
  - `targetType`: `filePath`
  - `targetIdentifier`: `docs/inbox.md`
  - `modificationType`: `wholeFile`
  - `wholeFileMode`: `append`
  - `content`: `\n{{ $json.entry }}`

De entry wordt onder de bestaande `## 📌 Te verwerken` sectie geplakt (append voegt toe aan het einde van het bestand).

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
2. Stuur een YouTube link → krijgt automatisch `🎥 **Video:**` prefix
3. Stuur een GitHub link → krijgt automatisch `🛠️ **GitHub:**` prefix
4. Stuur alleen tekst → verschijnt als `💡 **Idee:**` entry
5. Open Obsidian → verifieer dat de entries correct zijn toegevoegd onder `## 📌 Te verwerken`

---

## Uitbreidingen

- [ ] **Commando's:** `/idee`, `/video`, `/artikel` als Telegram commando's → forceert een specifieke prefix
- [ ] **Instagram reels:** Stuur een reel-link met een korte beschrijving → wordt direct naar [[PARA/Resources/Self-Growth & Parenting]] geschreven (overslaat inbox)
- [ ] **Herinnering:** Elke zondag om 10:00 een Telegram bericht als de inbox meer dan 5 entries heeft
- [ ] **AI-categorisatie:** Pipe de entry door Claude om automatisch de juiste PARA Resource map te detecteren en er direct naartoe te schrijven

---

## Hints bij problemen

**Telegram Trigger ontvangt niks:**
Zorg dat de workflow actief is (toggle aan) en dat je bot een bericht hebt gestuurd ná activatie.

**Chat ID klopt niet:**
Haal het op via `https://api.telegram.org/bot<TOKEN>/getUpdates` na een bericht te hebben gestuurd.

**Obsidian note wordt niet bijgewerkt:**
Check of je MCP server draait en of het pad `docs/inbox.md` klopt (vault-relatief).

**Entry staat op verkeerde plek in inbox:**
`append` plakt altijd onderaan het bestand. Als je wil dat entries onder een specifieke sectie komen, is een meer geavanceerde aanpak nodig (Code node die de bestaande content leest, parseert en injecteert).

---

## Referentie

- [n8n Telegram Trigger docs](https://docs.n8n.io/integrations/builtin/trigger-nodes/n8n-nodes-base.telegramtrigger/)
- [n8n Telegram node docs](https://docs.n8n.io/integrations/builtin/app-nodes/n8n-nodes-base.telegram/)
- [BotFather](https://t.me/BotFather)
- [[inbox]] — de note waar alles naartoe gaat
- [[PARA/00 - PARA Index]] — je second brain structuur
- [[n8n MCP Homelab Setup]] — hoe de Obsidian MCP connectie werkt
- [[Opdracht 01 — Arr Stack Monitoring]] — vorige opdracht
