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

  Elke submap onder `voorbeeld/` is zelf het kopieerbare startpunt voor een nieuw project met
  die architectuur — niet `voorbeeld/` als geheel.

## Hoe gebruik je dit

1. Fork of clone deze repo.
2. Gebruik `voorbeeld/wetsanalyse/` als startpunt voor een nieuw multi-service project — kopieer
   de inhoud naar een nieuwe repo.
3. Zet de agent-root op een map die **beide** repo's bevat — de werkwijze-repo én je nieuwe
   project-repo — zodat de AI de methodologie kan lezen terwijl ze aan het project werkt.
4. Kopieer `werkwijze/.claude/skills/` naar `<agent-root>/.claude/skills/` zodat de skills
   beschikbaar zijn vanuit elke submap in de workspace.

## Bij wijzigingen in de werkwijze

Pas `werkwijze/CLAUDE.md` en de relevante skill(s) aan, en werk `voorbeeld/wetsanalyse/`
bij zodat het de gewijzigde werkwijze blijft illustreren. Zo blijven de voorbeelden altijd
actuele startpunten.

## Geen CI op deze repo

Zie `werkwijze/CLAUDE.md` §Een nieuw project starten voor de kanttekening over GitHub Actions
en Dependabot zolang `voorbeeld/wetsanalyse/` een submap is in plaats van een eigen repo.
