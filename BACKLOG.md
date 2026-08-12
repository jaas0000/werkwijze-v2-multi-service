# Backlog werkwijze-v2-multi-service

Wat er nog in moet komen. Volgorde = volgorde van bespreken, niet van prioriteit.

## Core (altijd van toepassing)

- [ ] Multi-service topologie — hoe services gedefinieerd, hoe ze communiceren. Dát het er
      meerdere zijn ligt vast (ADR-0002); nog open: hoeveel, hoe ze heten, synchroon HTTP of
      events, en hoe een service-grens in `stack-profiel.md` §Topologie beschreven wordt zodat
      de skills 'm kunnen lezen
- [ ] Contract-first — SQLAlchemy Core + Pydantic + openapi-typescript; CI dwingt generatie af.
      Het patroon (ADR-0011) én het CI-sjabloon dat de check bevat (ADR-0016, stap 2) liggen
      vast. Nog open: de daadwerkelijke workflow bestaat pas zodra de topologie (hieronder)
      bekend is en het sjabloon per service ingevuld wordt
- [ ] Cross-service contracten — hoe het contract tussen twee services vastligt en geversioneerd
      wordt (ADR-0002 stelt alleen dat het een eigen artefact is, geen gedeelde import)
- [x] API-versioning — mechanisme én uitfaseerbeleid liggen vast in ADR-0010
- [ ] CI/CD per service — het sjabloon (welke stappen, in welke volgorde, per-service-workflow
      i.p.v. een gedeelde matrix) ligt vast in ADR-0016. Nog open: de daadwerkelijke matrix —
      hoeveel van deze workflowbestanden er komen, wacht op de topologiebeslissing hieronder.
      Tot die workflows daadwerkelijk bestaan, staat been 1 van het Verificatie-principe stil
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
- [x] MCP-server & -client — twee kanten met eigen eisen (registratie/contract voor de
      server-kant, error-boundary voor de client-kant). Zie ADR-0014
- [x] Contracttests voor MCP-oppervlakken — aparte verificatie naast de generatieketen, want een
      MCP-tool-schema is geen OpenAPI-`response_model`. Zie ADR-0013
- [ ] LLM-integratie — modelprofielen, concurrency-rem, retry, prompt-caching
- [ ] Orkestratie/workflow-engine — een centraal proces dat een taak in meerdere fasen
      aanstuurt (bv. meerdere LLM-aanroepen + tussenstappen na elkaar); ander bouwstuk dan losse
      LLM-integratie, met een eigen vraag over waar die staat (eigen service, of binnen een
      feature) en hoe voortgang/status zichtbaar blijft
- [x] Async jobs — claim/lease/reaper/reconcile-bij-herstart. Zie ADR-0008
- [ ] Gedeelde referentie-/inhoudsbron tussen een interactieve skill en een runtime-service —
      bv. dezelfde reference-content die zowel een CLI-skill als een API-endpoint gebruikt; geen
      gewone databasetabel, dus valt buiten de standaard-Store-abstractie hieronder
- [x] Runtime-configuratie / feature-flags — eigen store, admin-only schrijfbaar, read-through
      cache. Zie ADR-0015
- [ ] Observability — de baseline (structured JSON-logs + correlation-ID tussen services) ligt
      vast in ADR-0012. Nog open: het log-/tracing-backend (OTel, Grafana) en de dashboards
- [x] Store-abstractie — Protocol-gebaseerde Store (SQLite in tests, Postgres in productie). Zie
      ADR-0007
- [ ] Deployment — Azure ACA + Docker Compose lokaal
