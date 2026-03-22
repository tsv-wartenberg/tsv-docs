# Branching-Strategie und Staging-Konzept

## Branch-Modell

```
main          Produktion — laeuft auf Hetzner
  │
  ├── develop     Entwicklung / Staging — laeuft auf Scaleway (DEV)
  │     │
  │     ├── feature/xxx    Neues Feature
  │     ├── fix/xxx        Bugfix
  │     └── chore/xxx      Wartung, Refactoring
  │
  └── (hotfix/xxx)         Kritischer Prod-Fix (direkt von main, selten)
```

### Branch-Regeln

| Branch | Zweck | Push direkt? | Deployment |
|--------|-------|-------------|------------|
| `main` | Produktion | **Nein** — nur via PR von `develop` | Hetzner (automatisch via CI) |
| `develop` | Integration / Staging | **Nein** — nur via PR von Feature-Branches | Scaleway DEV (automatisch via CI) |
| `feature/*` | Feature-Entwicklung | Ja (eigener Branch) | — |
| `fix/*` | Bugfixes | Ja (eigener Branch) | — |
| `chore/*` | Wartung | Ja (eigener Branch) | — |
| `hotfix/*` | Kritischer Prod-Fix | Ja, PR direkt gegen `main` | Hetzner (nach Merge) |

### Enforcement

| Repo | Technisch erzwungen? | Methode |
|------|---------------------|---------|
| tsv-docs | Ja | GitHub Ruleset (public Repo) |
| tsv-website | Ja | GitHub Ruleset (public Repo) |
| tsv-hub | **Nein** (private Repo, Free Plan) | Per Konvention + Claude-Anweisungen |
| tsv-auth | **Nein** (private Repo, Free Plan) | Per Konvention + Claude-Anweisungen |

> **Konvention:** Auch ohne technisches Enforcement gilt: **Nie direkt auf `main` oder `develop` pushen.** Immer Feature-Branch + PR. Claude Code ist entsprechend instruiert (siehe Issue-Templates).

## Staging-Umgebungen

### DEV (Scaleway)

```
Branch: develop
URL:    https://tsv-hub-dev.ck99.io (Frontend)
API:    https://tsvhubdevsnreqyhh-tsv-hub-api-dev.functions.fnc.fr-par.scw.cloud
DB:     Scaleway Serverless SQL (PostgreSQL)
```

**Zweck:**
- Automatisches Deployment bei jedem Merge in `develop`
- Testen neuer Features vor dem Release
- Demo-Umgebung fuer Spartenleiter und Tester
- Seed-Daten fuer Tests (Demo-Accounts)

**Wer hat Zugriff:**
- Alle Entwickler
- Ausgewaehlte Spartenvertreter (fuer Feedback)

### PROD (Hetzner)

```
Branch: main
URL:    https://tsv-hub.tsv-wartenberg.de
API:    https://tsv-hub.tsv-wartenberg.de/api/v1
DB:     PostgreSQL 16+ (Docker Container auf Hetzner CX22)
```

**Zweck:**
- Produktivbetrieb fuer alle Vereinsmitglieder
- Echte Daten, echte Nutzer
- Backup: taeglich 03:00 UTC via pg_dump

**Wer hat Zugriff:**
- Alle Vereinsmitglieder (je nach Rolle)

## Ablauf: Feature entwickeln und deployen

```
1. Issue erstellen / zugewiesen bekommen
   │
   ▼
2. Feature-Branch von develop erstellen
   git checkout develop && git pull
   git checkout -b feature/ical-export
   │
   ▼
3. Entwickeln (Plan → Todo → Verify → Doku → Commit)
   │
   ▼
4. PR gegen develop erstellen
   gh pr create --base develop --title "feat: ..." --body "closes #42"
   │
   ▼
5. Review (mindestens Selbst-Review, besser: zweite Person)
   │
   ▼
6. Merge in develop → Automatisches Deployment auf DEV
   │
   ▼
7. Testen auf DEV-Umgebung
   │
   ▼
8. Wenn stabil: PR von develop → main erstellen
   gh pr create --base main --head develop --title "release: v0.x.y"
   │
   ▼
9. Merge in main → Automatisches Deployment auf PROD
```

## Ablauf: Hotfix (kritischer Prod-Bug)

```
1. Hotfix-Branch von main erstellen
   git checkout main && git pull
   git checkout -b hotfix/login-broken
   │
   ▼
2. Fix implementieren + Test
   │
   ▼
3. PR gegen main UND develop erstellen
   gh pr create --base main --title "fix: ..."
   │
   ▼
4. Nach Merge in main: develop ebenfalls aktualisieren
   git checkout develop && git merge main
```

## CI/CD Pipeline (pro App)

```yaml
# .github/workflows/deploy-dev.yml
on:
  push:
    branches: [develop]
# → Build, Test, Deploy auf Scaleway

# .github/workflows/deploy-prod.yml
on:
  push:
    branches: [main]
# → Build, Test, Deploy auf Hetzner
```

### Pipeline-Schritte

1. **Lint**: `ruff check` (Backend), `eslint` (Frontend)
2. **Test**: `pytest` (Backend), `vitest` (Frontend)
3. **Build**: Docker Image bauen
4. **Deploy**: Image pushen + Container neustarten

## Claude Code Konvention

Da tsv-hub (private) kein technisches Branch-Enforcement hat, muss Claude Code die Regeln selbst einhalten. Dafuer sorgen:

1. **Issue-Templates**: Enthalten explizite Anweisungen fuer Branch-Erstellung und PR
2. **CLAUDE.md**: Dokumentiert Branch-Strategie und PR-Pflicht
3. **Standard-Workflow**: Plan → Todo → Verify → Doku → Commit → PR

### Claude Code Checkliste (bei jedem Issue)

```
□ Feature-Branch von develop erstellt (nie direkt auf main/develop arbeiten)
□ Conventional Commit Messages verwendet
□ Tests geschrieben und gruen
□ Linting sauber
□ CLAUDE.md aktualisiert (falls Architektur-Aenderung)
□ PR gegen develop erstellt mit "closes #ISSUE_NR"
□ PR-Beschreibung enthaelt Summary + Test Plan
```
