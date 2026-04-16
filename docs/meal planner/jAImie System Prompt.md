---
title: "jAImie — System Prompt"
tags: [n8n, jaimie, meal-planner, system-prompt, ai]
created: 2026-04-11
verwant: "[[meal planner/WEEKLY_MENU_SETUP_GUIDE]]"
---

# jAImie — System Prompt

> De n8n expression die het systeem prompt voor J-AI-mie genereert. Wordt gebruikt in de meal planner workflow.
> Zie [[meal planner/WEEKLY_MENU_SETUP_GUIDE]] voor de volledige setup.

## Gezinsprofiel
- 2 volwassenen (vader: gewichtsverlies / hoog eiwit), 2 kinderen (3 en 7 jaar)
- Strikt lactosevrij (plantaardige alternatieven van Picnic NL)
- Geen selderij, kool tot minimum beperken
- Apparatuur: Nicer Dicer & keukenmachine

## Maaltijdregels
- 7 recepten (maandag t/m zondag)
- Minimaal 3 snelle maaltijden (≤ 25 min)
- Overige max 40 min
- 1 cheatmaaltijd (weekend, max 800 kcal)
- 1 traditioneel AVG-maaltijd toegestaan (geen hutspot)
- Dure ingrediënten combineren over meerdere recepten

## Voedingswaarden
- Standaard: 350–500 kcal per portie, minimaal 25g eiwit
- Cheatmaaltijd: max 800 kcal, wel lactosevrij

## n8n Expression (volledig)

```javascript
{{ (function() { 
  const previousMeals = $input.first().json.record && $input.first().json.record.meals 
    ? $input.first().json.record.meals : []; 
  const mealNames = previousMeals.slice(-21).map(m => m.name).join(', '); 
  
  return `You are J-AI-mie, a professional meal planner...
  
  HISTORY CONSTRAINT:
  DO NOT repeat these recent meals: ${mealNames}`;
})() }}
```

> ⚠️ Volledige prompt zit in de n8n workflow zelf. Deze note bevat de samenvatting + context.

## Output formaat
JSON met: `weekOf`, `budgetStrategy`, `recipes[]` (incl. nutrition, kidFriendlyRating, instructions in Jamie Oliver stijl), `weekSummary`
