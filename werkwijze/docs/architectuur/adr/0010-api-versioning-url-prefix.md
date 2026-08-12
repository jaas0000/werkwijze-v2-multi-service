# ADR-0010: API-versioning — URL-prefix-mechanisme

**Status:** geaccepteerd
**Datum:** 2026-08-12

## Context

Een service-contract evolueert. Zonder een expliciete versiestrategie breekt een
schemawijziging (veld hernoemd/verwijderd, gedrag gewijzigd) stilzwijgend bestaande consumers
zodra de nieuwe versie live gaat — met meerdere services (ADR-0002) is dat geen incident meer
binnen één team, maar een breuk tussen onafhankelijk deploybare eenheden.

## Beslissing

Elke service publiceert zijn contract onder een expliciete versieprefix in de URL (`/v1/...`).

- Een **backward-incompatibele** wijziging (veld verwijderd/hernoemd, betekenis gewijzigd,
  verplicht veld toegevoegd) verhoogt de prefix (`/v2/...`) **naast** de bestaande, niet in
  plaats ervan — de oude prefix blijft draaien tot expliciet uitgefaseerd.
- Een **backward-compatibele** toevoeging (nieuw optioneel veld, nieuw endpoint) leidt niet tot
  een versiebump.

## Consequenties

- Consumers breken nooit stilzwijgend door een deploy van de aanbiedende service.
- Meerdere versies van dezelfde service kunnen tijdelijk naast elkaar draaien tijdens een
  migratie van consumers.
- Nadeel, bewust geaccepteerd: een service kan tijdelijk twee versies van dezelfde routes moeten
  onderhouden.
- **Bewust nog niet besloten in dit ADR:** het uitfaseerbeleid zelf — hoe lang een oude versie
  blijft bestaan, en wie beslist wanneer hij weg mag. Dat blijft een apart, open backlogpunt;
  dit ADR legt alleen het mechanisme vast waarmee een versie zichtbaar wordt.
