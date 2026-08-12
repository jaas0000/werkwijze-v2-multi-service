# ADR-0004: Stack-profiel voor projectspecifieke aannames

**Status:** geaccepteerd
**Datum:** 2026-08-10

## Context

De werkwijze werd tot nu toe geschreven met precies één stack in gedachten (SQLModel/FastAPI,
één class = tabel + contract, openapi-typescript-generatie naar één frontend, één monolithisch
proces — zie ADR-0001 en ADR-0002). Die aannames staan hard gecodeerd in de skills zelf, met
name in `feature-bouwen` regel 3 ("de ene bron" = een SQLModel-class).

Een project met een wezenlijk andere architectuur (bijvoorbeeld: SQLAlchemy Core in plaats van
SQLModel, geen automatische contractgeneratie, meerdere services in plaats van één monoliet, een
overwegend horizontale bestaande structuur in plaats van vertical-sliced feature-mappen) kan
`feature-bouwen` regel 3 dan niet volgen — niet omdat het onderliggende principe ("vorm op één
plek, expliciet, vóór gedrag") niet zou gelden, maar omdat de regel de concrete SQLModel-vorm
ervan als enige mogelijkheid beschrijft.

De rest van de werkwijze (Verificatie-principe, Simplify-stap, PR-triage-staatmachine,
story-review-checklist, "duplicatie pas na de tweede implementatie", ADR-praktijk zelf) is al
stack-onafhankelijk gebleken bij nader onderzoek — het gaat hier specifiek om de handvol regels
die een concrete implementatievorm hardcoderen in plaats van een principe uit te drukken.

## Beslissing

Een nieuw, projectspecifiek artefact — `docs/architectuur/stack-profiel.md`, met template
`docs/architectuur/stack-profiel.TEMPLATE.md` — legt de antwoorden vast op de vragen die een
skill anders stilzwijgend zou aannemen (de ene bron, contractgeneratie, feature-eenheid, dunne
verzamelaars, topologie, migraties, frontend(s)). `feature-bouwen` regel 3 is als eerste, enige
regel herschreven om naar dit bestand te verwijzen in plaats van SQLModel hard te coderen; het
bestaande SQLModel-voorbeeld blijft staan als illustratie (zie `werkwijze-v1-contract-first`) als referentie voor die specifieke stack, niet als universele wet.

`voorbeeld/wetsanalyse/` krijgt zelf een ingevuld `stack-profiel.md` dat de v2-architectuurkeuzes
herformuleert in deze vorm — het is een aparte, latere stap.

## Consequenties

- Een project kan nu, in principe, `feature-bouwen` regel 3 volgen ongeacht zijn concrete
  stack, zolang het zijn `stack-profiel.md` invult. Zonder dat bestand is regel 3 een expliciete
  stop, geen impliciete SQLModel-aanname meer.
- **Bewust nog niet gegeneraliseerd in deze ronde** (elk van deze bevat een vergelijkbare, nu nog
  hardgecodeerde aanname, ontdekt bij hetzelfde onderzoek):
  - `feature-bouwen` regel 4 ("Genereer de keten") — gaat nog uit van een openapi-typescript-
    generatiescript; een stack-profiel zonder contractgeneratie (§Contractgeneratie = "nee")
    heeft hier nog geen alternatief pad.
  - `feature-bouwen` regel 7 ("Migratie apart") — verwijst nog rechtstreeks naar
    `SQLModel.metadata.create_all()` in plaats van naar stack-profiel §Migraties.
  - `code-review` — checklistpunten ("Schema staat alleen in `models.py` (SQLModel-classes)",
    "`frontend/generated/*` is alleen gewijzigd via het genereerscript") zijn nog SQLModel-
    specifiek geformuleerd.
  - `architectuur-audit` regel 1 — leest nog specifiek `api/app/features/`, `api/app/db.py`,
    `api/app/main.py`; geen mechanisme voor een multi-service-topologie (stack-profiel
    §Topologie = "meerdere services").
  - `frontend-bouwen` — gaat nog uit van precies één `frontend/`-pad; geen mechanisme om tussen
    meerdere frontend-apps te kiezen (stack-profiel §Frontend(s) > 1).
  - `dependency-updates` regel 1 — groepeert nog naar een vaste lijst van drie
    manifestbestanden; een multi-service-project heeft er potentieel veel meer.
  - `wetsanalyse-ai` zelf krijgt in deze ronde geen `stack-profiel.md` en wordt niet aangeraakt —
    dat is een aparte, latere stap.
- Dit is bewust een gefaseerde aanpak, niet uitgesteld door onwil: elke bovenstaande regel raakt
  een ander deel van de werkwijze, en in één keer alles generaliseren zonder een werkend
  voorbeeld eerst (dit ADR + regel 3) zou het risico op een half doordachte abstractie vergroten.
