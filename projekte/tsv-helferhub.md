# tsv-helferhub — Helfer-App

## Uebersicht

Helfer-App fuer die Verwaltung von Helferschichten bei Vereinsveranstaltungen. Gebaut auf Base44 (AI-powered App Builder).

| Feld | Wert |
|------|------|
| **Plattform** | Base44 |
| **App-URL** | [helferhub.base44.app](https://helferhub.base44.app) |
| **App-ID** | `699788636dd9fc3c5e8a3a25` |
| **Status** | In Entwicklung |
| **Erstellt von** | Externer User (Base44-Account) |

## Integration

- **SDK**: `@base44/sdk` (npm) fuer externen API-Zugriff
- **Dashboard-Widget**: In tsv-hub/tsv-dashboard eingebettet
- **Entity-Discovery**: Schemas muessen ueber SDK ausgelesen werden (Base44-Login erforderlich)

## Offene Punkte

- [ ] Base44-Login/Zugang fuer TSV-Hub-Integration beschaffen
- [ ] Entity-Schemas dokumentieren
- [ ] Auth-Integration mit tsv-auth klaeren (Base44 hat eigenes User-System)
