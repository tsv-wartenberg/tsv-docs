# ADR-005: GitHub Organisation fuer TSV Wartenberg

## Status

Akzeptiert (2026-03-22)

## Kontext

Die TSV-Repos liegen aktuell unter dem persoenlichen Account `github.com/tsv-wartenberg`. Mit wachsender Anzahl an Repos und Entwicklern wird eine zentrale Organisation benoetigt.

## Entscheidung

Eine **GitHub Organisation** `tsv-wartenberg` wird erstellt (Free Plan, kostenlos).

### Vorteile

- Alle `tsv-*` Repos unter einem Dach (`github.com/tsv-wartenberg/tsv-hub`)
- Team-Verwaltung: Entwickler einladen/entfernen ohne persoenliche Account-Bindung
- Gemeinsame GitHub Projects ueber alle Repos hinweg
- Professioneller Auftritt fuer den Verein
- Kostenlos im Free Plan (unbegrenzte Repos + Collaborators)

### Free Plan Limits

- 2.000 CI/CD Minuten/Monat (ausreichend fuer unsere Groesse)
- 500 MB Packages Storage
- Keine Protected Branches (erst ab Team Plan $4/User/Monat)

### Migration

1. Organisation `tsv-wartenberg` auf GitHub erstellen
2. Repos einzeln transferieren (`Settings → Transfer ownership`)
3. Lokale Remotes aktualisieren: `git remote set-url origin https://github.com/tsv-wartenberg/tsv-hub.git`
4. CI/CD Secrets in der Organisation neu anlegen

## Konsequenzen

- **Positiv**: Zentrale Verwaltung, Team-Skalierung, saubere URLs
- **Negativ**: Einmaliger Migrationsaufwand, CI/CD Secrets muessen neu gesetzt werden
