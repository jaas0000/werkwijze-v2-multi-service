# Backlog werkwijze-v2-multi-service

Wat er nog in moet komen. Volgorde = volgorde van bespreken, niet van prioriteit.

## Core (altijd van toepassing)

- [ ] Multi-service topologie — hoe services gedefinieerd, hoe ze communiceren. Dát het er
      meerdere zijn ligt vast (ADR-0002); nog open: hoeveel, hoe ze heten, synchroon HTTP of
      events, en hoe een service-grens in `stack-profiel.md` §Topologie beschreven wordt zodat
      de skills 'm kunnen lezen
- [ ] Contract-first — SQLAlchemy Core + Pydantic + openapi-typescript; CI dwingt generatie af.
      Nog open: hoe "de ene bron" er in die combinatie concreet uitziet (welk bestand, welke
      relatie tussen tabeldefinitie en Pydantic-model), en hoe de generatieketen heet — de
      skills verwijzen daarvoor nu naar `stack-profiel.md` §De ene bron / §Contractgeneratie
- [ ] Cross-service contracten — hoe het contract tussen twee services vastligt en geversioneerd
      wordt (ADR-0002 stelt alleen dat het een eigen artefact is, geen gedeelde import)
- [ ] API-versioning — automatisch, expliciete strategie zodat DB-evolutie consumers niet stilletjes breekt
- [ ] CI/CD per service — monorepo-matrix of losse workflows. De werkwijze verwijst al bij naam
      naar checks die deze workflow moet leveren: `check-generated-types`,
      `check-frontend-e2e-coverage`, `check-python-style`, `check-ts-style` en de testrun. Tot
      die workflow bestaat, staat been 1 van het Verificatie-principe stil
- [ ] Vertical slicing per service — de indeling *binnen* een service (ADR-0001, ADR-0002); nog
      open: hoe gedeelde code tussen services eruitziet (gedeelde bibliotheek met eigen
      versionering, of bewuste duplicatie)

- [ ] Referentie-implementatie `voorbeeld/wetsanalyse/` — bevat nu alleen een CLAUDE.md. Nodig:
      een ingevuld `docs/architectuur/stack-profiel.md` (ADR-0004 verwijst ernaar als het
      voorbeeld dat er nog niet is), plus genoeg code om als kopieerbaar skelet te dienen
      (`CLAUDE.md` §Hoe gebruik je dit gaat daar nu expliciet niet van uit)

## Optionele bouwstenen

- [ ] Auth / login — Auth.js + sessie-management + TOTP-2FA
- [ ] Secrets — genereren, opslaan als bestanden, doorgeven via `*_FILE`-patroon
- [ ] MCP-client — externe service koppelen met eigen error-boundary
- [ ] LLM-integratie — modelprofielen, concurrency-rem, retry, prompt-caching
- [ ] Async jobs — claim/lease/reaper/reconcile-bij-herstart
- [ ] Observability — structured logs, OTel, Grafana
- [ ] Store-abstractie — Protocol-gebaseerde Store (SQLite in tests, Postgres in productie)
- [ ] Deployment — Azure ACA + Docker Compose lokaal
