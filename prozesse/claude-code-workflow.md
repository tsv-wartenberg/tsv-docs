# Entwicklung mit Claude Code — Standardprozess

## Ueberblick

Alle TSV-Projekte werden mit **Claude Code** (Anthropic CLI) entwickelt. Die Entwicklung erfolgt durch mehrere Personen. Damit alle Entwickler konsistent und effizient mit Claude arbeiten koennen, folgt jedes Projekt einem einheitlichen Setup und Workflow.

## Pflichtdateien pro Repository

Jedes `tsv-*` Repository **muss** folgende Dateien enthalten:

```
tsv-<name>/
├── CLAUDE.md                    # Projekt-Intelligence fuer Claude Code
├── .claude/
│   └── settings.local.json      # Berechtigungen (git-ignored)
├── README.md                    # Projekt-Uebersicht + Setup-Anleitung
├── .env.example                 # Umgebungsvariablen (keine Secrets!)
└── .gitignore                   # Inkl. .claude/ Ausschluss
```

### CLAUDE.md — Projekt-Intelligence

Die `CLAUDE.md` ist die **zentrale Wissensbasis** fuer Claude Code in jedem Repo. Sie wird automatisch geladen und gibt Claude den vollstaendigen Projektkontext. Inhalt:

1. **Projektuebersicht** — Was macht diese App, wer nutzt sie
2. **Tech-Stack** — Sprachen, Frameworks, Versionen
3. **Projektstruktur** — Verzeichnisbaum mit Erklaerungen
4. **Datenmodell** — Entitaeten, Beziehungen (falls relevant)
5. **API-Konventionen** — Endpunkte, Auth, Fehlerformat
6. **Coding-Konventionen** — Linting, Formatting, Namensgebung
7. **Entwicklungsprozess** — Verweis auf den Standard-Workflow (dieses Dokument)
8. **Infrastruktur** — Deployment, Umgebungen, URLs
9. **Sicherheit** — Secrets-Handling, CORS, Env-Variablen

### .claude/settings.local.json

Definiert erlaubte Befehle fuer Claude Code. Wird **nicht** committet (in `.gitignore`). Jeder Entwickler richtet seine Berechtigungen lokal ein. Eine Vorlage liegt unter `tsv-docs/vorlagen/claude-settings-template.json`.

## Standard-Workflow: Plan → Todo → Doku → Commit

Jede Aufgabe folgt einem festen Ablauf:

```
┌─────────────────────────────────────────────────┐
│                  AUFGABE                        │
└─────────┬───────────────────────────────────────┘
          │
          ▼
┌─────────────────────┐
│  1. PLAN            │  Claude Code: /plan oder Plan Mode
│  - Anforderungen    │  → Recherche im Codebase
│  - Architektur      │  → Abgleich mit CLAUDE.md
│  - Plan-Datei       │  → User-Rueckfragen klaeren
└─────────┬───────────┘
          │ Plan genehmigt
          ▼
┌─────────────────────┐
│  2. TODO            │  Claude Code: TaskCreate
│  - Aufgabe in       │  → Einzelne Schritte definieren
│    Schritte teilen  │  → Abhaengigkeiten markieren
│  - Pro Schritt:     │  → Status tracken
│    implement+test   │
└─────────┬───────────┘
          │ Schritt abgeschlossen
          ▼
┌─────────────────────┐
│  3. VERIFY          │  Tests laufen lassen
│  - Tests gruen      │  Linting pruefen
│  - Linting sauber   │  Build pruefen
│  - Build OK         │
└─────────┬───────────┘
          │ Alles gruen
          ▼
┌─────────────────────┐
│  4. DOKU            │  CLAUDE.md aktualisieren (falls noetig)
│  - CLAUDE.md        │  API-Spec aktualisieren
│  - API-Spec         │  ADR schreiben (bei Architektur-Aenderungen)
│  - ADR (optional)   │
└─────────┬───────────┘
          │
          ▼
┌─────────────────────┐
│  5. COMMIT          │  Conventional Commits
│  - Conventional     │  feat: / fix: / docs: / chore:
│    Commit Message   │  Nur geaenderte Dateien stagen
│  - Push             │
└─────────┬───────────┘
          │
          ▼
┌─────────────────────┐
│  6. NAECHSTER TODO  │  TaskUpdate → completed
│  - Todo als done    │  Naechsten Schritt beginnen
│    markieren        │
│  - Weiter bei 2.    │
└─────────────────────┘
```

### Regeln

1. **Nie Code schreiben ohne Plan**: Erst verstehen, dann implementieren
2. **Kleine Schritte**: Jeder Todo-Schritt soll in sich abgeschlossen und testbar sein
3. **Tests vor Commit**: Kein Commit ohne gruene Tests
4. **Doku sofort**: Nicht "spaeter dokumentieren" — nach jedem abgeschlossenen Todo
5. **Conventional Commits**: Immer `feat:`, `fix:`, `docs:`, `chore:`, etc.
6. **CLAUDE.md aktuell halten**: Wenn sich die Architektur aendert, CLAUDE.md anpassen

### Beispiel-Ablauf

```
User: "Fuege iCal-Export fuer Events hinzu"

1. PLAN
   Claude: Recherchiert bestehende Event-Routen, Schema, Tests
   Claude: Erstellt Plan → ical-export.md
   User: Genehmigt Plan

2. TODO
   #1 ical_service.py erstellen (ics-Format generieren)
   #2 Router-Endpunkt GET /events/{id}/ical
   #3 Router-Endpunkt GET /events/ical (alle)
   #4 Tests schreiben

3. VERIFY (nach jedem Schritt)
   pytest tests/ -v
   ruff check app/

4. DOKU
   api-spec.md: iCal-Endpunkte dokumentieren
   CLAUDE.md: iCal als implementiert markieren

5. COMMIT
   feat: add iCal export for events (.ics download)

6. NAECHSTER TODO → Schritt #2, #3, #4...
```

## Multi-User Hinweise

### Wenn mehrere Entwickler an einem Repo arbeiten

- **CLAUDE.md ist die Single Source of Truth** — alle halten sie aktuell
- **Plan-Dateien** (`.claude/plans/`) sind lokal und werden nicht committet
- **Memory** (`.claude/projects/`) ist benutzerspezifisch und lokal
- **settings.local.json** ist benutzerspezifisch — jeder richtet seine Berechtigungen ein
- **Konflikte in CLAUDE.md**: Bei Merge-Konflikten beide Aenderungen zusammenfuehren

### Neuen Entwickler onboarden

1. Repository klonen
2. `README.md` lesen → Setup durchfuehren
3. `CLAUDE.md` lesen → Projektkontext verstehen
4. `.claude/settings.local.json` aus Vorlage einrichten
5. Ersten Feature-Branch erstellen und loslegen

## Referenzen

- [Konventionen: Repo-Namen](../konventionen/repo-namen.md)
- [Konventionen: Coding](../konventionen/coding.md)
- [Konventionen: API-Design](../konventionen/api-design.md)
- [Architektur: Uebersicht](../architektur/uebersicht.md)
