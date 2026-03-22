# Projektarbeit mit GitHub Issues & Projects

## Ueberblick

Die Projektarbeit wird ueber **GitHub Issues** (Aufgaben) und **GitHub Projects** (Kanban-Board) organisiert. Claude Code kann Issues direkt lesen und abarbeiten — deshalb sind Issues die zentrale Einheit fuer Aufgaben.

## GitHub Issues als Aufgabenmanagement

### Warum Issues statt Projects-Tasks

GitHub Projects bieten ein schoenes Kanban-Board, aber die einzelnen Tasks darin sind fuer Claude Code schwer zugaenglich. **Issues** hingegen:

- Koennen von Claude Code direkt gelesen werden: `gh issue view 42`
- Koennen von Claude Code erstellt werden: `gh issue create`
- Koennen kommentiert und geschlossen werden: `gh issue close 42`
- Haben Labels, Assignees, Milestones
- Koennen einem Project-Board zugeordnet werden (beste beider Welten)

### Issue-Workflow mit Claude Code

```
┌─────────────────────────┐
│  1. Issue lesen          │  gh issue view 42
│     Anforderung          │  → Claude versteht die Aufgabe
│     verstehen            │
└──────────┬──────────────┘
           │
           ▼
┌─────────────────────────┐
│  2. Plan erstellen       │  Claude Code: Plan Mode
│     Recherche +          │  → Plan-Datei schreiben
│     Architektur          │  → User genehmigt
└──────────┬──────────────┘
           │
           ▼
┌─────────────────────────┐
│  3. Implementieren       │  Todo-Schritte abarbeiten
│     Test + Verify        │  → Tests + Linting
└──────────┬──────────────┘
           │
           ▼
┌─────────────────────────┐
│  4. Commit + Push        │  Conventional Commit
│     Issue referenzieren  │  → "feat: ... (closes #42)"
└──────────┬──────────────┘
           │
           ▼
┌─────────────────────────┐
│  5. Issue schliessen     │  gh issue close 42
│     oder automatisch     │  → oder via "closes #42" im Commit
└─────────────────────────┘
```

### Claude Code Befehle fuer Issues

```bash
# Issues auflisten
gh issue list
gh issue list --label "bug"
gh issue list --assignee "@me"

# Issue lesen (Claude versteht den Kontext)
gh issue view 42

# Issue erstellen
gh issue create --title "iCal-Export implementieren" \
  --body "Events als .ics-Datei exportieren" \
  --label "feat" --assignee "ckoehler99"

# Issue kommentieren (Fortschritt dokumentieren)
gh issue comment 42 --body "Plan erstellt, starte Implementierung"

# Issue schliessen
gh issue close 42 --comment "Implementiert in commit abc1234"
```

### Typischer Ablauf

```
User:   "Arbeite Issue #42 ab"
Claude: gh issue view 42
        → Liest Titel, Beschreibung, Labels, Kommentare
        → Erstellt Plan basierend auf Issue-Anforderungen
        → Implementiert nach Standard-Workflow
        → git commit -m "feat: add iCal export (closes #42)"
        → gh issue close 42
```

## GitHub Projects (Kanban-Board)

### Setup

Ein **zentrales Project** im tsv-docs Repo (oder in der GitHub Organisation) fuer die Gesamtuebersicht:

| Spalte | Bedeutung |
|--------|-----------|
| **Backlog** | Ideen und zukuenftige Aufgaben |
| **Ready** | Priorisiert, kann gestartet werden |
| **In Progress** | Wird gerade bearbeitet |
| **Review** | PR erstellt, wartet auf Review |
| **Done** | Abgeschlossen |

### Issues dem Project zuordnen

Issues aus verschiedenen Repos koennen dem gleichen Project zugeordnet werden:

```bash
# Issue einem Project zuordnen (ueber GitHub UI oder gh CLI)
gh project item-add PROJECT_NUMBER --owner tsv-wartenberg --url ISSUE_URL
```

### Empfohlene Labels (einheitlich ueber alle Repos)

| Label | Farbe | Bedeutung |
|-------|-------|-----------|
| `feat` | gruen | Neues Feature |
| `fix` | rot | Bugfix |
| `docs` | blau | Dokumentation |
| `chore` | grau | Wartung, Dependencies |
| `refactor` | orange | Code-Umbau ohne neue Funktion |
| `prio:high` | dunkelrot | Hohe Prioritaet |
| `prio:low` | hellgrau | Niedrige Prioritaet |
| `good-first-issue` | lila | Geeignet fuer neue Entwickler |

## Milestones

Pro Release oder Sprint einen Milestone anlegen:

```bash
gh api repos/tsv-wartenberg/tsv-hub/milestones \
  -f title="MVP Go-Live" -f due_on="2026-09-01T00:00:00Z"
```

Issues dem Milestone zuordnen fuer Sprint-Planung.

## Zusammenfassung

| Werkzeug | Zweck | Claude Code Zugriff |
|----------|-------|-------------------|
| **Issues** | Einzelne Aufgaben, Bugs, Features | Direkt via `gh issue` |
| **Projects** | Visuelle Uebersicht (Kanban) | Eingeschraenkt (nur `gh project`) |
| **Milestones** | Sprint-/Release-Planung | Via `gh api` |
| **Labels** | Kategorisierung | Via `gh issue create --label` |

**Faustregel:** Issues sind die Waehrung — Projects sind die Vitrine.
