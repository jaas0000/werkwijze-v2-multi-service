# Voorbeeld: wetsanalyse

Referentie-implementatie bij deze werkwijze, gericht op een multi-service stack.
**Stand van zaken:** topologie en stack-profiel liggen vast (zie hieronder), maar er is nog
geen code — geen services, geen `.github/`. Er valt hier dus nog niets te kopiëren als startpunt
voor een nieuw project.

## Structuur (vastgelegd, nog niet gebouwd)

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

Nog open, in volgorde van afhankelijkheid (zie ook `BACKLOG.md` in de root van deze repo):
cross-service-contracten vastleggen, het CI/CD-sjabloon (werkwijze-ADR-0016) invullen voor deze
zes services, en dan de eerste feature bouwen (de interne herindeling van `api`'s acht domeinen
is daarbij de eerste, grootste stap — zie ADR-0001 §Consequenties).

De methodologie zelf staat in [`../../werkwijze/`](../../werkwijze/).
