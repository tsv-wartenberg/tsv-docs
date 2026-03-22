# Entwicklungsprozess

## Arbeitsweise

- **Sprints**: 2 Wochen
- **Kommunikation**: Signal/WhatsApp-Gruppe
- **Aufgabenmanagement**: GitHub Issues + Projects (Kanban)
- **Code-Reviews**: Jeder PR wird von mindestens einer Person reviewed
- **KI-gestuetzt**: Claude Code mit CLAUDE.md fuer Kontext

## Git-Workflow

```
main ← develop ← feature/xxx
```

1. Feature-Branch von `develop` erstellen
2. Entwickeln + Tests schreiben
3. PR gegen `develop` erstellen
4. Code-Review + CI gruen
5. Merge in `develop`
6. Regelmaessig `develop` → `main` (Release)

## Lokale Entwicklung

Jede App hat ein eigenes Setup — siehe jeweiliges `README.md`:

```bash
cd tsv-wartenberg/tsv-hub        # oder tsv-auth, tsv-dashboard
cat README.md                     # Setup-Anleitung
```

## CI/CD

- GitHub Actions pro Repository
- `develop` → Dev/Demo (Scaleway)
- `main` → Produktion (Hetzner)
