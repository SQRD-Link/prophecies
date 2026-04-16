---
title: "DNS Crash Course"
tags: [homelab, netwerk, dns, kennis, referentie]
created: 2026-04-11
bron: https://bytebytego.com
verwant: "[[Email Deliverability/Email Deliverability - Teacher Deep Dive]]"
---

# DNS — Crash Course

> Uitgebreid overzicht van hoe DNS werkt. Relevant voor hosting, email deliverability en homelab netwerkconfiguratie.

## Wat is DNS?

DNS (Domain Name System) is het adresboek van het internet — het vertaalt domeinnamen (google.com) naar IP-adressen (172.217.16.206). Zonder DNS zou je elk IP-adres uit je hoofd moeten kennen.

## Belangrijkste functies

- **Host-to-IP mapping** — domeinnaam → IP-adres (A-record)
- **Host aliasing** — meerdere namen → één IP (CNAME-record)
- **Email routing** — welke mailserver ontvangt mail (MX-record)
- **Reverse lookup** — IP-adres → domeinnaam (PTR-record)
- **Load balancing** — meerdere IP's teruggeven voor hetzelfde domein

## DNS Hiërarchie

```
Root DNS servers (13 logisch, ~1000 fysiek, beheerd door ICANN)
    ↓
TLD servers (.com, .nl, .org, .edu)
    ↓
Authoritative DNS servers (de echte bron voor jouw domein)
    ↓
Recursive resolver (doet het zoekwerk namens jouw apparaat)
    ↓
Jouw apparaat (lokale cache)
```

## Record types

| Type | Gebruik |
|---|---|
| A | Domeinnaam → IPv4 adres |
| AAAA | Domeinnaam → IPv6 adres |
| CNAME | Alias → canonieke naam |
| MX | Mailserver voor domein |
| NS | Welke nameservers zijn authoritative |
| TXT | Vrije tekst (o.a. SPF, DKIM) |
| PTR | Reverse lookup (IP → naam) |

## Resolutie: iteratief vs. recursief

**Iteratief:** de resolver stuurt de client door naar de volgende server (referrals).
**Recursief:** de resolver doet zelf alle queries en geeft het eindantwoord terug. Meest gebruikt in de praktijk.

## Caching & TTL

DNS-records worden gecached op: lokaal apparaat, recursive resolver, authoritative server. De **TTL** (Time To Live) bepaalt hoe lang een record gecached mag worden. Na een DNS-wijziging duurt propagatie typisch 24-48 uur (max 72).

## Security

| Protocol | Wat het doet |
|---|---|
| DNSSEC | Digitale handtekeningen op DNS-records (integriteit) |
| DoH (DNS over HTTPS) | Versleutelt DNS-queries via HTTPS |
| DoT (DNS over TLS) | Versleutelt DNS-queries via TLS |

### Bedreigingen
- **Cache poisoning / DNS Spoofing** — valse records in cache injecteren
- **DDoS** — DNS-servers overbelasten
- **DNS Hijacking** — controle overnemen van domein DNS-instellingen
- **MitM** — DNS-verkeer onderscheppen

## Verwant
- [[Email Deliverability/Email Deliverability - Teacher Deep Dive]] — SPF/DKIM/DMARC zijn DNS-records
- [[homelab/n8n MCP Homelab Setup]]
