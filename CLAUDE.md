# werkwijze-v2-multi-service

**v2** — gericht op multi-service applicaties (meerdere deploybare services, Next.js BFF,
LLM-orkestratie, async achtergrondtaken). Bouwt voort op de principes van v1 maar de patronen
lopen uiteen. Zie ook: [`werkwijze-v1-contract-first`](../werkwijze-v1-contract-first/) voor de
enkelvoudige backend/frontend-stack.

Deze repo bestaat uit twee mappen:

- **`werkwijze/`** — de methodologie: skills, het hoofddocument en ADR's over de werkwijze zelf.
  Zie `werkwijze/CLAUDE.md` voor de volledige methodologie-uitleg.
- **`voorbeeld/`** — werkende referentie-implementaties die de werkwijze in de praktijk laten
  zien, elk in een eigen submap met een eigen stack-profiel:
  - `wetsanalyse/` — referentie-implementatie voor v2: meerdere deploybare services, Next.js BFF,
    LLM-orkestratie, async achtergrondtaken. Nog niet volledig uitgebouwd; zie
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

`.github/` staat onder `voorbeeld/wetsanalyse/` (waar het inhoudelijk bij hoort — CI en
dependabot gaan over die referentie-implementatie, niet over de methodologie), maar GitHub leest
workflows en dependabot-config uitsluitend van de root van een repository. Zolang
`voorbeeld/wetsanalyse/` hier een submap is, draait er dus geen CI en scant Dependabot niets op
déze repo. Dat is bewust geaccepteerd totdat `voorbeeld/wetsanalyse/` als eigen repo bestaat —
zie `werkwijze/CLAUDE.md` §Een nieuw project starten.
