# Story <nummer>: <titel>

**Prioriteit:** none <!-- none | low | medium | high — `none` is de startwaarde, `story-review` vult 'm aan -->
**Story points:** <!-- leeg laten; `story-review` kent 1-5 toe -->
**Service:** <!-- bij welke service hoort deze feature (stack-profiel.md §Topologie)? -->

Kopieer dit bestand naar `docs/stories/<nummer>-<naam>.md` en hernummer het; bewerk de template
zelf niet. `story-review` toetst het resultaat vóórdat `feature-bouwen` begint.

## Verhaal

Als <rol> wil ik <actie> zodat <reden>.

## Acceptatiecriteria

Concreet en testbaar — elk criterium moet direct te vertalen zijn naar een gedragstest
(`feature-bouwen` regel 6). "Het werkt intuïtief" is geen acceptatiecriterium.

- [ ]
- [ ]

## Schemabeslissing

Welke velden en types komen erbij of veranderen, en op welke entiteit? Expliciet, niet "de
gebruikelijke velden" (`feature-bouwen` regel 1 en 3).

## Edge cases

Wat gebeurt er bij ongeldige invoer, een actie op iets dat niet bestaat, of een actie die al is
uitgevoerd (dubbel aanmaken, dubbel inleveren)?

## Auth / rollen

Wie mag deze actie uitvoeren? Bij een muterend endpoint is dit verplicht — een lege sectie is
een onduidelijkheid, geen aanname om zelf te maken.

## Gedeelde logica

Gebruikt deze feature een bestaande implementatie, noteer dan de terugverwijzing in het formaat
uit `feature-bouwen` regel 8: "gebruikt `shared/<naam>.py`, zie daar" of "gebruikt
`<feature>.<module>.<functie>`, zie daar". Binnen dezelfde service — over een servicegrens heen
kan het niet (ADR-0002). Anders: leeg laten.

## UI

Vraagt deze story een scherm? Zo ja, welk gedrag moet zichtbaar zijn (`frontend-bouwen`)? Zo
nee: "geen UI".
