---
title: "Lokale LLMs op Mac met LM Studio"
tags: [homelab, ai, llm, mac, apple-silicon, lm-studio]
created: 2025-04-11
bron: https://getdeploying.com
verwant: "[[Cloud GPU Prijsvergelijking]]"
---

# Lokale LLMs op Mac met LM Studio

> LM Studio + MLX maakt het mogelijk om LLMs lokaal te draaien op Apple Silicon zonder cloud API.

## Benodigdheden

- Mac met Apple Silicon (M1 of nieuwer)
- **8GB RAM** → kleine modellen (3B) of sterk gequantiseerde 7B
- **16GB RAM** → comfortabel 7B/8B modellen
- **32GB+ RAM** → 13B tot 34B modellen

Jouw M5 Pro met 48GB is ideaal voor modellen tot ~34B.

## Wat is MLX?

Apple's open-source ML framework, specifiek voor Apple Silicon. Gebruikt unified memory zodat CPU en GPU data delen zonder kopiëren — resulteert in snelle, efficiënte inferentie.

## Setup (kort)

1. Download [LM Studio](https://lmstudio.ai/) → Apple Silicon build
2. Zoek een model (bijv. `Qwen3 4B` of `Gemma 3 27B`)
3. Kies **4-bit quantisatie** (`Q4_K_M`) voor goede balans
4. Chat → laad model → zet **Hardware Acceleration** op **MLX**

## Aanbevolen startmodellen

| Model | RAM | Gebruik |
|---|---|---|
| `qwen3-4b-thinking` | 8GB+ | Snel, goed voor chat & code |
| `gemma-3-27b` | 32GB+ | Krachtig, langere context |
| `qwen3-coder:30b` (Ollama) | 32GB+ | Jouw huidige voorkeur voor code |

## Lokaal vs. Cloud

| | Lokaal | Cloud |
|---|---|---|
| Kosten | Gratis | Pay-per-token |
| Privacy | Volledig privé | Data naar derde partij |
| Snelheid | Afhankelijk van GPU | Snel, dedicated hardware |
| Modellen | Beperkt door RAM | Alle SOTA modellen |

**Workflow:** Lokaal prototypen → cloud voor productie.

## Verwant
- [[Cloud GPU Prijsvergelijking]]
- [[n8n mcp homelab]]
