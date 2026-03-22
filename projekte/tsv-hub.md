# tsv-hub — Vereinsmanagement

## Uebersicht

Modulare Webplattform fuer den TSV Wartenberg. Backend-API mit vier Fachmodulen + React-Frontend.

| Feld | Wert |
|------|------|
| **Repo** | [github.com/tsv-wartenberg/tsv-hub](https://github.com/tsv-wartenberg/tsv-hub) |
| **Technologie** | FastAPI + React + PostgreSQL |
| **Status** | In Entwicklung (Phase 3 abgeschlossen) |
| **Dev-URL** | https://tsv-hub-bucket.s3-website.fr-par.scw.cloud |
| **Dev-API** | https://tsvhubdevsnreqyhh-tsv-hub-api-dev.functions.fnc.fr-par.scw.cloud |

## Module

- **Mitglieder**: CRUD, Spartenzuordnung, Spartenfilter
- **Events/Kalender**: CRUD, Statusverwaltung, Datumsfilter
- **Helfer**: Schichten, Zuordnungen, Selbstanmeldung
- **Material**: Inventar, Zustandsverwaltung
- **Buchungen**: Einnahmen/Ausgaben, Zusammenfassung

## RBAC

Vollstaendige spartenbasierte Zugriffskontrolle:
- Admin sieht alles
- Spartenleiter nur eigene Sparten
- Mitglied nur eigenes Profil

## Zukuenftige Aenderung

Auth wird nach tsv-auth migriert. tsv-hub wird reiner Fach-Service.
