# ADR-006: Zitadel als zentrale Authentifizierungsloesung

## Status

Vorgeschlagen (2026-03-22)

## Kontext

Die Multi-App-Architektur (ADR-001) erfordert einen zentralen Identity Provider. Acht Loesungen wurden evaluiert (siehe `architektur/auth-evaluation.md`).

Anforderungen: europaeisch, OIDC-zertifiziert, self-hosted auf Hetzner CX22/CX32, Admin-UI, geringer Wartungsaufwand, 0 EUR Softwarekosten.

## Entscheidung

**Zitadel** (self-hosted) wird als zentraler Identity Provider eingesetzt.

### Begruendung

- **Einzige vollwertige Loesung die auf CX22 passt** (~512 MB RAM, Go-Binary)
- **Schweizer Unternehmen** (CAOS AG, Zuerich) — staerkster DSGVO-Standort in Europa
- **OpenID Certified** — standardkonforme OIDC-Implementation
- **Admin-UI vorhanden** — im Gegensatz zu Ory (kein UI) und Authelia (kein UI)
- **Teilt PostgreSQL** — kein Redis oder zusaetzliche Datenbank noetig
- **Event-Sourced** — Backup = pg_dump (bereits vorhanden)
- **Docker Compose Deployment** — dokumentiert, in 3 Minuten lauffaehig

### Verworfene Alternativen

| Loesung | Grund fuer Ablehnung |
|---------|---------------------|
| Authentik | Zu viel RAM (2+ GB), braucht Redis, CX22 nicht ausreichend |
| Keycloak | Java/JVM = 1.5+ GB RAM, Overkill, CX32+ noetig |
| Ory | Kein Admin-UI in OSS, zwei Services, zu komplex fuer Ehrenamtliche |
| Authelia | Kein User-Management, keine Self-Registration |
| Logto | Nicht-europaeisch, undokumentierte Ressourcen |
| Supabase Auth | Kein OIDC Provider — disqualifiziert |
| Custom FastAPI | 3-6 Monate Aufwand, Sicherheitsrisiko |

## Konsequenzen

- **Positiv**: SSO ueber alle Apps, DSGVO-konform, 0 EUR Softwarekosten, geringer Wartungsaufwand
- **Negativ**: AGPL-3.0 Lizenz (irrelevant ohne Quellcode-Aenderungen), CX32-Upgrade empfohlen (+5.50 EUR/Monat)
- **Migration**: User aus tsv-hub nach Zitadel migrieren, Auth-Code in tsv-hub durch OIDC-Verifikation ersetzen
