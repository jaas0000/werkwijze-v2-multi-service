# Backlog werkwijze-v2-multi-service

Wat er nog in moet komen. Volgorde = volgorde van bespreken, niet van prioriteit.

## Core (altijd van toepassing)

- [x] Multi-service topologie — zes services, communicatiestijl (synchroon HTTP) en plek van de
      orkestratie (module binnen `api`) vastgelegd voor de referentie-implementatie. Zie
      `voorbeeld/wetsanalyse/docs/architectuur/adr/0001-multi-service-topologie.md`
- [ ] Contract-first — SQLAlchemy Core + Pydantic + openapi-typescript; CI dwingt generatie af.
      Het patroon (ADR-0011) én het CI-sjabloon (ADR-0016, stap 2) liggen vast, en de topologie
      is nu bekend (zes services). Nog open: de daadwerkelijke workflowbestanden per service
      bestaan nog niet
- [ ] Cross-service contracten — hoe het contract tussen twee services vastligt en geversioneerd
      wordt (ADR-0002 stelt alleen dat het een eigen artefact is, geen gedeelde import; ook het
      nieuwe topologie-ADR laat dit expliciet open)
- [x] API-versioning — mechanisme én uitfaseerbeleid liggen vast in ADR-0010
- [ ] CI/CD per service — het sjabloon ligt vast (ADR-0016), en de matrix is nu bekend (zes
      services, zie topologie hierboven). Nog open: de workflowbestanden zelf moeten nog gebouwd
      worden — tot dan staat been 1 van het Verificatie-principe stil voor deze services
- [ ] Vertical slicing per service — de indeling *binnen* een service (ADR-0001, ADR-0002); nog
      open: hoe gedeelde code tussen services eruitziet (gedeelde bibliotheek met eigen
      versionering, of bewuste duplicatie)
- [x] Migraties — Alembic verplicht zodra een service een productiedatabase heeft, geen
      handmatige schema-reconciliatiefunctie. Zie ADR-0005
- [x] Meerdere frontend-apps — twee (`frontend`, `frontend-chat`), beide praten alleen met
      `api`. Zie het topologie-ADR hierboven

- [ ] Referentie-implementatie `voorbeeld/wetsanalyse/` — `docs/architectuur/stack-profiel.md`
      is nu ingevuld en het topologie-ADR staat er. Nog nodig: genoeg code om als kopieerbaar
      skelet te dienen (`CLAUDE.md` §Hoe gebruik je dit gaat daar nog niet van uit)

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
      LLM-integratie. "Waar die staat" is voor de referentie-implementatie beantwoord (module
      binnen `api`, zie het topologie-ADR); nog open: hoe voortgang/status zichtbaar blijft
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
