# Voorbeeld: wetsanalyse

Referentie-implementatie bij deze werkwijze, gericht op een multi-service stack.
**Stand van zaken:** topologie en stack-profiel liggen vast (zie hieronder), en de `api`-service
bevat nu zijn eerste feature: het feedback-domein
(`api/app/features/feedback/`) — indienen, admin-lijst met paginering, verwijderen,
ongelezen-aantal en markeer-gezien, herbouwd volgens deze werkwijze op basis van het
feedback-domein van het externe `wetsanalyse-ai`-project (niet gekopieerd, opnieuw opgezet met
vertical slicing, een store-abstractie en een eigen `feedback_leesbewijzen`-tabel in plaats van
een geleende kolom op een users-tabel — zie
[`docs/stories/001-feedback-indienen-en-beheren.md`](docs/stories/001-feedback-indienen-en-beheren.md)
voor de volledige afweging). De overige zeven domeinen van `api` en de vijf andere services uit
de topologie hieronder staan nog niet — er valt dus nog niet genoeg te kopiëren als startpunt
voor een nieuw project, wel om te zien hoe één feature er in deze werkwijze uitziet.

Draai het lokaal: `cd api && uv sync && uv run pytest -q` (tests groen), `uv run ruff check . &&
uv run ruff format --check .` (codestandaard schoon), `alembic upgrade head` tegen een schone
SQLite-db (migratie draait).

## Structuur (topologie vastgelegd, `api`/feedback gebouwd, rest nog niet)

Zes services, zie [`docs/architectuur/adr/0001-multi-service-topologie.md`](docs/architectuur/adr/0001-multi-service-topologie.md)
voor de volledige afweging:

| Service | Verantwoordelijk voor |
|---|---|
| `api/` | Kernbackend: analyse/jobs, LLM-configuratie, auth, wetcatalogus, runtime-config, annotatie, berichten, feedback, admin, orkestratie (module) |
| `frontend/` | Hoofdwebapp (BFF) |
| `frontend-chat/` | Losse chatapp |
| `tools/wettenbank-mcp/` | MCP-server, wetcatalogus-lookups |
| `tools/graph-qa/` | QA-/annotatie-agent |
| `tools/wetsanalyse-admin-mcp/` | Admin-MCP |

Alle projectspecifieke stack-keuzes (de ene bron, contractgeneratie, feature-eenheid, dunne
verzamelaars, migraties, frontends, codestandaard) staan in
[`docs/architectuur/stack-profiel.md`](docs/architectuur/stack-profiel.md) — `feature-bouwen`
regel 3 leest daaruit.

## Volgende stap

Nog open, in volgorde van afhankelijkheid (zie ook `BACKLOG.md` in de root van deze repo): de
overige zeven domeinen van `api` (analyse/jobs, LLM-configuratie, auth, wetcatalogus,
runtime-config, annotatie, berichten) als evenzoveel feature-mappen herbouwen — dat is nog
steeds de grootste stap, zie ADR-0001 §Consequenties — de vijf andere services opzetten, het
CI/CD-sjabloon (werkwijze-ADR-0016) invullen, en cross-service-contracten vastleggen zodra er
meer dan één service is om te verbinden.

De methodologie zelf staat in [`../../werkwijze/`](../../werkwijze/).
