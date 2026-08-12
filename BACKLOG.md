# Backlog werkwijze-v2-multi-service

Wat er nog in moet komen. Volgorde = volgorde van bespreken, niet van prioriteit.

## Core (altijd van toepassing)

- [ ] Multi-service topologie — hoe services gedefinieerd, hoe ze communiceren. Dát het er
      meerdere zijn ligt vast (ADR-0002); nog open: hoeveel, hoe ze heten, synchroon HTTP of
      events, en hoe een service-grens in `stack-profiel.md` §Topologie beschreven wordt zodat
      de skills 'm kunnen lezen
- [ ] Contract-first — SQLAlchemy Core + Pydantic + openapi-typescript; CI dwingt generatie af.
      Het patroon zelf (welk bestand, relatie tabeldefinitie ↔ Pydantic-model, naam van de
      generatieketen) ligt vast in ADR-0011. Nog open: de CI-afdwinging zelf, die hangt af van
      het "CI/CD per service"-punt hieronder
- [ ] Cross-service contracten — hoe het contract tussen twee services vastligt en geversioneerd
      wordt (ADR-0002 stelt alleen dat het een eigen artefact is, geen gedeelde import)
- [ ] API-versioning — het mechanisme (URL-prefix, `/v1/` naast `/v2/` bij een
      backward-incompatibele wijziging) ligt vast in ADR-0010. Nog open: het uitfaseerbeleid
      zelf — hoe lang een oude versie blijft bestaan, wie beslist wanneer hij weg mag
- [ ] CI/CD per service — monorepo-matrix of losse workflows. De werkwijze verwijst al bij naam
      naar checks die deze workflow moet leveren: `check-generated-types`,
      `check-frontend-e2e-coverage`, `check-python-style`, `check-ts-style` en de testrun. Tot
      die workflow bestaat, staat been 1 van het Verificatie-principe stil
- [ ] Vertical slicing per service — de indeling *binnen* een service (ADR-0001, ADR-0002); nog
      open: hoe gedeelde code tussen services eruitziet (gedeelde bibliotheek met eigen
      versionering, of bewuste duplicatie)
- [x] Migraties — Alembic verplicht zodra een service een productiedatabase heeft, geen
      handmatige schema-reconciliatiefunctie. Zie ADR-0005
- [ ] Meerdere frontend-apps — topologie beschrijft nu alleen backend-services; hoeveel
      frontend-apps er zijn en hoe elk zich verhoudt tot welke service(s)/contract(en) staat nog
      nergens vast. `frontend-bouwen` gaat vooralsnog uit van precies één `frontend/`-pad

- [ ] Referentie-implementatie `voorbeeld/wetsanalyse/` — bevat nu alleen een CLAUDE.md. Nodig:
      een ingevuld `docs/architectuur/stack-profiel.md` (ADR-0004 verwijst ernaar als het
      voorbeeld dat er nog niet is), plus genoeg code om als kopieerbaar skelet te dienen
      (`CLAUDE.md` §Hoe gebruik je dit gaat daar nu expliciet niet van uit)

## Optionele bouwstenen

- [x] Auth / login — twee gescheiden schema's: gebruikersinlog (Auth.js + sessie-management +
      TOTP-2FA, rollen) én een apart service-naar-service/admin-schema (bearer-tokens). Zie
      ADR-0009
- [x] Secrets — bestandsgebaseerd, `*_FILE`-patroon. Zie ADR-0006
- [ ] MCP-server & -client — twee kanten: een eigen MCP-endpoint exposen (een service die
      andere partijen als MCP-server aanspreken) is een ander bouwstuk dan een externe
      MCP-service consumeren (het bestaande "MCP-client"-punt)
- [ ] Contracttests voor MCP-oppervlakken — aparte verificatie naast de generatieketen: een
      MCP-tool-schema is geen OpenAPI-`response_model` en wordt dus niet door dezelfde check
      gedekt
- [ ] LLM-integratie — modelprofielen, concurrency-rem, retry, prompt-caching
- [ ] Orkestratie/workflow-engine — een centraal proces dat een taak in meerdere fasen
      aanstuurt (bv. meerdere LLM-aanroepen + tussenstappen na elkaar); ander bouwstuk dan losse
      LLM-integratie, met een eigen vraag over waar die staat (eigen service, of binnen een
      feature) en hoe voortgang/status zichtbaar blijft
- [x] Async jobs — claim/lease/reaper/reconcile-bij-herstart. Zie ADR-0008
- [ ] Gedeelde referentie-/inhoudsbron tussen een interactieve skill en een runtime-service —
      bv. dezelfde reference-content die zowel een CLI-skill als een API-endpoint gebruikt; geen
      gewone databasetabel, dus valt buiten de standaard-Store-abstractie hieronder
- [ ] Runtime-configuratie / feature-flags — instellingen die tijdens bedrijf wijzigen zonder
      herdeploy, los van Secrets (die zijn build-/opstarttijd) en los van Store-abstractie (die
      is voor domeindata)
- [ ] Observability — de baseline (structured JSON-logs + correlation-ID tussen services) ligt
      vast in ADR-0012. Nog open: het log-/tracing-backend (OTel, Grafana) en de dashboards
- [x] Store-abstractie — Protocol-gebaseerde Store (SQLite in tests, Postgres in productie). Zie
      ADR-0007
- [ ] Deployment — Azure ACA + Docker Compose lokaal
