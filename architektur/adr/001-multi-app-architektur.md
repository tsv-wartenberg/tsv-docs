# ADR-001: Multi-App-Architektur statt Monolith

## Status

Akzeptiert (2026-03-22)

## Kontext

Das TSV-Hub-Projekt wurde als monolithische Full-Stack-App (FastAPI + React) gestartet. Mit wachsenden Anforderungen (Helfer-App auf Base44, TYPO3-Website, zukuenftige Module) wird eine flexiblere Architektur benoetigt.

## Entscheidung

Wir wechseln von einer monolithischen Anwendung zu einer Multi-App-Architektur mit unabhaengigen, spezialisierten Applikationen:

- Jede App hat ein eigenes Git-Repository
- Jede App kann eine eigene Technologie nutzen
- Alle Apps teilen sich eine zentrale Benutzerverwaltung (tsv-auth)
- Kommunikation ausschliesslich ueber REST-APIs

## Konsequenzen

- **Positiv**: Flexibilitaet, unabhaengige Deployments, kleinere Codebasen
- **Negativ**: Mehr Infrastruktur-Overhead, Koordination zwischen Repos
- **Risiko**: Auth-Service ist Single Point of Failure
