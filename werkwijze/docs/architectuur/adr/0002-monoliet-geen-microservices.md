# ADR-0002: Eén monolithische API, geen microservices

**Status:** geaccepteerd — geldt voor het `voorbeeld/` met een single-service stack (v1-context);
v2-projecten met meerdere services vervangen dit ADR door een eigen topologie-beslissing.
**Datum:** 2026-08-10 <!-- met terugwerkende kracht vastgelegd -->

## Context

Vertical slicing (ADR-0001) maakt features al onderling losgekoppeld op codeniveau. Dat roept
de vraag op of ze ook als aparte, los te deployen services zouden moeten draaien.

## Beslissing

Alle features draaien in één FastAPI-app (`api/app/main.py`), gedeeld één databaseproces
(`api/app/db.py`). Geen aparte services, geen API-contracten tussen services.

## Consequenties

- Eén contract-generatieketen (SQLModel → OpenAPI → TypeScript) is alleen zo eenvoudig als hij
  is omdat alles in één proces draait — cross-service contracten zijn een wezenlijk ander
  probleem (aparte OpenAPI-schema's, versiebeheer tussen services) dat deze werkwijze niet
  oplost, zie `feature-bouwen` §Wat dit niet oplost.
- Vertical slicing blijft de voorbereiding mócht een feature ooit alsnog uitgesplitst worden
  (de featuremap is er al op ingericht), maar dat uitsplitsen zelf is een aparte, hier niet
  gemaakte stap.
- Nadeel, bewust geaccepteerd: alle features delen dezelfde uptime en dezelfde
  schaal-eigenschappen. Voor een klein voorbeeldproject (en de meeste projecten die deze
  werkwijze als startpunt gebruiken) weegt dat niet op tegen de complexiteit van aparte
  services.
