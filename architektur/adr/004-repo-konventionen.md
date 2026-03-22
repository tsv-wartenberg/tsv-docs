# ADR-004: Repository-Namenskonventionen

## Status

Akzeptiert (2026-03-22)

## Kontext

Mit wachsender Anzahl an Repositories brauchen wir einheitliche Benennungsregeln.

## Entscheidung

### Namensschema

Alle TSV-Repositories folgen dem Muster: `tsv-<name>`

- Kleinbuchstaben, Bindestrich als Trennzeichen
- Kurz und beschreibend
- Englisch fuer technische Namen, Deutsch fuer fachliche Begriffe erlaubt

### Aktuelle Repositories

| Repository | Beschreibung |
|-----------|-------------|
| `tsv-docs` | Zentrale Dokumentation |
| `tsv-auth` | Benutzerverwaltung |
| `tsv-hub` | Vereinsmanagement (Mitglieder, Events, Material, Buchungen) |
| `tsv-dashboard` | Zentrales Dashboard |
| `tsv-website` | TYPO3 Sitepackages |
| `tsv-website-server` | TYPO3 Server-Konfiguration |

### Regeln

- Jede App = ein Repository
- Jedes Repo hat ein `README.md` mit Setup-Anleitung
- Jedes Repo hat eine `CLAUDE.md` fuer KI-gestuetzte Entwicklung
- Branch-Strategie: `main` → `develop` → `feature/xxx`
- Commit-Messages: Conventional Commits (`feat:`, `fix:`, `docs:`, `chore:`)

## Konsequenzen

- **Positiv**: Einheitlichkeit, leicht auffindbar, klare Zuordnung
- **Negativ**: Bestehende Repos muessen umbenannt werden
