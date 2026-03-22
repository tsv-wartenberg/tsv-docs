# Architekturuebersicht

## Designprinzipien

1. **Multi-App statt Monolith**: Jede Anwendung hat eine klar abgegrenzte Verantwortlichkeit
2. **Zentrale Authentifizierung**: `tsv-auth` ist der einzige Identity Provider
3. **API-First**: Alle Apps kommunizieren ueber REST-APIs
4. **Technologie-Freiheit**: Jede App kann den besten Stack fuer ihren Zweck nutzen
5. **Unabhaengige Deployments**: Jede App hat eigenes Repo, eigene CI/CD, eigenes Deployment

## Kommunikation zwischen Apps

```
┌───────────┐     JWT       ┌───────────┐
│  Client   │──────────────▶│ tsv-auth  │
│ (Browser) │◀──────────────│           │
└─────┬─────┘   Token       └───────────┘
      │                          │
      │ Bearer Token             │ JWKS (Public Key)
      │                          │
      ▼                          ▼
┌─────────────────┐         ┌──────────────────┐
│ tsv-hub         │         │ tsv-xyz          │
│                 │         │                  │
│ Verifiziert JWT │         │ Verifiziert JWT  │
│ via JWKS        │         │ via JWKS         │
└─────────────────┘         └──────────────────┘
```

### Ablauf

1. User oeffnet eine App (z.B. tsv-hub)
2. App leitet zum Login auf tsv-auth weiter
3. tsv-auth authentifiziert und gibt JWT zurueck
4. App sendet JWT bei jedem API-Request mit
5. Backend verifiziert JWT ueber den Public Key von tsv-auth (`/.well-known/jwks.json`)

## Datenhoheit

| Daten | Verantwortliche App |
|-------|-------------------|
| User, Rollen, Sparten | tsv-auth |
| Mitglieder-Stammdaten | tsv-hub |
| Events, Kalender | tsv-hub |
| Material, Buchungen | tsv-hub |
| Helferschichten, Anmeldungen | tsv-helferhub (Base44) |
| Website-Inhalte | tsv-website (TYPO3) |

## Vor- und Nachteile

### Vorteile

- **Unabhaengige Entwicklung**: Jede App separat entwickeln, deployen, skalieren
- **Kleine Codebasen**: Einfacher zu verstehen, testen und warten
- **Fehler-Isolation**: Ausfall einer App beeintraechtigt nicht die anderen
- **Team-Skalierung**: Verschiedene Personen an verschiedenen Apps
- **Technologie-Freiheit**: Base44, React, TYPO3 nebeneinander

### Nachteile

- **Mehr Infrastruktur**: Jede App braucht eigenes Deployment
- **Auth als SPOF**: tsv-auth muss hochverfuegbar sein
- **Daten-Sync**: Keine gemeinsame DB, Konsistenz ueber APIs
- **Overhead**: Fuer ehrenamtliches Team evtl. viele bewegliche Teile

### Risikominimierung

- Anzahl Apps auf 3-5 begrenzen
- Gemeinsame Infrastruktur (Docker Compose, Hetzner)
- tsv-auth einfach und stabil halten
- Monitoring ueber alle Apps (Prometheus/Grafana)
