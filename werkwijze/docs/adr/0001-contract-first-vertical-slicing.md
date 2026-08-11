# ADR-0001: Contract-first + vertical slicing als basiswerkwijze

**Status:** geaccepteerd
**Datum:** 2026-08-10 <!-- met terugwerkende kracht vastgelegd; dit is de beslissing die aan het begin van dit project al gold -->

## Context

Een project met een database, een API en een frontend kan het schema op drie plekken los van
elkaar laten ontstaan (tabel, contract, frontend-type), of het op één plek vastleggen en de rest
genereren. De eerste aanpak (schema-per-laag, met de hand gesynchroniseerd) is de bekendste
faalmodus: de drie versies lopen na een paar wijzigingen vanzelf uit elkaar, meestal pas zichtbaar
als een runtime-fout in productie.

Een tweede, onafhankelijke keuze: features horizontaal indelen (alle modellen bij elkaar, alle
routes bij elkaar) of verticaal (alles voor één feature bij elkaar, inclusief zijn eigen tests).
Horizontale indeling laat gedeelde bestanden (`models.py`, `routes.py`) onbeperkt groeien met elk
nieuw domein erbij.

## Beslissing

Vorm (velden, types) wordt op precies één plek vastgelegd — een SQLModel-class die tegelijk
databasetabel en Pydantic-contract is — en van daaruit gegenereerd naar de rest (OpenAPI-schema →
TypeScript-types). Gedrag (businessregels, validatie voorbij het schema) wordt apart, met de hand
geschreven, nooit gegenereerd.

Features worden verticaal georganiseerd: `api/app/features/<naam>/` bevat alles voor die feature
(`models.py`, `router.py`, `tests/`). Gedeelde bestanden (`db.py`, `main.py`) blijven dun —
alleen samenvoegers, geen domeinkennis.

Zie `.claude/skills/feature-bouwen/SKILL.md` voor de volledige, uitvoerbare regelreeks die hier
uit volgt.

## Consequenties

- Eén brontype per entiteit betekent dat een velduitbreiding altijd op dezelfde plek begint —
  geen keuze meer nodig over "waar pas ik dit als eerste aan".
- Genereren dwingt een vaste keten af (`scripts/genereer-types.sh`) die in CI gecontroleerd kan
  worden (`check-generated-types`) — dat kan alleen omdat er één bron is om tegen te
  verifiëren.
- Nadeel, bewust geaccepteerd: dit werkt alleen zolang de stack SQLModel/FastAPI +
  openapi-typescript blijft. Een andere taal/framework aan een van beide kanten (bijvoorbeeld
  een niet-Python-backend) vraagt een ander generatiemechanisme, niet alleen een andere
  implementatie van hetzelfde patroon.
- Nadeel: vertical slicing betekent dat een concept zonder natuurlijke eigenaar (gedeeld tussen
  ≥2 features) een expliciete beslissing vraagt (`shared/` vs. owner-export, zie
  `feature-bouwen` regel 8) — er is geen vanzelfsprekende plek zoals bij een horizontale indeling.
