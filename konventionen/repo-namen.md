# Repository-Namenskonventionen

## Schema

```
tsv-<name>
```

- **Praefix**: Immer `tsv-`
- **Name**: Kleinbuchstaben, Bindestrich als Trennzeichen
- **Sprache**: Englisch fuer technische Begriffe, Deutsch erlaubt fuer fachliche
- **Laenge**: Moeglichst kurz und beschreibend (max. 3 Woerter)

## Beispiele

| Gut | Schlecht |
|-----|---------|
| `tsv-auth` | `TSV_Auth_Service` |
| `tsv-hub` | `TSV Vereinshub` |
| `tsv-website` | `web_tsv_wartenberg_bootstrap` |
| `tsv-dashboard` | `tsv-central-dashboard-app` |

## Repository-Grundausstattung

Jedes Repo muss enthalten:

- `README.md` — Projektuebersicht, Setup-Anleitung, Tech-Stack
- `CLAUDE.md` — KI-Kontext fuer Claude Code / AI-Assistenten
- `.env.example` — Umgebungsvariablen-Vorlage (keine Secrets!)
- `.gitignore` — Sprachspezifisch
- `LICENSE` — Falls Open Source

## Branch-Strategie

```
main        → Produktion (geschuetzt, nur via PR)
develop     → Entwicklung (Integrations-Branch)
feature/xxx → Feature-Branches (kurzlebig)
fix/xxx     → Bugfix-Branches
```

## Commit-Messages

[Conventional Commits](https://www.conventionalcommits.org/):

```
feat: add user registration endpoint
fix: correct JWT expiration calculation
docs: update API specification
chore: upgrade FastAPI to 0.116
refactor: extract auth logic into service layer
test: add RBAC isolation tests
style: apply ruff format
```
