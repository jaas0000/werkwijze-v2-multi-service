# werkwijze-v2-multi-service

Een AI-werkwijze voor het bouwen van multi-service applicaties: meerdere onafhankelijk
deploybare services (ADR-0002), een Next.js BFF, LLM-orkestratie en async achtergrondtaken.
De methodologie is geschreven om door Claude Code uitgevoerd te worden, niet alleen gelezen.

Deze repo bestaat uit twee mappen:

- **`werkwijze/`** — de methodologie: skills, het hoofddocument en ADR's over de werkwijze zelf.
  Zie `werkwijze/CLAUDE.md` voor de volledige methodologie-uitleg.
- **`voorbeeld/`** — referentie-implementaties die de werkwijze in de praktijk laten zien, elk
  in een eigen submap met een eigen stack-profiel:
  - `wetsanalyse/` — nog niet uitgebouwd: alleen een CLAUDE.md, nog geen code; zie
    `voorbeeld/wetsanalyse/CLAUDE.md`.

  Bedoeling is dat elke submap onder `voorbeeld/` zelf het kopieerbare startpunt is voor een
  nieuw project met die architectuur — niet `voorbeeld/` als geheel. Zover is het nog niet:
  zie `BACKLOG.md`.

## Hoe gebruik je dit

1. Fork of clone deze repo.
2. Maak een lege repo voor je project. Kopieer daarin
   `werkwijze/docs/architectuur/stack-profiel.TEMPLATE.md` naar
   `docs/architectuur/stack-profiel.md` en vul 'm in — dat is de eerste stap, niet een
   formaliteit achteraf (ADR-0004). Zolang `voorbeeld/wetsanalyse/` nog geen code bevat, is er
   geen map om te kopiëren als skelet.
3. Zet de agent-root op een map die **beide** repo's bevat — de werkwijze-repo én je nieuwe
   project-repo — zodat de AI de methodologie kan lezen terwijl ze aan het project werkt.
4. Kopieer `werkwijze/.claude/skills/` naar `<agent-root>/.claude/skills/` zodat de skills
   beschikbaar zijn vanuit elke submap in de workspace.

## Bij wijzigingen in de werkwijze

Pas `werkwijze/CLAUDE.md` en de relevante skill(s) aan, en werk `voorbeeld/wetsanalyse/` bij
zodra dat code bevat, zodat het de gewijzigde werkwijze blijft illustreren. Verandert er iets
aan wat een project zelf moet beslissen, werk dan ook
`werkwijze/docs/architectuur/stack-profiel.TEMPLATE.md` bij — die template is de canonieke lijst
van die vragen (ADR-0004).

## Geen CI op deze repo

Deze repo bevat documentatie en skills, geen code: er valt niets te testen of te bouwen, dus
staat er geen `.github/` op de root. De CI-checks waar de werkwijze naar verwijst
(`check-generated-types`, `check-frontend-e2e-coverage`, `check-python-style`, `check-ts-style`)
horen in een project dat de werkwijze gebruikt, en bestaan nog niet als uitgeleverde workflow —
zie `BACKLOG.md` §Core (CI/CD per service) en `werkwijze/CLAUDE.md` §Een nieuw project
starten.
