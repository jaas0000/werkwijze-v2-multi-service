---
name: architectuur-audit
description: >-
  Periodieke, projectbrede controle op drie architecturale zorgen tussen features: duplicatie
  (literaal én semantisch), cohesie (een gedeeld bestand dat te veel domeinen tegelijk draagt),
  en grenzen/naming (dezelfde naam voor twee verschillende concepten, of een concept dat de
  verkeerde feature-map in ligt). Gebruik deze skill bij "zoek duplicatie in de features", "tijd
  voor de architectuurcheck", "past dit nog bij de architectuur", of op vaste cadans zonder dat
  er een specifieke feature of PR aanleiding is. Niet voor losse featurebouw (zie
  `feature-bouwen`) en niet voor het reviewen van één PR (zie `code-review`) — in die twee zijn
  al deze drie zorgen bewust alleen opportunistisch, deze skill is de systematische tegenhanger.
---

# Periodieke architectuur-audit

**Trigger:** vaste cadans (bijvoorbeeld wekelijks), losstaand van een specifieke feature of PR.

## Regels

1. Lees breed: alle mappen onder `api/app/features/`, `api/app/shared/`, en de gedeelde
   verzamelbestanden (`api/app/db.py`, `api/app/main.py`). Doel is uitsluitend deze drie
   zorgen — niet featurecorrectheid beoordelen.

2. **Duplicatie.** Onderscheid **literale duplicatie** (copy-paste — mechanisch te vinden,
   bijvoorbeeld met een los tool) van **semantische duplicatie** (hetzelfde probleem, andere
   implementatie — vraagt conceptueel lezen). Vind je een patroon dat in twee of meer features
   semantisch hetzelfde oplost: de bestemming hangt af van welk geval het is, exact zoals
   `feature-bouwen` regel 8 die tweedeling beschrijft — een gedeelde module in
   `api/app/shared/<naam>.py` als het patroon geen natuurlijke eigenaar heeft, of een openbare
   functie op de eigenaar-feature als het bij één bestaande entiteit hoort. Herschrijf de
   betrokken features om de gekozen route te gebruiken, en voeg in elke betrokken story de
   passende terugverwijzing toe (zie regel 8 voor het exacte formaat). Vind je precies één
   voorkomen van een patroon: laat het staan — duplicatie is pas een probleem ná de tweede,
   onafhankelijke implementatie (zie `feature-bouwen` regel 8).

3. **Cohesie.** Groeit `db.py` of `main.py` naar meer dan een dun samenvoegpunt (tabellen,
   routelogica, of domeinkennis die er niet hoort, zie `feature-bouwen` regel 2)? Groeit een
   feature-map zelf zo groot dat hij meerdere, los van elkaar te ontwikkelen sub-concepten
   bevat? Signaleer dit — een groeiend verzamelpunt (een centraal bestand dat gestaag steeds
   meer domeinen erbij krijgt, elk op zich klein) is vaak het eerste, sluipende teken dat een
   indeling niet meer klopt: het went per toevoeging, en valt pas op als het al honderden
   regels beslaat.

4. **Grenzen/naming.** Twee features die onafhankelijk van elkaar dezelfde naam gebruiken voor
   verschillende concepten (een klassecollisie), of een class/functie die inhoudelijk bij een
   andere feature hoort dan waar hij fysiek staat. Verifieer bij twijfel of een concept wel in
   de juiste `features/<naam>/` map staat (`feature-bouwen` regel 2). Hoort hier ook bij: een
   entiteit die door een tweede feature als harde afhankelijkheid (cross-feature FK, of een
   directe import) wordt gebruikt — dat is op zichzelf geen probleem (zie `feature-bouwen`
   regel 8's owner-export-route), maar bij een *derde* feature die dezelfde entiteit zo nodig
   heeft, is dat een signaal dat het concept eigenlijk geen eigenaar-feature meer heeft en naar
   een eigen, gedeelde plek zou moeten (niet meer "eigendom van de oorspronkelijke feature").

5. Rapporteer bevindingen per zorg apart (duplicatie / cohesie / grenzen). Voer een
   niet-triviale verplaatsing of hernoeming niet door zonder controle (een mens, of een aparte
   reviewstap) — deze skill signaleert, en herschrijft alleen wat evident veilig is. Iets dat je
   signaleert maar niet direct oplost: zet direct in `docs/vervolgpunten.md` (er is geen PR om
   het aan op te hangen zoals bij `code-review`). Is een bevinding zelf een projectbrede
   technische keuze (niet alleen een code-verplaatsing — bijvoorbeeld "we accepteren deze
   duplicatie bewust, want een gedeelde abstractie zou hier de verkeerde koppeling
   introduceren"): leg die vast als `docs/architectuur/adr/<nummer>-<naam>.md`, niet alleen als
   vervolgpunt — een vervolgpunt is een open item, een ADR is een gemaakte keuze met reden.

6. **Registreer dat deze ronde heeft plaatsgevonden**, ook als er niets te melden was: een regel
   in `docs/vervolgpunten.md` met de datum en "architectuur-audit gedraaid, geen bevindingen"
   (of de bevindingen zelf, als die er al onder regel 5 in staan). Zonder dit spoor is een
   cadans die stilvalt niet te onderscheiden van een cadans die niets vond.
