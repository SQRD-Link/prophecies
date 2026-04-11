---
title: "Opdracht 01 — Arr Stack Monitoring"
tags: [homelab, n8n, opdracht, monitoring]
created: 2026-04-06
updated: 2026-04-07
---

# Opdracht 01 — Arr Stack Monitoring

## Doel
Bouw een n8n workflow die periodiek je arr stack en Plex controleert en je via Telegram waarschuwt als een service down is of interne problemen heeft.

Als je deze opdracht afgerond hebt, heb je de basis gelegd voor een AI sysadmin die later automatisch kan ingrijpen.

---

## Wat je leert
- Schedule Trigger gebruiken
- HTTP Request node configureren
- IF node gebruiken voor conditionele logica
- Merge node gebruiken om meerdere checks samen te voegen
- Telegram notificaties versturen
- Error handling in n8n

---

## Services om te monitoren

| Service | URL | Endpoint |
|---|---|---|
| Seerr | `https://verzoekjes.sqrd.link` | `/api/v1/status` |
| Sonarr | `https://sonarr.sqrd.link` | `/api/v3/health` (API key vereist) |
| Radarr | `https://radarr.sqrd.link` | `/api/v3/health` (API key vereist) |
| Plex | `http://plex.sqrd.link:32400` | `/identity` |

### Waarom de health endpoint?

`/api/v3/health` geeft je meer dan alleen "is de container up" — het vertelt je ook of er interne problemen zijn zoals:
- Indexers die niet bereikbaar zijn
- Download client problemen
- Schijfruimte waarschuwingen

Mogelijke responses:

| Situatie | Response |
|---|---|
| Alles ok | `[]` — lege array |
| Interne problemen | `[{"source": "...", "type": "warning", "message": "..."}]` |
| Auth mislukt | `401 Unauthorized` |
| Container down | Connection error / timeout |

**Let op:** Een lege array `[]` is een succesvolle response — alles is healthy. n8n toont dan "No output data returned" wat verwarrend kan zijn. Zet **"Always Output Data"** aan in de Settings tab om altijd output te zien.

---

## Authenticatie voor Sonarr en Radarr

Sonarr en Radarr gebruiken een eigen API key header — **niet** de Authentication dropdown in n8n.

Configureer de HTTP Request node zo:
- Authentication: `None`
- Send Headers: `aan`
- Header naam: `X-Api-Key`
- Header waarde: jouw API key

API keys vind je onder `Settings → General → API Key` in elke service.

---

## Opdracht stappen

### Stap 1 — Schedule Trigger
Maak een nieuwe workflow aan in n8n met een **Schedule Trigger**.
- Interval: elke 5 minuten

---

### Stap 2 — HTTP checks
Voeg voor elke service een **HTTP Request** node toe.

Configureer elke node:
- Method: `GET`
- URL: de health endpoint van de service
- Voor Sonarr en Radarr: voeg header toe `X-Api-Key: jouw_api_key`
- Zet **"Continue On Fail"** aan in de Settings tab
- Zet **"Always Output Data"** aan in de Settings tab

**Leervraag:** Waarom zijn "Continue On Fail" en "Always Output Data" hier beide belangrijk?

---

### Stap 3 — Status bepalen per service
Voeg na elke HTTP Request een **IF** node toe.

Je hebt nu twee scenario's om op te checken:

**Scenario 1 — Container is down:**
De HTTP Request faalt met een connection error. Check:
- `{{ $json.error }}` bestaat → service is down

**Scenario 2 — Container is up maar heeft problemen:**
De health endpoint geeft items terug in de array. Check:
- `{{ $json.length }}` groter dan `0` → er zijn problemen

Combineer beide in je IF node met een OR conditie.

**Leervraag:** Wat is het verschil tussen een connection error en een lege array response?

---

### Stap 4 — Samenvoegen
Gebruik een **Merge** node om alle "down of ongezond" outputs samen te voegen.

- Mode: `Combine` → `Append`
- Verbind alle `false` outputs van je IF nodes met deze Merge node

---

### Stap 5 — Telegram notificatie
Voeg een **Telegram** node toe na de Merge node.

- Operation: `Send Message`
- Message voorbeeld:

```
🚨 Service alert!

Service: {{ $json.service }}
Status: {{ $json.message }}

Tijdstip: {{ $now.toISO() }}
```

---

### Stap 6 — Testen
1. Stop een container tijdelijk → alert moet binnenkomen
2. Laat een indexer falen in Sonarr → health alert moet binnenkomen
3. Start de container weer → volgende run geen alert

---

## Uitbreidingen

- [ ] Voeg een **cooldown** toe zodat je niet elke 5 minuten dezelfde alert krijgt
- [ ] Stuur ook een "service is weer up" notificatie
- [ ] Gebruik **Static Data** om vorige status te onthouden
- [ ] Differentieer tussen `warning` en `error` in de health response en stuur verschillende alerts

---

## Hints bij problemen

**"No output data returned":**
Zet "Always Output Data" aan in de Settings tab — een lege array `[]` betekent dat alles healthy is.

**HTTP Request faalt meteen:**
Check of de service bereikbaar is vanuit je n8n container op `docker.sqrd.link`.

**Telegram stuurt geen bericht:**
Stuur `/start` naar je bot en check je chat ID via [@userinfobot](https://t.me/userinfobot).

**Merge node doet niks:**
Correct gedrag als alles up is — geen input betekent geen output.

---

## Referentie

- [n8n HTTP Request node docs](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.httprequest/)
- [n8n Telegram node docs](https://docs.n8n.io/integrations/builtin/app-nodes/n8n-nodes-base.telegram/)
- [n8n Schedule Trigger docs](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.scheduletrigger/)
- [Sonarr API docs](https://sonarr.tv/docs/api/)
- [Radarr API docs](https://radarr.video/docs/api/)
