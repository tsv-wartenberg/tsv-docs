# User-Management-Konzept mit Kinde

**Stand:** 2026-03-23
**Bezug:** ADR-006 (Kinde als Identity Provider)

## Ueberblick

**Kinde ist die zentrale Stelle fuer Benutzer, Rollen UND Vereinsstruktur (Sparten).**

Die Sparten werden als Kinde Organizations abgebildet. Damit sind User, Rollen und
Sparten-Zuordnungen fuer ALLE Apps ueber eine einzige Quelle (Kinde API / JWT) verfuegbar —
tsv-hub, TYPO3, Beitragseinzug, Dashboard, kuenftige Apps.

### Designprinzip

> **Kinde = Single Source of Truth** fuer alles, was mit Personen, Rollen und
> Vereinsstruktur zu tun hat. Die Apps konsumieren diese Daten, sie verwalten sie nicht.

### Warum alles in Kinde?

| Vorteil | Erklaerung |
|---------|-----------|
| **Eine Quelle fuer alle Apps** | tsv-hub, Beitragseinzug, TYPO3 greifen auf dieselben Daten zu |
| **Kein Sync noetig** | Keine Doppelpflege, keine Inkonsistenzen |
| **Admin-UI fuer alle** | Nicht-technische Admins pflegen alles in einem Dashboard |
| **API fuer Automatisierung** | Beitragseinzug, Reports etc. per Management API |
| **Neue Apps sofort angebunden** | Jede neue App bekommt User+Rollen ueber OIDC/API |

---

## Datenmodell in Kinde

### Vereinsstruktur: Sparten als Organizations

```
Kinde Business: "TSV Wartenberg"
│
├── Org: "Gesamtverein"       org_code: org_gesamt
│   ├── Rolle: admin           (Vorstand, IT)
│   └── Rolle: mitglied        (alle Vereinsmitglieder)
│
├── Org: "Fussball"            org_code: org_fussball
│   ├── Rolle: spartenleiter
│   ├── Rolle: trainer
│   └── Rolle: mitglied
│   └── Properties:
│       ├── beitrag_monatlich: 15
│       ├── beitrag_jaehrlich: 150
│       └── trainingszeiten: "Di+Do 18-20 Uhr"
│
├── Org: "Tennis"              org_code: org_tennis
│   ├── Rolle: spartenleiter
│   ├── Rolle: trainer
│   └── Rolle: mitglied
│   └── Properties:
│       ├── beitrag_monatlich: 20
│       ├── beitrag_jaehrlich: 200
│       └── trainingszeiten: "Mi 17-19 Uhr"
│
├── Org: "Turnen"              org_code: org_turnen
│   ├── Rolle: spartenleiter
│   ├── Rolle: trainer
│   └── Rolle: mitglied
│   └── Properties:
│       ├── beitrag_monatlich: 12
│       ├── beitrag_jaehrlich: 120
│       └── trainingszeiten: "Fr 16-18 Uhr"
│
└── (weitere Sparten nach Bedarf)
```

### Rollen (einmal definiert, pro Org zuweisbar)

| Rolle | Bedeutung | Typische Nutzer |
|-------|-----------|-----------------|
| `admin` | Volle Verwaltungsrechte (nur in Org "Gesamtverein") | Vorstand, IT |
| `spartenleiter` | Leitet eine Sparte, verwaltet deren Mitglieder | Abteilungsleiter |
| `trainer` | Trainiert in einer Sparte, sieht deren Mitglieder | Trainer, Uebungsleiter |
| `mitglied` | Einfaches Mitglied | Alle Vereinsmitglieder |

**Gleiche Person, verschiedene Rollen pro Sparte:**

| Person | Gesamtverein | Fussball | Tennis | Turnen |
|--------|-------------|----------|--------|--------|
| Max Mustermann | mitglied | trainer | mitglied | — |
| Anna Schmidt | admin | spartenleiter | — | mitglied |
| Tom Fischer | mitglied | mitglied | — | — |

### Organization Properties (Custom-Daten pro Sparte)

| Property | Typ | Beispiel | Verwendung |
|----------|-----|---------|-----------|
| `beitrag_monatlich` | Number | 15 | Beitragseinzug |
| `beitrag_jaehrlich` | Number | 150 | Beitragseinzug |
| `beitrag_aufnahme` | Number | 30 | Beitragseinzug (einmalig) |
| `trainingszeiten` | String | "Di+Do 18-20" | Website, Kalender |
| `kontakt_email` | String | "fussball@tsv..." | Kontaktformular |
| `max_mitglieder` | Number | 120 | Kapazitaetsplanung |

---

## Was liefert Kinde an die Apps?

### Im JWT (bei jedem Login/Request)

```json
{
  "sub": "kp_abc123",
  "email": "max.mustermann@example.de",
  "given_name": "Max",
  "family_name": "Mustermann",
  "org_codes": ["org_gesamt", "org_fussball", "org_tennis"],
  "org_code": "org_fussball",
  "roles": ["trainer"],
  "permissions": ["read:members", "write:training"]
}
```

- `org_codes`: Alle Sparten des Nutzers (im ID Token)
- `org_code`: Aktuelle Sparte (im Access Token)
- `roles`: Rollen in der aktuellen Sparte

### Per Management API (fuer Backend-Systeme)

```
GET /api/v1/organizations/{org_code}/users     → Alle Mitglieder einer Sparte
GET /api/v1/users/{user_id}/organizations      → Alle Sparten eines Nutzers
GET /api/v1/organizations/{org_code}/properties → Beitragshöhe etc.
GET /api/v1/organizations/{org_code}/users/{id}/roles → Rolle in Sparte
```

---

## Anwendungsfall: Beitragseinzug

```
Beitragseinzug-System (z.B. Script, App, oder Buchhalter-Tool)
  │
  ├─ 1. Kinde API: Alle User aus Org "Gesamtverein" holen
  │     GET /api/v1/organizations/org_gesamt/users
  │     → [{id: "kp_abc123", name: "Max Mustermann", email: "max@..."}, ...]
  │
  ├─ 2. Pro User: Alle Orgs (Sparten) holen
  │     GET /api/v1/users/kp_abc123/organizations
  │     → ["org_gesamt", "org_fussball", "org_tennis"]
  │
  ├─ 3. Pro Sparte: Beitrag holen
  │     GET /api/v1/organizations/org_fussball/properties
  │     → {beitrag_monatlich: 15}
  │     GET /api/v1/organizations/org_tennis/properties
  │     → {beitrag_monatlich: 20}
  │
  └─ 4. Beitrag berechnen:
        Max Mustermann:
          Fussball  15 EUR
          Tennis    20 EUR
          ──────────────────
          Gesamt    35 EUR/Monat
```

**Kein Zugriff auf tsv-hub noetig.** Das Beitragseinzug-System braucht nur die Kinde API.

---

## Prozesse der Benutzerpflege

### Prozess 1: Neues Mitglied registriert sich selbst

```
1. Mitglied klickt Registrierungslink
   (Website, HelferHub, QR-Code, WhatsApp, E-Mail)
       │
       ▼
2. Kinde: Registrierungsseite
   - Vorname, Nachname, E-Mail, Passwort
   - Oder: Google / Apple Login
   - E-Mail-Verifizierung
       │
       ▼
3. Kinde: Nutzer wird angelegt
   - Automatisch in Org "Gesamtverein" (Default)
   - Rolle: "mitglied"
   - Noch KEINER Sparte zugeordnet
       │
       ▼
4. Redirect zu tsv-hub → Willkommensseite
   "Willkommen! Ein Spartenleiter wird dich zuordnen."
       │
       ▼
5. Admin/Spartenleiter: In Kinde Admin-UI
   - Nutzer oeffnen → Organisationen verwalten
   - Sparte(n) zuweisen: z.B. "Fussball" hinzufuegen
   - Rolle in Sparte setzen: z.B. "mitglied"
       │
       ▼
6. Fertig: Nutzer kann sich in allen Apps mit seinen
   Sparten-Rollen anmelden
```

**Dauer:** Schritt 1-4 = 2 Minuten (Self-Service). Schritt 5 = innerhalb 1-2 Tagen.

---

### Prozess 2: Admin laedt Mitglied ein

```
1. Admin erstellt Einladungslink (mit vorausgefuellter E-Mail):
   https://tsv-wartenberg.kinde.com/oauth2/auth
     ?prompt=create
     &login_hint=max.mustermann@example.de
     &org_code=org_gesamt
     &redirect_uri=https://tsv-hub.tsv-wartenberg.de/callback
       │
       ▼
2. Mitglied erhaelt E-Mail mit Link
       │
       ▼
3. Mitglied klickt → Kinde Registrierung (E-Mail vorausgefuellt)
       │
       ▼
4. Admin weist in Kinde Admin-UI Sparte(n) + Rolle zu

Optional vorab: Admin kann Nutzer in Kinde manuell anlegen
und Sparten + Rollen VORHER setzen. Nutzer setzt beim
ersten Login nur noch sein Passwort.
```

---

### Prozess 3: Bulk-Import (z.B. Jahres-Import)

```
1. CSV vorbereiten:
   vorname, nachname, email

2. In Kinde Admin-UI hochladen (oder per Management API):
   - User werden angelegt
   - Alle kommen in Org "Gesamtverein"

3. Sparten-Zuordnung per Kinde Admin-UI oder API:
   - User zu Sparten-Orgs hinzufuegen
   - Rollen setzen

4. Nutzer erhalten Einladungsmail → Passwort setzen
```

---

### Prozess 4: Rolle aendern

```
Ausloeser: Mitglied uebernimmt neue Aufgabe oder wechselt Sparte

Alles in Kinde Admin-UI:

Fall A: Sparten-Rolle aendern (Mitglied wird Trainer)
   → Nutzer oeffnen → Org "Fussball" → Rolle: trainer
   → 1 Klick, sofort wirksam in allen Apps

Fall B: Neue Sparte zuordnen
   → Nutzer oeffnen → Org "Tennis" hinzufuegen → Rolle: mitglied
   → Beitragseinzug sieht neue Sparte automatisch

Fall C: Sparte verlassen
   → Nutzer oeffnen → Org "Fussball" entfernen
   → Kein Zugriff mehr auf Fussball-Daten
   → Beitragseinzug reduziert Beitrag automatisch

Fall D: Zum Admin machen
   → Nutzer oeffnen → Org "Gesamtverein" → Rolle: admin
   → Nutzer hat jetzt ueberall Admin-Rechte
```

---

### Prozess 5: Mitglied tritt aus / wird gesperrt

```
Alles in Kinde Admin-UI:

Fall A: Austritt (Zugang sperren)
   → Nutzer sperren (Suspend)
   → Kann sich nirgends mehr einloggen
   → Sparten-Zuordnungen bleiben (historisch)
   → tsv-hub: Keine Aenderung noetig (Login blockiert)

Fall B: DSGVO-Loeschung
   → Nutzer in Kinde loeschen
   → tsv-hub: Anonymisierung der verknuepften Daten
     (Buchungen, Helferschichten → "Geloeschter Nutzer")

Fall C: Temporaere Sperre
   → Nutzer sperren → spaeter wieder aktivieren
```

---

### Prozess 6: Passwort / MFA (Self-Service)

```
Passwort vergessen:
   → Login-Seite → "Passwort vergessen" → Kinde Reset-Mail
   → Kein Admin noetig

MFA einrichten:
   → Kinde Account-Einstellungen → TOTP aktivieren
   → Kein Admin noetig

MFA verloren:
   → Admin → Kinde Admin-UI → MFA zuruecksetzen
```

---

## Wo pflegt wer was?

### Alles in Kinde Admin-UI (kinde.com Dashboard)

| Aufgabe | Wer | Wie oft |
|---------|-----|---------|
| Neues Mitglied einer Sparte zuordnen | Admin / Spartenleiter | Woechentlich |
| Sparten-Rolle aendern | Admin / Spartenleiter | Monatlich |
| Spartenleiter ernennen | Admin (Vorstand) | Selten |
| Nutzer sperren/loeschen | Admin | Selten |
| Beitragshöhe einer Sparte aendern | Admin | Jaehrlich |
| Neue Sparte anlegen | Admin (IT) | Sehr selten |
| MFA-Policy setzen | Admin (IT) | Einmalig |
| Bulk-Import | Admin (IT) | 1x jaehrlich |

### Was bleibt in tsv-hub?

tsv-hub verwaltet **nur noch fachliche Daten**, keine Benutzer/Rollen mehr:

| In tsv-hub | Nicht mehr in tsv-hub |
|-----------|----------------------|
| Events / Kalender | ~~mitglied_sparten~~ → Kinde |
| Material-Inventar | ~~sparten.leiter_id~~ → Kinde Rolle |
| Buchungen / Finanzen | ~~mitglieder.rolle~~ → Kinde |
| Helferschichten | ~~Passwort-Hashes~~ → Kinde |
| Anwesenheitslisten | ~~JWT-Secret~~ → Kinde JWKS |

tsv-hub behaelt eine **schlanke Mitglieder-Tabelle** als lokalen Cache:

```sql
CREATE TABLE mitglieder (
    id UUID PRIMARY KEY,
    kinde_sub VARCHAR(255) UNIQUE NOT NULL,  -- Kinde User ID
    -- Keine Rolle, keine Sparte — kommt aus Kinde JWT/API
    -- Nur app-spezifische Daten:
    notizen TEXT,            -- interne Notizen
    created_at TIMESTAMP,
    updated_at TIMESTAMP
);
```

Stammdaten (Name, E-Mail) kommen aus dem Kinde JWT bei jedem Request.
Sparten und Rollen kommen aus dem Kinde JWT (`org_codes`, `roles`).

---

## Registrierung: Zugangspunkte

### Registrierungslink (Direkt-URL)

```
https://tsv-wartenberg.kinde.com/oauth2/auth
  ?response_type=code
  &client_id=<TSV_HUB_CLIENT_ID>
  &redirect_uri=https://tsv-hub.tsv-wartenberg.de/callback
  &scope=openid%20profile%20email
  &prompt=create
  &org_code=org_gesamt
```

### Wo einbettbar?

| Ort | Beispiel |
|-----|---------|
| **HelferHub (Base44)** | "Noch kein TSV-Konto? Hier registrieren" |
| **TYPO3 Website** | "Mitglied werden → Konto erstellen" |
| **E-Mail-Einladung** | "Du wurdest eingeladen, klicke hier" |
| **QR-Code bei Events** | Poster mit QR zum Registrierungslink |
| **WhatsApp/Telegram** | Direkter Link in Gruppen |
| **tsv-hub Login-Seite** | "Konto erstellen"-Button |

### Szenario: Helfer-App → Registrierung

```
┌──────────────────────────┐
│   HelferHub (Base44)     │
│                          │
│  Schicht: Getraenkestand │
│  Sa. 14.06. 10-14 Uhr   │
│  [Ich helfe mit!]        │
│                          │
│  Noch kein TSV-Konto?    │
│  [Jetzt registrieren] ──────→ Kinde (prompt=create)
│                          │          │
└──────────────────────────┘          ▼
                               Registrierung
                                     │
                                     ▼
                               Redirect → tsv-hub
                               "Willkommen!"
```

**Hinweis:** HelferHub-Login bleibt separat (Base44 Auth),
da Base44 OIDC erst ab Elite-Plan ($80/Mo) unterstuetzt.

---

## Integration pro App

### tsv-hub (React + FastAPI)

```
Login → Kinde → JWT zurueck → tsv-hub

FastAPI bei jedem Request:
  1. JWT-Signatur pruefen (Kinde JWKS)
  2. org_codes auslesen → User ist in diesen Sparten
  3. roles auslesen → Rolle in aktueller Sparte
  4. Berechtigungen pruefen:
     - Admin (role=admin in org_gesamt)? → Alles sehen
     - Spartenleiter in org_fussball? → Fussball-Daten verwalten
     - Trainer in org_fussball? → Fussball-Mitglieder sehen
     - Mitglied? → Nur eigene Daten
```

**Aenderungen in tsv-hub:**
- Login/Register-Seite entfernen → Redirect zu Kinde
- JWT-Verifikation: Kinde JWKS statt lokaler Secret
- RBAC: Rollen aus JWT statt aus DB lesen
- `mitglied_sparten`-Tabelle entfaellt (Kinde ist Source of Truth)
- `sparten`-Tabelle wird optional (Kinde Org Properties)
- Schlanke `mitglieder`-Tabelle als Cache (nur kinde_sub + app-spezifische Daten)

### TYPO3 Website

- OIDC Login mit Kinde
- User-Rollen aus JWT → TYPO3-Benutzergruppen
- Geschuetzte Bereiche pro Sparte moeglich

### Beitragseinzug (kuenftig)

- Kinde Management API abfragen (M2M Token)
- Pro Mitglied: Sparten + Beitragshoehe ermitteln
- Beitragsberechnung ohne Zugriff auf tsv-hub

### Base44 HelferHub

- Kein OIDC (nur ab Elite $80/Mo)
- Registrierungslink als HTML-Link
- Separates Login (Base44 Auth)

---

## Berechtigungsmatrix

| Aktion | admin (org_gesamt) | spartenleiter (org_X) | trainer (org_X) | mitglied (org_X) |
|--------|-------------------|-----------------------|-----------------|-------------------|
| Alle Mitglieder sehen | ✓ | — | — | — |
| Sparten-Mitglieder sehen | ✓ | ✓ (eigene) | ✓ (eigene) | — |
| Eigenes Profil sehen | ✓ | ✓ | ✓ | ✓ |
| Mitglied anlegen/bearbeiten | ✓ | ✓ (eigene) | — | — |
| Events erstellen | ✓ | ✓ (eigene) | — | — |
| Events sehen | ✓ | ✓ | ✓ | ✓ |
| Material verwalten | ✓ | ✓ (eigene) | ✓ (eigene) | — |
| Buchungen erstellen | ✓ | ✓ (eigene) | — | — |
| Helferschichten verwalten | ✓ | ✓ (eigene) | — | — |
| Fuer Schicht eintragen | ✓ | ✓ | ✓ | ✓ |
| Rollen zuweisen | ✓ (Kinde) | — | — | — |
| Beitraege aendern | ✓ (Kinde) | — | — | — |

---

## Kinde-Setup fuer TSV Wartenberg

### Schritt-fuer-Schritt

1. **Account erstellen** auf kinde.com (Free Tier, EU-Region)
2. **Rollen definieren** (businessweit):
   - `admin`, `spartenleiter`, `trainer`, `mitglied`
3. **Permissions definieren** (optional, fuer feinere Steuerung):
   - `read:members`, `write:members`, `manage:events`, `manage:material`, etc.
4. **Organisation "Gesamtverein"** anlegen (`org_gesamt`)
   - Default-Org fuer alle neuen Registrierungen
   - Default-Rolle: `mitglied`
5. **Organisation pro Sparte** anlegen:
   - "Fussball" (`org_fussball`) + Properties (Beitrag etc.)
   - "Tennis" (`org_tennis`) + Properties
   - "Turnen" (`org_turnen`) + Properties
   - weitere nach Bedarf
6. **OIDC Client: tsv-hub** anlegen
   - Redirect URI: `https://tsv-hub.tsv-wartenberg.de/callback`
7. **OIDC Client: TYPO3** anlegen
   - Redirect URI: `https://www.tsv-wartenberg.de/typo3/callback`
8. **Token Customization** aktivieren:
   - Access Token: Roles (array) einschalten
   - ID Token: org_codes einschalten
9. **Self-Registration** aktivieren
10. **MFA optional** aktivieren (TOTP)
11. **Bestehende User** importieren (CSV)
12. **Webhook** einrichten (User-Events → tsv-hub)

### Kinde-Endpoints

| Endpoint | URL |
|----------|-----|
| Discovery | `https://tsv-wartenberg.kinde.com/.well-known/openid-configuration` |
| JWKS | `https://tsv-wartenberg.kinde.com/.well-known/jwks` |
| Authorization | `https://tsv-wartenberg.kinde.com/oauth2/auth` |
| Token | `https://tsv-wartenberg.kinde.com/oauth2/token` |
| Management API | `https://tsv-wartenberg.kinde.com/api/v1/...` |
| Registrierung | `...oauth2/auth?...&prompt=create&org_code=org_gesamt` |

### Kosten

| Phase | Plan | Kosten |
|-------|------|--------|
| MVP / Test | Free | 0 EUR/Monat |
| Produktion (< 10.500 MAU) | Free | 0 EUR/Monat |
| Mit Custom Domain | Pro | ~23 EUR/Monat |

Realistisch fuer den TSV: **0 EUR/Monat dauerhaft** (Free Tier reicht).
