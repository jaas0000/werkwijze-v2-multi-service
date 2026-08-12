---
name: frontend-bouwen
description: >-
  Bouwt een UI voor een feature die daarom vraagt — niet elke feature heeft een scherm nodig,
  dit is dus geen verplichte stap zoals `feature-bouwen`. Kernidee: de mockup en de
  implementatie zijn dezelfde component, nooit een los ontwerpartefact dat kan verouderen.
  Fase 1 bouwt de component met nepdata op basis van de al gegenereerde TypeScript-types (ter
  visuele validatie, goedkoop te herzien); fase 2 vervangt de nepdata door een echte API-call in
  diezelfde component, plus een verplichte Playwright-E2E-test. Gebruik deze skill bij "bouw een
  scherm voor X", "maak een UI voor deze feature", "laat me een mockup zien", of wanneer een
  story een UI-acceptatiecriterium bevat. Vereist dat `feature-bouwen` regel 1-6 (schema, keten,
  logica, tests) al klaar zijn voor de onderliggende feature — dit vervangt `feature-bouwen`
  niet, het schuift ertussen (vóór regel 9, de gezamenlijke aflevering) als een frontend nodig
  is.
---

# Frontend bouwen — mockup en implementatie zijn dezelfde component

**Trigger:** een story vraagt een UI/scherm voor een feature, en `feature-bouwen` regel 1-6 zijn
al klaar (schema, keten, logica, tests). Vraagt de feature geen UI: sla deze skill over.

## Regels

1. **Kies de frontend, werk vanuit de gegenereerde types.** Heeft het project meer dan één
   frontend-app, dan zegt `stack-profiel.md` §Frontend(s) welke bij deze feature hoort — dat is
   geen keuze om zelf te maken. Importeer daarna uit de gegenereerde types van die frontend
   (`<frontend>/generated/`) — nooit een eigen, met de hand getypeerd datamodel voor de UI
   verzinnen. Bestaat de generatieketen nog niet voor deze feature: draai eerst
   `feature-bouwen` regel 4.

   Praat de UI met meer dan één service, dan is elk van die contracten apart gegenereerd
   (ADR-0002): één gegenereerd bestand per service, geen handmatig samengevoegd type ertussen.

2. **Fase 1 — nepdata (de "mockup").** Bouw de component met een hardgecodeerde instantie van
   het gegenereerde type. Geen live fetch. Dit is opzettelijk goedkoop: geen backend-aanroep
   nodig, snel te herzien, en het is dezelfde component die je in fase 2 hergebruikt — geen los
   mockup-bestand of extern ontwerptool dat kan gaan afwijken van de uiteindelijke
   implementatie.

3. **Laat fase 1 zien vóór je doorgaat naar fase 2.** Dit is het moment waarop de mens de UI
   goedkeurt — vergelijkbaar met een `story-review`-checkpoint, maar visueel in plaats van
   tekstueel. Commit fase 1 apart, zodat de git-geschiedenis toont hoe de component evolueerde
   in plaats van dat de mockup ergens anders (Figma, een los bestand) heeft gestaan. Dit is nog
   geen aflevering in de zin van `feature-bouwen` regel 9 (geen `/simplify`, geen Simplify-regel
   nodig op dit tussenpunt) — dat gebeurt pas één keer, aan het eind, over de volledige
   wijziging.

4. **Fase 2 — vervang de nepdata door een echte aanroep.** Zelfde component, zelfde bestand:
   vervang de hardgecodeerde instantie door een `fetch`/API-call naar het bestaande endpoint.
   Geen herbouw vanaf nul — alleen de databron verandert.

5. **Geen apart design system optuigen vooraf.** Herbruikbare stijl/componenten (kleuren,
   knoppen, lay-outpatronen) pas extraheren zodra een tweede scherm hetzelfde patroon nodig
   heeft — duplicatie is pas een probleem ná de tweede onafhankelijke implementatie, net als
   `feature-bouwen` regel 8.

6. **Playwright-E2E-test, niet optioneel.**
   - **6a. Wanneer.** Bouw je een nieuwe of gewijzigde UI (d.w.z. je gebruikt `frontend-bouwen`
     sowieso al, zie de Trigger), dan hoort er een test bij in
     `<frontend>/tests/e2e/<naam>.spec.ts` die de UI echt in een browser bedient
     (`@playwright/test`) — niet een ad-hoc scriptje tijdens het bouwen dat na afloop wordt
     weggegooid (zie §Bekende valkuilen). Geen frontend-wijziging in deze PR: dan is deze regel
     niet van toepassing, net als de rest van deze skill.
   - **6b. Wat minimaal.** Het gelukkige pad (actie uitvoeren, resultaat zien zonder
     page-reload) en één foutpad (bv. een 409 van de server die als zichtbare foutmelding
     verschijnt, niet stil faalt). Draai de test lokaal (`npm run test:e2e`, met de dev-server en
     elke service die de UI aanroept al draaiend) vóórdat je aflevert. Twee onafhankelijke
     checks vangen dit daarna nog een keer op, geen van beide is zelfrapportage: CI
     (`check-frontend-e2e-coverage` in `.github/workflows/ci.yml`) faalt als de bron van een
     frontend wijzigt zonder een bijbehorende wijziging in diens `tests/e2e/`, en `code-review`
     regel 1 controleert het los daarvan nog een keer bij het lezen van de diff.

7. **Simplify en aflevering gebeuren niet hier.** Ná deze skill loopt `feature-bouwen` regel 9
   verder — één `/simplify`-ronde en één Simplify-regel in het commit-/PR-bericht voor de hele
   wijziging (backend + frontend samen), niet apart per fase.

## Bekende valkuilen

- **Een handmatig testscript dat tijdens het bouwen wordt gedraaid en daarna weggegooid, voelt
  als verificatie maar laat geen herhaalbaar spoor achter.** Het werkt op het moment zelf, maar
  een latere wijziging aan diezelfde UI heeft niets om tegen te testen. Vandaar regel 6: de test
  hoort in de repo en in CI, niet als eenmalig scriptje ernaast.

## Wat dit niet oplost

- **Design system / herbruikbare component-bibliotheek** — pas relevant bij een tweede scherm
  (zie regel 5).
- **Mockup vóórdat er een schema is** — deze skill gaat uit van een al bestaande
  schemabeslissing en gegenereerde keten (`feature-bouwen` regel 1-6 eerst). Een mockup die de
  schemabeslissing zelf moet informeren, is een ander (nog niet uitgewerkt) scenario.
