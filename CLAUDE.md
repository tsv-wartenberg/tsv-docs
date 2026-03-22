# tsv-docs — Zentrale Dokumentation

## Projektuebersicht

Zentrale Dokumentation fuer alle digitalen Projekte des TSV Wartenberg e.V. Dieses Repo enthaelt keine Anwendung, sondern ausschliesslich Markdown-Dokumentation: Architektur, ADRs, Prozesse, Konventionen, Infrastruktur und Projekt-Steckbriefe.

**Betreiber:** TSV Wartenberg e.V., 85456 Wartenberg, Bayern

## Projektstruktur

```
tsv-docs/
├── CLAUDE.md                    ← Du bist hier
├── README.md                    ← Projektuebersicht + App-Landschaft
├── architektur/
│   ├── uebersicht.md            ← Architekturdiagramm, Designprinzipien
│   ├── adr/                     ← Architecture Decision Records
│   │   ├── 001-multi-app-architektur.md
│   │   ├── 002-zentrale-benutzerverwaltung.md
│   │   ├── 003-hosting-strategie.md
│   │   └── 004-repo-konventionen.md
│   └── auth-konzept.md          ← (geplant) OAuth2/JWT Flow
├── infrastruktur/
│   └── uebersicht.md            ← Server, DNS, Domains, Backup
├── prozesse/
│   ├── entwicklung.md           ← Git-Workflow, CI/CD
│   └── claude-code-workflow.md  ← Standard-Workflow fuer Claude Code
├── konventionen/
│   ├── repo-namen.md            ← tsv-* Praefix, Benennungsregeln
│   ├── coding.md                ← Sprachen, Linting, Formatting
│   └── api-design.md            ← REST-Standards, Auth, Fehlerformat
├── projekte/
│   ├── tsv-hub.md               ← Vereinsmanagement
│   ├── tsv-auth.md              ← Benutzerverwaltung
│   ├── tsv-helferhub.md         ← Base44 Helfer-App
│   ├── tsv-website.md           ← TYPO3 Website
│   └── tsv-dashboard.md         ← Zentrales Dashboard
├── vorlagen/
│   ├── CLAUDE.md.template       ← Vorlage fuer CLAUDE.md in neuen Repos
│   ├── claude-settings-template.json ← Vorlage fuer .claude/settings.local.json
│   └── gitignore-template       ← .gitignore Vorlage
└── legacy/
    ├── ANLEITUNG-v2-UMSCHALTUNG.md
    └── TSV-Wartenberg-Design-Analyse.md
```

## Konventionen

### Dokumentations-Sprache
- **Deutsch** fuer alle Inhalte (Vereinskontext)
- **Umlaute vermeiden** in Dateinamen (ue statt ue)
- **Markdown** als Format, keine Word/PDF-Dateien

### Neue Dokumente anlegen
- ADRs: Nummeriert (`NNN-kurztitel.md`), Status: Akzeptiert/Abgelehnt/Ersetzt
- Projekte: Ein Steckbrief pro App (`tsv-<name>.md`)
- Prozesse: Beschreibend, mit Ablaufdiagrammen wo sinnvoll

### Aenderungen
- Jede Aenderung committen mit `docs:` Praefix
- README.md aktualisieren wenn neue Apps/Repos dazukommen

## Entwicklungsprozess

Dieses Projekt folgt dem TSV-Standard-Workflow:

**Plan → Todo → Verify → Doku → Commit**

Details: [prozesse/claude-code-workflow.md](prozesse/claude-code-workflow.md)

## Architektur-Kontext

Dieses Repo dokumentiert die TSV-Multi-App-Architektur:
- **tsv-auth**: Zentrale Benutzerverwaltung (JWT/OAuth2) — geplant
- **tsv-hub**: Vereinsmanagement (Mitglieder, Events, Material, Buchungen)
- **tsv-helferhub**: Helfer-App auf Base44 (helferhub.base44.app)
- **tsv-dashboard**: Zentrales Dashboard — geplant
- **tsv-website**: Vereinswebsite (TYPO3)

Alle Repos: [github.com/ckoehler99](https://github.com/ckoehler99?tab=repositories&q=tsv-)
