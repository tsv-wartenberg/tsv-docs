# tsv-auth — Zentrale Benutzerverwaltung

## Uebersicht

Zentraler Identity Provider fuer alle TSV-Apps. Verwaltet User, Rollen und Sparten. Stellt JWTs aus, die von allen anderen Apps verifiziert werden.

| Feld | Wert |
|------|------|
| **Repo** | [github.com/ckoehler99/tsv-auth](https://github.com/ckoehler99/tsv-auth) |
| **Technologie** | FastAPI + PostgreSQL |
| **Status** | Geplant |

## Verantwortlichkeiten

- User-Registrierung und -Verwaltung (CRUD)
- Login / Authentifizierung (JWT + Refresh Tokens)
- Rollen- und Spartenverwaltung
- JWKS-Endpunkt fuer Token-Verifikation (`/.well-known/jwks.json`)
- Passwort-Reset (spaeter: E-Mail-Verifikation via Resend)

## API-Endpunkte (geplant)

```
POST   /auth/login
POST   /auth/refresh
POST   /auth/register       (nur Admin)
GET    /auth/me
GET    /users
GET    /users/{id}
PUT    /users/{id}
DELETE /users/{id}
GET    /sparten
POST   /sparten
GET    /.well-known/jwks.json
```

## Migration

User-Daten, Rollen und Sparten werden aus tsv-hub extrahiert.
