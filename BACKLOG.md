# Backlog werkwijze-v2-multi-service

Wat er nog in moet komen. Volgorde = volgorde van bespreken, niet van prioriteit.

## Core (altijd van toepassing)

- [ ] Multi-service topologie — hoe services gedefinieerd, hoe ze communiceren
- [ ] Contract-first — SQLAlchemy Core + Pydantic + openapi-typescript; CI dwingt generatie af
- [ ] API-versioning — automatisch, expliciete strategie zodat DB-evolutie consumers niet stilletjes breekt
- [ ] CI/CD per service — monorepo-matrix of losse workflows
- [ ] Vertical slicing per service

## Optionele bouwstenen

- [ ] Auth / login — Auth.js + sessie-management + TOTP-2FA
- [ ] Secrets — genereren, opslaan als bestanden, doorgeven via `*_FILE`-patroon
- [ ] MCP-client — externe service koppelen met eigen error-boundary
- [ ] LLM-integratie — modelprofielen, concurrency-rem, retry, prompt-caching
- [ ] Async jobs — claim/lease/reaper/reconcile-bij-herstart
- [ ] Observability — structured logs, OTel, Grafana
- [ ] Store-abstractie — Protocol-gebaseerde Store (SQLite in tests, Postgres in productie)
- [ ] Deployment — Azure ACA + Docker Compose lokaal
