# TSV Wartenberg — Digitalisierung

Zentrale Dokumentation fuer alle digitalen Projekte des TSV Wartenberg e.V.

## Vision

Die Vereinsprozesse des TSV Wartenberg werden schrittweise digitalisiert — nicht durch eine grosse monolithische App, sondern durch **kleine, spezialisierte Anwendungen**, die jeweils eine klar abgegrenzte Aufgabe erfuellen. Eine zentrale Benutzerverwaltung (`tsv-auth`) verbindet alle Apps.

## App-Landschaft

| App | Beschreibung | Technologie | Repo | Status |
|-----|-------------|-------------|------|--------|
| **tsv-auth** | Zentrale Benutzerverwaltung, OAuth2/JWT Provider | FastAPI + PostgreSQL | [tsv-auth](https://github.com/tsv-wartenberg/tsv-auth) | Geplant |
| **tsv-hub** | Vereinsmanagement (Mitglieder, Events, Material, Buchungen) | FastAPI + React | [tsv-hub](https://github.com/tsv-wartenberg/tsv-hub) | In Entwicklung |
| **tsv-helferhub** | Helfer-App (Schichten, Anmeldungen) | Base44 | [helferhub.base44.app](https://helferhub.base44.app) | In Entwicklung |
| **tsv-dashboard** | Zentrales Dashboard, aggregiert alle Apps | React | — | Geplant |
| **tsv-website** | Vereinswebsite tsv-wartenberg.de | TYPO3 v13 | [tsv-website](https://github.com/tsv-wartenberg/tsv-website) | Produktiv |
| **tsv-website-server** | TYPO3 Server-Konfiguration | Docker | — | Produktiv |

## Architekturdiagramm

```
                    ┌──────────────────────────────┐
                    │     tsv-auth (User-Mgmt)     │
                    │   OAuth2 / JWT Provider      │
                    └──────────┬───────────────────┘
                               │ JWT
            ┌──────────────────┼────────────────────┐
            │                  │                    │
   ┌────────▼────────┐  ┌──────▼─────────┐  ┌───────▼────────┐
   │  tsv-dashboard  │  │  tsv-helferhub │  │  tsv-hub       │
   │  React SPA      │  │  Base44 App    │  │  FastAPI+React │
   └─────────────────┘  └────────────────┘  └────────────────┘
            │
   ┌────────▼────────┐
   │  tsv-website    │
   │  TYPO3          │
   └─────────────────┘
```

## Dokumentationsstruktur

- [`architektur/`](architektur/) — Architekturuebersicht, ADRs, Auth-Konzept, Base44-Bewertung
- [`infrastruktur/`](infrastruktur/) — Server, DNS, Hosting, Monitoring
- [`prozesse/`](prozesse/) — Entwicklungs-Workflow, Claude Code, GitHub Issues/Projects
- [`konventionen/`](konventionen/) — Repo-Namen, Coding-Standards, API-Design
- [`projekte/`](projekte/) — Status und Details pro App/Projekt
- [`vorlagen/`](vorlagen/) — CLAUDE.md Template, Settings, .gitignore

## Entwicklung mit Claude Code

Alle TSV-Projekte werden mit **Claude Code** (Anthropic CLI) entwickelt. Jedes Repo enthaelt:

- `CLAUDE.md` — Projekt-Intelligence (wird automatisch von Claude geladen)
- `.claude/settings.local.json` — Berechtigungen (lokal, nicht committet)

### Standard-Workflow

Jede Aufgabe folgt: **Plan → Todo → Verify → Doku → Commit**

Ausfuehrliche Dokumentation: [`prozesse/claude-code-workflow.md`](prozesse/claude-code-workflow.md)

### Vorlagen fuer neue Repos

- [`vorlagen/CLAUDE.md.template`](vorlagen/CLAUDE.md.template) — CLAUDE.md Vorlage
- [`vorlagen/claude-settings-template.json`](vorlagen/claude-settings-template.json) — Berechtigungen
- [`vorlagen/gitignore-template`](vorlagen/gitignore-template) — .gitignore mit `.claude/` Ausschluss

## Projektarbeit mit GitHub

Aufgaben werden ueber **GitHub Issues** verwaltet. Claude Code kann Issues direkt lesen und abarbeiten:

```bash
gh issue view 42          # Aufgabe lesen
# → Claude erstellt Plan, implementiert, committet mit "closes #42"
```

**GitHub Projects** (Kanban-Board) dient als visuelle Uebersicht. Issues aus allen Repos werden einem zentralen Project zugeordnet.

Ausfuehrliche Dokumentation: [`prozesse/projektarbeit-github.md`](prozesse/projektarbeit-github.md)

## Infrastruktur

| Umgebung | Anbieter | Zweck |
|----------|----------|-------|
| Produktion | Hetzner Cloud CX22 (Nuernberg) | tsv-hub, tsv-auth |
| Dev/Demo | Scaleway Serverless (Paris) | API + Frontend Previews |
| Website | Strato (TYPO3-Hosting) | tsv-wartenberg.de |
| DNS | Strato | tsv-wartenberg.de, tsv-hub.tsv-wartenberg.de |

## Kontakt

| Rolle | Person |
|-------|--------|
| Projektleitung / Backend / DevOps | Christian Koehler |
| Frontend-Entwicklung | [zu besetzen] |
| Fachliche Anforderungen | Spartenvertreter |
