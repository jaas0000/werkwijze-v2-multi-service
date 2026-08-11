---
name: feature-bouwen
description: >-
  Contract-first werkwijze voor het bouwen van een nieuwe feature vanuit een user story, of het
  uitbreiden van bestaand gedrag. Gebruik deze skill bij "implementeer deze story", "bouw een
  endpoint/domein voor X", "voeg een nieuwe tabel/feature toe", elke keer dat een gevalideerde
  user story of gedragsuitbreiding daadwerkelijk gebouwd gaat worden, én wanneer `pr-triage`
  een blocking review-bevinding laat oplossen — ook een fix op een bestaande PR volgt deze
  regels, geen aparte ad-hoc aanpak. Niet voor het toetsen van een nieuwe, nog niet gebouwde
  story op volledigheid (zie `story-review`, die loopt hieraan vooraf), niet voor het reviewen
  van een bestaande pull request (zie `code-review`), en niet voor een projectbrede zoektocht
  naar duplicatie (zie `architectuur-audit`).
---

# Feature bouwen — contract-first

**Trigger:** een nieuwe user story, een uitbreiding van bestaand gedrag, of `pr-triage` die een
blocking review-bevinding laat verwerken. Is de story nog niet gecheckt op volledigheid, draai
dan eerst `story-review`. Vraagt de story ook een UI: `frontend-bouwen` loopt ná regel 1-6
hieronder (schema, keten, logica, tests zijn dan klaar), vóór regel 9 (zie daar).

Lever nooit af zonder de checklist in regel 9 te doorlopen — zonder het controleerbare spoor
dat daar verplicht is, behandelt `code-review` de PR als onvolledig.

## Stappen (1-6)

1. **Schemabeslissing.** Leg de story en acceptatiecriteria vast in
   `docs/stories/<nummer>-<naam>.md`, met expliciet welke velden en types nodig zijn. Abstraheer
   niet vooruitlopend op een patroon dat nog niet is gezien — zie regel 8 voor wanneer
   duplicatie wél een probleem wordt. (Is de story al door `story-review` gegaan, dan is dit al
   gedaan — controleer alleen dat de story nog actueel is.)

2. **Isoleer.** Nieuwe feature → nieuwe map `api/app/features/<naam>/`. Nooit een tabel of
   route rechtstreeks in een gedeeld bestand (`db.py`, `main.py`) — die blijven dun, alleen
   samenvoegers.

3. **Schrijf de ene bron, zoals dit project 'm heeft vastgelegd in
   `docs/architectuur/stack-profiel.md` (§De ene bron).** Bestaat dat bestand nog niet: dat is de
   eerste vraag om te beantwoorden, geen aanname om impliciet te maken — kopieer
   `docs/architectuur/stack-profiel.TEMPLATE.md` en vul 'm in vóór je verdergaat.

   In het SQLModel-referentievoorbeeld (zie `werkwijze-v1-contract-first`) is de ene bron één `models.py` per
   feature met SQLModel-classes (`table=True`), waar elke class tegelijk databasetabel en
   Pydantic-contract is:

   ```python
   class XBase(SQLModel):          # gedeelde velden
       ...

   class X(XBase, table=True):     # de tabel
       id: int | None = Field(default=None, primary_key=True)

   class XCreate(XBase):           # wat een client mag aanleveren
       ...

   class XRead(XBase):             # wat een client terugkrijgt
       id: int
   ```

   Geen gedragslogica hier — een schema kan "mag dit nu wel" niet uitdrukken (regel 5). Vermijd
   `Relationship()` met een generic forward-ref (`list["Y"]`) onder
   `from __future__ import annotations` (zie §Bekende valkuilen hieronder); zoek gerelateerde
   rijen op via `select(...).where(...)` in de router. (Deze twee alinea's zijn specifiek voor
   het SQLModel-stack-profiel (zie `werkwijze-v1-contract-first`) — een project met een ander
   stack-profiel past hetzelfde principe toe op zijn eigen vorm van "de ene bron".)

4. **Genereer de keten.**

   ```bash
   scripts/genereer-types.sh
   ```

   `app.openapi()` → `openapi-typescript`. Bewerk gegenereerde bestanden
   (`frontend/generated/*`) nooit met de hand — draai het script opnieuw. Commit de output mee.

5. **Schrijf wat niet uit de vorm volgt.** Auth-checks, validatie voorbij het schema,
   businessregels — in `router.py`. Aparte, hand-geschreven code per concern, geen duplicatie
   met regel 3.

6. **Test gedrag, niet vorm.** Vorm is al gegarandeerd door regel 3-4. Test de
   acceptatiecriteria en de randgevallen: de businessregel zelf, wat er gebeurt als je hem
   probeert te omzeilen, 404's op onbekende id's.

## Situationeel (7-8)

7. **Bestaande database? Migratie apart.** `SQLModel.metadata.create_all()` maakt alleen
   ontbrekende tabellen aan — geen ALTER, geen kolom-migratie op een bestaande tabel. Zodra dit
   tegen een bestaande productiedatabase draait: Alembic (of gelijkwaardig), los van dit
   patroon.

8. **Gedeelde logica: opportunistisch verwijzen, niet vooruitlopend abstraheren.** Duplicatie
   is pas een probleem ná de tweede, onafhankelijke implementatie van hetzelfde patroon — niet
   vóór de eerste. Herken je tijdens het bouwen dat deze feature een patroon herhaalt van een
   feature die je al kent (de story verwijst ernaar, of je hebt 'm net gelezen): verwijs naar de
   bestaande implementatie in plaats van te kopiëren. Twee gevallen, met een andere bestemming:

   - **Het patroon hoort bij één entiteit die een andere feature al bezit** (bijvoorbeeld:
     "bestaat dit boek?" hoort bij `Boek`, dat eigendom is van `uitlenen`) → maak de bestaande
     functie openbaar in de eigenaar-feature (geen underscore-prefix), importeer 'm vanuit de
     consumerende feature. Geen `shared/`-geval: er is een duidelijke eigenaar.
   - **Het patroon heeft geen natuurlijke eigenaar** (een generieke implementatie die evengoed
     bij feature A als bij feature B had kunnen ontstaan) → naar `api/app/shared/<naam>.py`.

   Ga hiervoor **niet** het hele project doorzoeken (dat staat haaks op regel 2) — systematisch
   zoeken naar duplicatie die je nog niet kende is `architectuur-audit`'s taak, niet die van
   deze skill. Gebruik je een van de twee routes: zet in de story van *deze* feature de regel
   "gebruikt `shared/<naam>.py`, zie daar" of "gebruikt `<feature>.router.<functie>`, zie daar"
   — die terugverwijzing verwacht `architectuur-audit` aan te treffen, en helpt andere features
   het te vinden zonder te hoeven zoeken.

## Afleveren (9-10)

9. **Checklist — doorloop dit expliciet, sla geen stap over:**

   - [ ] Tests groen (regel 6).
   - [ ] Generatieketen gedraaid, geen diff op `frontend/generated/*` (regel 4).
   - [ ] Vroeg de story ook een UI: `frontend-bouwen` is afgerond (inclusief zijn eigen
     E2E-test-eis).
   - [ ] Check `CLAUDE.md` §Instellingen — Simplify bij feature-bouwen:
     - `ja` — draai `/simplify` daadwerkelijk (ingebouwde Claude Code-skill, niet zelf
       herimplementeren: vier parallelle checks — reuse, simplificatie, efficiency, altitude —
       op de wijzigingen sinds de vorige `/simplify`-ronde op deze PR, exclusief
       `frontend/generated/*`). Bevindingen direct toepassen. Expliciet **geen correctheid**,
       dat blijft `code-review`'s taak. Geen kortsluitroute op basis van "de wijziging is klein"
       — zie §Bekende valkuilen voor waarom juist kleine wijzigingen dit risico lopen.
     - `nee` — sla de daadwerkelijke check over, maar niet stilzwijgend: ga direct door naar de
       volgende stap.
   - [ ] Het commit-bericht (bij een fix-commit op een bestaande PR) of de PR-beschrijving (bij
     de eerste keer) bevat één van deze vier regels — dit is de canonieke lijst, andere skills
     verwijzen hiernaar in plaats van 'm te herhalen:
     - `Simplify: <bevindingen>` — er was iets te verbeteren, en dat is gebeurd.
     - `Simplify: geen` — gedraaid, niets gevonden.
     - `Simplify: overgeslagen (instelling staat op nee)` — de instelling stond uit.
     - `Simplify: n.v.t. (<reden>)` — deze wijziging bevat geen productiecode om op te
       toetsen (bv. puur documentatie of CI-configuratie).

     Dit is het enige controleerbare bewijs dat deze stap is afgehandeld — geen aanname die je
     zelf mag maken. Zonder een van deze vier regels behandelt `code-review` een PR die
     `api/app/features/**` of `frontend/src/**` raakt als onvolledig.

   Vink deze lijst niet stilzwijgend af door meteen naar git-commando's te gaan (zie §Bekende
   valkuilen).

10. **Afleveren.** Twee verschillende acties, afhankelijk van de trigger:

    - **Eerste keer** (nieuwe story/uitbreiding, inclusief een eventuele `frontend-bouwen`-fase
      erin): **open de PR.** Vanaf dat moment is `pr-triage` aan zet.
    - **Fix op een blocking bevinding** (`pr-triage` stuurde je hierheen): de PR bestaat al —
      **push een commit op die bestaande PR**, open geen nieuwe. `pr-triage` pikt de nieuwe
      commit vanzelf weer op (zijn trigger is "PR aangemaakt, of krijgt een nieuwe commit").

## Bekende valkuilen

- **`SQLModel Relationship()` + generic forward-ref (`list["Y"]`) onder
  `from __future__ import annotations`** → `sqlalchemy.exc.InvalidRequestError` bij
  mapper-initialisatie; de class-registry kan de gestringificeerde annotatie niet resolven.
  Oplossing: voeg geen ORM-relatie toe die je niet gebruikt (zoek op via een query), of gebruik
  `TYPE_CHECKING`-imports met expliciete `Mapped[...]`-annotaties.
- **`datetime.utcnow()` is deprecated** — gebruik `datetime.now(UTC)`.
- **`openapi-typescript` trekt via `@redocly/openapi-core` soms een kwetsbare `js-yaml`-versie
  mee** zonder beschikbare fix. Dev-only build-tooling — risico is acceptabel, raakt nooit de
  productie-runtime.
- **Zonder het eigenaar/ownerless-onderscheid in regel 8 verzandt gedeeld gedrag in een
  asymmetrische herimplementatie**: een tweede feature die dezelfde check nodig heeft als een
  bestaande (bijvoorbeeld "bestaat deze entiteit?") krijgt dan al snel een eigen, private
  kopie in plaats van de bestaande functie te hergebruiken, simpelweg omdat er geen duidelijke
  "plek" leek te zijn om naartoe te verwijzen. Vandaar de twee expliciete routes in regel 8.
- **Een checklist-item dat alleen als tekst in een lijst staat, is makkelijk te missen zodra de
  rest van het werk klaar aanvoelt** — met name vlak vóór het committen, wanneer de aandacht al
  naar de volgende taak is verschoven. Vandaar de expliciete checklist in regel 9 én de
  verplichte spoor-regel in het commit-/PR-bericht: een lijst zonder controleerbaar bewijs is
  geen vangrail (zie `CLAUDE.md` §Verificatie-principe).

Kom je een nieuwe, structurele valkuil tegen (niet een eenmalige bug, maar iets dat deze skill
raakt): voeg hem hier toe als een generieke les, niet als een verslag van één specifieke build.

## Wat dit niet oplost

- **Migratie van een bestaande productiedatabase** — Alembic, los van dit patroon (regel 7).
- **Contracten tussen services** — een los draaiend ander proces, of een ongetypeerd
  streaming-endpoint, valt buiten bereik: OpenAPI/openapi-typescript dekt alleen wat via een
  `response_model` in déze FastAPI-app loopt.
- **Precisie die de bron zelf niet heeft** — een `str` in plaats van een `Literal[...]`
  genereert een losse `string`, geen strikter type. Wees scherp in regel 3.
