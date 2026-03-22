# Zentrale Benutzerverwaltung — Loesungsbewertung

## Anforderungen

| Anforderung | Prioritaet |
|-------------|-----------|
| OIDC/OAuth2 Provider (Multi-App SSO) | Muss |
| Europaeisch / DSGVO-konform | Muss |
| Self-Hosting moeglich (Hetzner CX22, 2 vCPU, 4 GB RAM) | Muss |
| Admin-UI fuer Benutzerverwaltung | Muss |
| User Self-Registration | Muss |
| MFA / 2FA | Muss |
| TYPO3 + Base44 + React + FastAPI kompatibel | Muss |
| Backup ueber bestehende pg_dump-Strategie | Muss |
| Hochverfuegbarkeit (HA) moeglich | Soll |
| Geringe Kosten (ehrenamtlicher Verein) | Muss |
| Social Login (Google, Apple) | Kann |
| Geringer Wartungsaufwand | Muss |

## Bewertete Loesungen

### 1. Zitadel (Schweiz, Go, AGPL-3.0)

**Empfehlung: Beste Gesamtloesung**

| Aspekt | Bewertung |
|--------|-----------|
| **Herkunft** | CAOS AG, Zuerich, Schweiz |
| **RAM** | ~512 MB (Go-Binary) |
| **CX22 tauglich** | Ja (knapp, CX32-Upgrade empfohlen fuer Komfort) |
| **OIDC** | OpenID Certified, alle Grant Types, PKCE, JWKS |
| **Multi-App** | Exzellent — Organizations, Projects, Project Grants |
| **Admin-UI** | Ja, vollstaendig |
| **Self-Registration** | Ja |
| **MFA** | Ja (TOTP, U2F/FIDO2, SMS) |
| **Social Login** | Ja (Google, GitHub, Apple, etc.) |
| **HA** | Horizontale Skalierung, stateless Binary |
| **Backup** | Event-Sourced → nur PostgreSQL sichern |
| **DB** | PostgreSQL >= 14 (teilt bestehende Instanz) |
| **Lizenz** | AGPL-3.0 (kein Problem wenn Quellcode nicht modifiziert wird) |
| **Kosten** | 0 EUR (self-hosted) |
| **Setup** | Docker Compose in 3 Minuten |
| **Wartung** | Gering (Go-Binary, Docker Image Updates) |
| **GitHub** | 13.3k Stars |

**Staerken:**
- Leichtgewichtig (Go), passt auf CX22
- Schweizer Unternehmen → staerkster DSGVO-Standort
- Event-Sourced Architecture → Backup = pg_dump (schon vorhanden)
- Teilt bestehende PostgreSQL-Instanz
- Ausgezeichnete Multi-App-Architektur (Organizations + Projects)
- Docker Compose Deployment dokumentiert

**Risiken:**
- AGPL-3.0 Lizenz (irrelevant fuer Verein ohne Quellcode-Aenderungen)
- CPU-Spitzen bei Passwort-Hashing (bcrypt) mit nur 2 vCPUs
- Relativ jüngeres Projekt als Keycloak

---

### 2. Authentik (Deutschland, Python, MIT)

| Aspekt | Bewertung |
|--------|-----------|
| **Herkunft** | Authentik Security GmbH, Deutschland |
| **RAM** | ~2 GB+ (Python + Redis) |
| **CX22 tauglich** | Nein — braucht CX32 (8 GB RAM, ~11 EUR/Monat) |
| **OIDC** | Vollstaendig, aber nicht OpenID Certified |
| **Multi-App** | Gut — separate Providers pro App |
| **Admin-UI** | Ja, sehr umfangreich |
| **MFA** | Ja |
| **HA** | Ja (stateless Server + PostgreSQL + Redis) |
| **Backup** | PostgreSQL + Redis sichern |
| **Lizenz** | MIT (sehr permissiv) |
| **Kosten** | 0 EUR (self-hosted OSS), Server-Upgrade ~5.50 EUR/Monat |
| **GitHub** | 20.6k Stars |

**Staerken:**
- Deutsches Unternehmen → exzellenter DSGVO-Standort
- Groesste Community (20.6k Stars)
- MIT-Lizenz (keine Einschraenkungen)
- Extrem flexible Flow-Engine
- Explizit TYPO3-kompatibel dokumentiert

**Schwaaechen:**
- Hoher Ressourcenverbrauch (Python + Redis = 2+ GB RAM)
- Redis als zusaetzliche Abhaengigkeit
- CX22 nicht ausreichend → Server-Upgrade noetig

---

### 3. Keycloak (Red Hat/USA, Java, Apache 2.0)

| Aspekt | Bewertung |
|--------|-----------|
| **Herkunft** | Red Hat (IBM), USA — CNCF Incubation |
| **RAM** | ~1.5-2 GB (JVM) |
| **CX22 tauglich** | Nein — braucht CX32+ |
| **OIDC** | OpenID Certified, Industriestandard |
| **Multi-App** | Exzellent — Realms, Clients, Organizations (v26+) |
| **Admin-UI** | Ja, sehr ausgereift |
| **HA** | Exzellent (Infinispan Clustering) |
| **Lizenz** | Apache 2.0 |
| **Kosten** | 0 EUR + Server-Upgrade ~5.50 EUR/Monat |
| **GitHub** | Massiv (groesstes Projekt) |

**Staerken:**
- Industriestandard, am laengsten am Markt
- Groesste Community und meiste Dokumentation
- Battle-tested bei Grossunternehmen

**Schwaechen:**
- Java/JVM = hoechster Ressourcenverbrauch (1.5+ GB RAM)
- US-Unternehmen (Self-Hosting eliminiert das DSGVO-Risiko)
- Overkill-Funktionsumfang fuer einen Sportverein
- CX22 definitiv nicht ausreichend

---

### 4. Ory Kratos + Hydra (Deutschland, Go, Apache 2.0)

| Aspekt | Bewertung |
|--------|-----------|
| **Herkunft** | Ory Corp, Muenchen, Deutschland |
| **RAM** | ~200-400 MB (zwei Go-Binaries) |
| **CX22 tauglich** | Ja (ressourcentechnisch) |
| **OIDC** | Hydra: OpenID Certified |
| **Admin-UI** | Nein (nur in Ory Network Cloud) |
| **Self-Registration** | Ja (Kratos) |
| **HA** | Ja (stateless Go-Binaries) |
| **Lizenz** | Apache 2.0 |
| **Kosten** | 0 EUR (self-hosted) |
| **GitHub** | 30k+ Stars (kombiniert) |

**Staerken:**
- Deutsches Unternehmen → bester DSGVO-Standort
- Apache 2.0 Lizenz (keine Einschraenkungen)
- Leichtgewichtig (Go)
- Enterprise-grade Sicherheit

**Schwaechen:**
- **Kein Admin-UI** in der OSS-Version → alle Verwaltung ueber API/YAML
- **Headless** → Login-UI muss selbst gebaut werden
- **Zwei Services** (Kratos + Hydra) → doppelte Komplexitaet
- Zu komplex fuer ehrenamtliche Wartung

---

### 5. Authelia (Community, Go, Apache 2.0)

| Aspekt | Bewertung |
|--------|-----------|
| **RAM** | ~50-100 MB |
| **CX22 tauglich** | Ja (leichteste Loesung) |
| **OIDC** | OpenID Certified (eingeschraenkt) |
| **Admin-UI** | Nein |
| **Self-Registration** | Nein |
| **Social Login** | Nein |
| **Kosten** | 0 EUR |

**Disqualifiziert:** Keine User-Verwaltung, keine Self-Registration, kein Admin-UI. Nur als Reverse-Proxy-Auth-Addon geeignet, nicht als standalone IdP.

---

### 6. Logto (USA/China, TypeScript, MPL-2.0)

| Aspekt | Bewertung |
|--------|-----------|
| **Herkunft** | Silverhand Inc., USA/China |
| **RAM** | ~300-500 MB (geschaetzt, nicht dokumentiert) |
| **CX22 tauglich** | Grenzwertig |
| **OIDC** | Ja (nicht zertifiziert) |
| **Kosten** | 0 EUR (self-hosted) |

**Schwaechen:** Nicht-europaeisches Unternehmen, undokumentierte Ressourcenanforderungen, weniger ausgereift. Kein Vorteil gegenueber Zitadel.

---

### 7. Supabase Auth — **Disqualifiziert**

Kann **nicht als OIDC Provider** agieren. Nur als Consumer (Relying Party). Kann TYPO3 und Base44 nicht bedienen.

### 8. Custom FastAPI — **Disqualifiziert**

3-6 Monate Entwicklungsaufwand, kein OIDC, Sicherheitsrisiko. Geloestes Problem nicht nochmal loesen.

---

## Gesamtvergleich

```
                    Ressourcen (RAM)
                    ◄── weniger          mehr ──►

  Authelia ·····|                                    Leichtgewichtig
  Ory      ·········|                                aber kein Admin-UI
  Zitadel  ···············|                          ★ EMPFEHLUNG
  Logto    ·····················|                     Nicht-EU
  Authentik ···························|              Braucht CX32
  Keycloak  ··································|      Braucht CX32+
  Supabase  ·········································|  Disqualifiziert

                    Funktionsumfang
                    ◄── weniger          mehr ──►

  Authelia ·····|
  Custom   ·········|
  Logto    ·················|
  Ory      ·······················|
  Zitadel  ····························|             ★ EMPFEHLUNG
  Authentik ·······························|
  Keycloak  ··································|

                    Wartungsaufwand
                    ◄── weniger          mehr ──►

  Zitadel  ·····|                                    ★ EMPFEHLUNG
  Logto    ·········|
  Authelia ·········|
  Authentik ···············|
  Keycloak  ·····················|
  Ory      ····························|
  Custom   ··································|
```

## Kostenvergleich (monatlich)

| Loesung | Software | Server | Gesamt |
|---------|----------|--------|--------|
| **Zitadel (CX22)** | 0 EUR | 5.49 EUR (bestehend) | **5.49 EUR** |
| **Zitadel (CX32)** | 0 EUR | 10.99 EUR (+5.50) | **10.99 EUR** |
| Authentik (CX32) | 0 EUR | 10.99 EUR (+5.50) | **10.99 EUR** |
| Keycloak (CX32) | 0 EUR | 10.99 EUR (+5.50) | **10.99 EUR** |
| Ory Network Cloud | 70+ USD/Monat | 0 EUR | **~65 EUR** |
| Zitadel Cloud Pro | 100 USD/Monat | 0 EUR | **~93 EUR** |
| Authentik Enterprise | 5 USD/User/Monat | 10.99 EUR | **variabel** |

## Empfehlung: Zitadel Self-Hosted

### Warum Zitadel?

1. **Passt auf CX22** (als einzige vollwertige Loesung neben Ory)
2. **Schweizer Unternehmen** (staerkster DSGVO-Schutz)
3. **Admin-UI vorhanden** (Ory hat keins)
4. **OpenID Certified** (Authentik und Logto nicht)
5. **Geringstes Wartungsaufwand** (einzelne Go-Binary)
6. **Teilt PostgreSQL** (kein Redis wie Authentik)
7. **Event-Sourced** → Backup = pg_dump (schon vorhanden)
8. **0 EUR zusaetzliche Kosten** (self-hosted, AGPL)

### Integrationsstrategie

```
┌─────────────────────────────────────────────────────┐
│                 Hetzner CX22/CX32                   │
│                                                     │
│  ┌──────────┐  ┌──────────┐  ┌──────────────────┐  │
│  │ Zitadel  │  │ tsv-hub  │  │   PostgreSQL     │  │
│  │ :8080    │  │ API :8000│  │   (shared)       │  │
│  │          │  │ Web :5173│  │   - zitadel DB   │  │
│  └────┬─────┘  └────┬─────┘  │   - tsvhub DB    │  │
│       │              │        └──────────────────┘  │
│       └──────────────┤                              │
│                      │                              │
│  ┌───────────────────┴──────────────────────┐       │
│  │              Nginx (Reverse Proxy)        │       │
│  │  auth.tsv-wartenberg.de → Zitadel :8080  │       │
│  │  tsv-hub.tsv-wartenberg.de → API :8000   │       │
│  └──────────────────────────────────────────┘       │
└─────────────────────────────────────────────────────┘
          │                    │
          ▼                    ▼
   ┌──────────┐        ┌──────────┐
   │ Base44   │        │ TYPO3    │
   │ HelferHub│        │ Website  │
   │ (OIDC)   │        │ (OIDC)   │
   └──────────┘        └──────────┘
```

### Migrationsschritte

1. Zitadel via Docker Compose deployen (teilt PostgreSQL)
2. Organisation "TSV Wartenberg" + Rollen anlegen
3. Bestehende User aus tsv-hub nach Zitadel migrieren
4. tsv-hub Backend: JWT-Verifikation auf Zitadel JWKS umstellen
5. tsv-hub Frontend: Login auf Zitadel Hosted Login umstellen
6. TYPO3: OIDC-Extension konfigurieren
7. Base44: OIDC-Provider konfigurieren
8. Alte Auth-Endpunkte in tsv-hub entfernen

### Server-Empfehlung

| Variante | Specs | Preis | Empfehlung |
|----------|-------|-------|------------|
| CX22 (bestehend) | 2 vCPU, 4 GB | 5.49 EUR | Funktioniert, aber knapp |
| **CX32 (Upgrade)** | **4 vCPU, 8 GB** | **10.99 EUR** | **Empfohlen** — Komfort fuer Zitadel + tsv-hub + PostgreSQL + Monitoring |
| CX42 | 8 vCPU, 16 GB | 18.99 EUR | Overkill, aber zukunftssicher |

**Empfehlung: CX32-Upgrade** (+5.50 EUR/Monat) fuer komfortablen Betrieb aller Services.

## Quellen

- [Zitadel Pricing](https://zitadel.com/pricing)
- [Zitadel Self-Hosting](https://zitadel.com/docs/self-hosting/deploy/compose)
- [Zitadel OIDC](https://zitadel.com/docs/guides/integrate/login/oidc/oauth-recommended-flows)
- [Zitadel GDPR/DPA](https://zitadel.com/docs/legal/data-processing-agreement)
- [Authentik Pricing](https://goauthentik.io/pricing)
- [Authentik Installation](https://docs.goauthentik.io/docs/install-config/install/docker-compose)
- [Keycloak Sizing](https://www.keycloak.org/high-availability/multi-cluster/concepts-memory-and-cpu-sizing)
- [Ory Pricing](https://www.ory.com/pricing/)
- [Authelia OIDC](https://www.authelia.com/configuration/identity-providers/openid-connect/provider/)
- [Logto Pricing](https://logto.io/pricing)
- [TYPO3 OIDC Extension](https://extensions.typo3.org/extension/oidc)
