# API-Design-Konventionen

Alle TSV-Apps folgen diesen REST-API-Konventionen.

## Allgemein

- **Basis-URL**: `/api/v1/`
- **Content-Type**: `application/json`
- **Datumsformat**: ISO 8601 (UTC)
- **IDs**: UUID v4
- **Sprache**: API-Pfade und Feldnamen auf Deutsch (da fachliche Domaene)

## Authentifizierung

```
Authorization: Bearer <JWT>
```

JWTs werden von `tsv-auth` ausgestellt und von allen Apps via JWKS verifiziert.

## Paginierung

```
GET /api/v1/mitglieder?page=1&size=20
```

Response:
```json
{
  "items": [...],
  "total": 142,
  "page": 1,
  "size": 20
}
```

## Filterung und Sortierung

```
GET /api/v1/mitglieder?status=aktiv&sort=nachname&order=asc
```

## Fehlerformat

```json
{
  "detail": "Mitglied nicht gefunden",
  "code": "NOT_FOUND"
}
```

## HTTP-Status-Codes

| Code | Bedeutung |
|------|-----------|
| 200 | OK |
| 201 | Created |
| 204 | No Content (Deleted) |
| 400 | Bad Request |
| 401 | Unauthorized (kein/ungueltiger Token) |
| 403 | Forbidden (keine Berechtigung) |
| 404 | Not Found |
| 409 | Conflict (Duplikat) |
| 422 | Validation Error |
| 500 | Internal Server Error |
