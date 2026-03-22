# ADR-002: Zentrale Benutzerverwaltung (tsv-auth)

## Status

Akzeptiert (2026-03-22)

## Kontext

In einer Multi-App-Architektur muss die Authentifizierung zentral geloest werden. Jede App eigene User verwalten zu lassen fuehrt zu Dateninkonsistenzen und mehrfachen Logins.

## Entscheidung

Eine dedizierte Auth-App (`tsv-auth`) wird als zentraler Identity Provider implementiert:

- **Technologie**: FastAPI + PostgreSQL (gleicher Stack wie tsv-hub)
- **Protokoll**: JWT mit JWKS-Endpunkt fuer Token-Verifikation
- **Umfang**: User CRUD, Rollen, Sparten, Login/Refresh
- **Verifikation**: Andere Apps verifizieren JWTs ueber `/.well-known/jwks.json`

## Alternativen betrachtet

1. **Keycloak / Auth0**: Zu komplex / zu teuer fuer einen Sportverein
2. **Auth im Monolith belassen**: Widerspricht der Multi-App-Architektur
3. **Shared Database**: Enge Kopplung, schwer unabhaengig zu deployen

## Konsequenzen

- **Positiv**: Single Sign-On, konsistente User-Daten, klare Trennung
- **Negativ**: Zusaetzlicher Service zu betreiben, SPOF-Risiko
- **Migration**: User-Daten aus tsv-hub nach tsv-auth migrieren
