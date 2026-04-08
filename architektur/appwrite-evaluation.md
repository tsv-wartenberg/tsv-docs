# Appwrite Evaluation — Backend-as-a-Service fuer den TSV Wartenberg

**Stand:** 2026-04-08
**Kontext:** Evaluation von Appwrite als potenzielle BaaS-Plattform (Backend-as-a-Service)
fuer die digitale Infrastruktur des TSV Wartenberg e.V. Diese Analyse deckt alle relevanten
Aspekte ab: Pricing, Auth, Datenbank, Self-Hosting, DSGVO und mehr.

---

## Inhaltsverzeichnis

1. [Ueberblick](#1-ueberblick)
2. [Pricing](#2-pricing)
3. [Self-Hosting](#3-self-hosting)
4. [Auth Features](#4-auth-features)
5. [Datenbank](#5-datenbank)
6. [Functions (Serverless)](#6-functions-serverless)
7. [Storage](#7-storage)
8. [EU Data Residency / DSGVO](#8-eu-data-residency--dsgvo)
9. [Cloud vs Self-Hosted](#9-cloud-vs-self-hosted)
10. [Architektur (Under the Hood)](#10-architektur-under-the-hood)
11. [SDKs](#11-sdks)
12. [Realtime](#12-realtime)
13. [Messaging](#13-messaging)
14. [Sites (Web Hosting)](#14-sites-web-hosting)
15. [Bekannte Limitierungen](#15-bekannte-limitierungen)
16. [Community und Reife](#16-community-und-reife)
17. [Vergleich mit Firebase und Supabase](#17-vergleich-mit-firebase-und-supabase)
18. [Bewertung fuer TSV Wartenberg](#18-bewertung-fuer-tsv-wartenberg)

---

## 1. Ueberblick

Appwrite ist eine Open-Source Backend-as-a-Service (BaaS) Plattform, die Auth, Datenbank,
Storage, Functions, Messaging und Realtime-APIs in einer einzigen Loesung vereint.
Verfuegbar als Managed Cloud oder vollstaendig Self-Hosted via Docker.

- **Website:** https://appwrite.io
- **GitHub:** https://github.com/appwrite/appwrite
- **Lizenz:** BSD 3-Clause (vollstaendig Open Source, kommerziell nutzbar)
- **Gruendung:** 2019 (Repository erstellt April 2019)
- **Aktuelle Version:** 1.9.0 (April 2026)
- **Geschrieben in:** TypeScript (63.7%), PHP (35%)

---

## 2. Pricing

### Tier-Uebersicht (Stand: September 2025, neue Preisstruktur)

| | Free | Pro | Enterprise |
|---|---|---|---|
| **Preis** | 0 EUR/Monat | ab 25 USD/Monat | Individuell |
| **Projekte** | Unbegrenzt | 1 inkl., je weiteres +15 USD | Individuell |
| **Seats/Members** | Unbegrenzt | Unbegrenzt | Unbegrenzt |

> **Hinweis:** Das alte "Scale"-Tier (599 USD/Monat) wurde in der Preisumstellung
> August/September 2025 umstrukturiert. Die aktuelle Preisseite zeigt drei Tiers:
> Free, Pro und Enterprise.

### Ressourcen-Limits pro Projekt

| Ressource | Free | Pro | Enterprise |
|---|---|---|---|
| **Monthly Active Users (MAU)** | 75.000 | 200.000 | Individuell |
| **API Bandwidth** | 5 GB/Monat | 2 TB/Monat | Individuell |
| **Storage** | 2 GB | 150 GB | Individuell |
| **Function Executions** | 750.000/Monat | 3,5 Mio./Monat | Individuell |
| **Database Reads** | 500.000/Monat | 1,75 Mio./Monat | Individuell |
| **Database Writes** | 250.000/Monat | 750.000/Monat | Individuell |
| **Datenbanken** | 1 pro Projekt | Unbegrenzt | Unbegrenzt |
| **Buckets** | 1 pro Projekt | Unbegrenzt | Unbegrenzt |
| **Functions** | 2 pro Projekt | Unbegrenzt | Unbegrenzt |
| **Teams** | 100 pro Projekt | Unbegrenzt | Unbegrenzt |
| **Realtime Connections** | 250 gleichzeitig | 500 gleichzeitig | Individuell |
| **Realtime Messages** | 2 Mio. | 6 Mio. | Individuell |
| **Messaging** | 1.000 Nachrichten/Monat, 1 Topic | Unbegrenzt | Unbegrenzt |

### Add-On-Preise (Pro-Tier)

| Ressource | Preis |
|---|---|
| Zusaetzliche Bandwidth | 15 USD pro 100 GB/Monat |
| Zusaetzlicher Storage | 2,80 USD pro 100 GB |
| Zusaetzliche DB Reads | 0,06 USD pro 100.000 |
| Zusaetzliche DB Writes | 0,10 USD pro 100.000 |
| Zusaetzliche Function Executions | 2 USD pro 1 Mio. |
| Zusaetzliche Realtime Connections | 5 USD pro 1.000 |
| Zusaetzliche Realtime Messages | 2,50 USD pro 1 Mio. |
| Zusaetzliche Users | 3 USD pro 1.000 |
| Zusaetzliches Projekt | 15 USD |

### Einschaetzung fuer TSV Wartenberg

Bei geschaetzten 50-200 MAU und moderater Nutzung:
- **Free Tier reicht voellig aus** (75.000 MAU >> 200 MAU)
- Auch Storage (2 GB), Bandwidth (5 GB) und DB Reads/Writes waeren ausreichend
- Einschraenkung: nur 1 Datenbank, 1 Bucket, 2 Functions im Free Tier

---

## 3. Self-Hosting

### Open Source und Lizenz

- **Lizenz:** BSD 3-Clause — vollstaendig Open Source
- **Kommerzielle Nutzung:** Erlaubt ohne zusaetzliche Lizenzkosten
- **Keine Feature-Einschraenkungen** gegenueber der Cloud-Version
- Kein "Open Core"-Modell mit zurueckgehaltenen Enterprise-Features

### Systemanforderungen

| Anforderung | Minimum | Empfohlen |
|---|---|---|
| **CPU** | 1 Core | 2 vCPU |
| **RAM** | 2 GB | 4 GB |
| **Disk** | Abhaengig vom Datenvolumen | SSD empfohlen |
| **OS** | Jedes OS mit Docker-Unterstuetzung | Linux (Ubuntu/Debian) |
| **Docker** | Docker CLI + Docker Compose v2 | Docker Desktop oder Docker Engine |

### Installationsmethoden

1. **Docker Compose** (Standard): Einfachste Methode, ideal fuer kleine/mittlere Instanzen
2. **One-Click Deployments:** DigitalOcean Marketplace, Akamai, AWS Marketplace
3. **Kubernetes/Docker Swarm:** Fuer groessere Deployments
4. **Coolify:** Platform-as-a-Service Wrapper fuer vereinfachtes Management
5. **Manuell auf VPS:** Z.B. Hetzner Cloud (ab ~5 EUR/Monat)

### Self-Hosting auf Hetzner (TSV-relevant)

Ein CX22 Server (2 vCPU, 4 GB RAM) bei Hetzner fuer ~4,50 EUR/Monat waere ausreichend.
Installation via Docker Compose, keine Lizenzkosten. Webbasierter Installations-Wizard
seit Version 1.9.0.

---

## 4. Auth Features

### Unterstuetzte Authentifizierungsmethoden

| Methode | Status | Details |
|---|---|---|
| Email & Passwort | Ja | Argon2 Hashing |
| Phone/SMS | Ja | SMS-basierte Verifizierung |
| Magic Links | Ja | Passwortloser Login via E-Mail |
| Email OTP | Ja | Zeitbasiertes Einmalpasswort per E-Mail |
| OAuth2 Social Login | Ja | 30+ Provider (Google, Apple, GitHub, Facebook, Microsoft, etc.) |
| Anonymous Sessions | Ja | Gaeste-Sessions, spaeter konvertierbar |
| JWT Tokens | Ja | Token-basierte Delegation |
| Custom Tokens | Ja | Biometrie, Passkeys etc. |
| MFA / 2FA | Ja | Multi-Faktor-Authentifizierung |
| SSR-kompatibel | Ja | Server-Side Rendering Auth Flows |

### OAuth2 Provider (Auswahl der 30+)

Google, Apple, Microsoft, GitHub, GitLab, Facebook, Amazon, Discord, Slack, Spotify,
Twitch, LinkedIn, PayPal, Bitbucket, Dropbox, Yahoo, Yandex, VK, Box, Salesforce,
WordPress, Autodesk, Auth0 und weitere.

### OIDC Provider Support

| Aspekt | Status |
|---|---|
| **Appwrite als OIDC Consumer** | Ja — Generischer OIDC-Adapter vorhanden (seit v1.4) |
| **Appwrite als OIDC Provider** | **NEIN** — Appwrite kann NICHT als Identity Provider fuer externe Apps dienen |

> **KRITISCH fuer TSV:** Appwrite kann nicht als zentraler OIDC Provider fungieren,
> ueber den sich andere Apps (TYPO3, Base44, FastAPI) per SSO anmelden. Appwrite ist
> ein OIDC **Consumer**, kein **Provider**. Das bedeutet: Appwrite kann sich bei
> Keycloak/Zitadel anmelden, aber TYPO3 kann sich nicht bei Appwrite anmelden.

### Teams und Organisationen

- **Teams:** Gruppen von Usern mit gemeinsamen Berechtigungen
- **Team-Rollen:** Owner, Developer, Editor, Analyst (Console-Rollen)
- **Custom Roles:** Angekuendigt fuer Scale/Enterprise Tier
- **Einladungssystem:** Mitglieder per E-Mail einladen
- **Berechtigungsmodell:** Team-basiert + Label-basiert (kein echtes RBAC)
- **Privacy-Controls:** Mitgliedernamen/-E-Mails koennen verborgen werden

### User Management UI

- Eingebaute Admin-Console (Web-basiert)
- User-Liste, Suche, Filter
- Team-Verwaltung ueber UI
- Einladungen und Rollen-Zuweisung

### Was fehlt

- **Kein OIDC Provider** — kann nicht als Identity Provider fuer Drittanwendungen dienen
- **Kein SAML Support** — keine Enterprise-SSO-Integration
- **Kein echtes RBAC** — nur Team-basierte Berechtigungen + Labels, keine feingranularen Rollen
- **Keine Organizations** im Sinne von Multi-Tenant (Teams sind nicht dasselbe)
- **Kein SCIM Provisioning** — keine automatische User-Synchronisation

---

## 5. Datenbank

### Datenbanktyp

- **Intern:** MongoDB (Standard seit v1.9.0) oder MariaDB (Legacy, weiterhin unterstuetzt)
- **API:** Document-orientiert mit SQL-aehnlicher Abfrage-Syntax
- **Struktur:** Databases > Tables (Collections) > Rows (Documents)
- **Wechsel in v1.9.0:** MongoDB ist jetzt der Standard; MariaDB bleibt als Option

### Abfrage-Capabilities

| Feature | Status |
|---|---|
| Pagination | Ja |
| Sortierung | Ja |
| Filterung (Operatoren) | Ja |
| Geo-Queries | Ja |
| Beziehungen (Relationships) | Ja |
| Transaktionen | Ja |
| Bulk Operations | Ja |
| Atomare numerische Operationen | Ja |
| CSV Import/Export | Ja |
| Offline Sync | Ja (aber eigene Sync-Logik noetig) |
| Backups | Ja |
| AI Suggestions | Ja |
| Type Generation | Ja |

### Limitierungen der Datenbank

- **Kein SQL** — keine direkten SQL-Queries moeglich
- **Kein Row-Level Security (RLS)** wie bei Supabase/PostgreSQL
- **Kein Full-Text Search** auf PostgreSQL-Niveau
- **Keine PostgreSQL-Extensions** (PostGIS, pg_vector etc.)
- Berechtigungen auf Document-Level, nicht auf Row-Level via Policies

---

## 6. Functions (Serverless)

### Unterstuetzte Runtimes

| Runtime | Version |
|---|---|
| Node.js | 21.x |
| Python | 3.12 |
| Python ML | Spezial-Runtime fuer Machine Learning |
| Bun | 1.0.29 |
| Deno | 1.40 |
| PHP | 8.3 |
| Ruby | 3.3 |
| Java | 18 |
| Kotlin | 1.9 |
| Swift | 5.9 |
| Dart | 3.3 |

### Execution Limits

| Parameter | Wert |
|---|---|
| **Max Timeout (Self-Hosted)** | 900 Sekunden (15 Minuten) |
| **Max Timeout (Cloud, synchron)** | 30 Sekunden |
| **Async Executions** | Empfohlen fuer lange Tasks |
| **Isolierung** | Eigener Container pro Function |
| **Eigene URL** | Jede Function bekommt eigene URL |

### Trigger

- HTTP-Requests
- SDK-Methoden
- Server Events
- Webhooks
- Scheduled Executions (Cron)

### Deployment

- Git-Integration (GitHub) mit Auto-Deploy
- Manuelles Deployment
- Templates verfuegbar

---

## 7. Storage

### Features

| Feature | Details |
|---|---|
| **Dateigroesse** | Keine Groessenbeschraenkung |
| **Chunked Upload** | Ja, 5 MB Chunks |
| **Verschluesselung** | Standard aktiviert (Dateien bis 20 MB) |
| **Antivirus** | Integriert (Dateien bis 20 MB) |
| **Kompression** | Integriert (Dateien bis 20 MB) |
| **Image Transformation** | Ja — Resize, Format-Konvertierung, Preview-Generation |
| **Buckets** | Organisationseinheit mit eigenen Berechtigungen |
| **Externe Backends** | S3, Backblaze B2, Wasabi, DigitalOcean Spaces |

### Wichtige Einschraenkung

> Dateien ueber 20 MB werden **ohne** Verschluesselung, Kompression und Antivirus
> gespeichert (als Plain Files).

### Limits (Cloud)

| Tier | Storage |
|---|---|
| Free | 2 GB |
| Pro | 150 GB |

---

## 8. EU Data Residency / DSGVO

### Cloud-Regionen

| Region | Standort | Status |
|---|---|---|
| **FRA** | Frankfurt, Deutschland | Verfuegbar (erste Region, seit Cloud-Beta) |
| NYC | New York, USA | Verfuegbar |
| SYD | Sydney, Australien | Verfuegbar |

### DSGVO-Bewertung

| Aspekt | Bewertung |
|---|---|
| **EU-Rechenzentrum** | Ja — Frankfurt als primaere EU-Region |
| **Regionswahl** | Ja — Bei Projekterstellung waehlbar |
| **Datenhoheit** | Bei Self-Hosting vollstaendig gewaehrleistet |
| **Unternehmenssitz** | Israel/USA (zu beachten bei Cloud-Nutzung) |
| **Auftragsverarbeitung (AVV/DPA)** | Auf Anfrage (Enterprise) |

### Empfehlung fuer TSV

- **Cloud:** Frankfurt-Region waehlen — Daten bleiben in der EU
- **Self-Hosting auf Hetzner (Nuernberg/Falkenstein):** Volle Kontrolle, DSGVO-konform
- **Fuer Vereinsdaten (Mitglieder):** Self-Hosting oder Cloud mit AVV empfohlen

---

## 9. Cloud vs Self-Hosted

### Feature-Vergleich

| Aspekt | Cloud | Self-Hosted |
|---|---|---|
| **Features** | Alle | Alle (identisch) |
| **Lizenzkosten** | Tier-abhaengig | Kostenlos (BSD 3-Clause) |
| **Setup** | Sofort | Docker Compose Setup noetig |
| **Wartung** | Keine (managed) | Eigenverantwortung |
| **Updates** | Automatisch | Manuell |
| **Backup** | Inkludiert | Eigenverantwortung |
| **Skalierung** | Automatisch | Manuell |
| **Performance** | Gelegentlich 2-10x langsamer als Self-Hosted | Schneller (direkte Hardware) |
| **DSGVO** | Frankfurt-Region waehlbar | Volle Kontrolle |
| **Datenbanken** | MongoDB | MongoDB oder MariaDB (waehlbar) |
| **Monitoring** | Inkludiert | Eigenverantwortung |
| **Support** | Community (Free) / Email (Pro) | Community |
| **Ressourcen-Limits** | Tier-basiert | Nur durch Hardware begrenzt |

### Wichtiger Unterschied

Self-Hosted hat **keine** Ressourcen-Limits (MAU, Bandwidth, Storage, Functions etc.) —
nur durch die verfuegbare Hardware begrenzt. Alle Features sind verfuegbar, inklusive
unbegrenzte Datenbanken, Buckets, Functions und Teams.

---

## 10. Architektur (Under the Hood)

### Technologie-Stack

| Komponente | Technologie |
|---|---|
| **Programmiersprache** | PHP (Backend API), TypeScript (Console/SDKs) |
| **Datenbank** | MongoDB (Standard seit v1.9) oder MariaDB |
| **Cache/Queue** | Redis |
| **Message Queue** | Redis + Resque (Pub/Sub) |
| **Metriken/Stats** | InfluxDB (Time-Series fuer API-Usage) |
| **Reverse Proxy** | Traefik (Ingress, TLS) |
| **Container** | Docker (~18 Microservices) |
| **Passwort-Hashing** | Argon2 |

### Microservices-Architektur

Appwrite laeuft als Multi-Container Docker-Anwendung mit ~18 separaten Services:
- API Layer (schnell, mit In-Memory-Caching)
- Background Workers (ressourcenintensive Tasks)
- Message Queue (Lastverteilung)
- Scheduler (Cron-Tasks)
- Realtime Server (WebSocket)
- Jeder Service skaliert unabhaengig

### API-Schnittstellen

- **REST API** — Primaer
- **GraphQL API** — Vollstaendig unterstuetzt
- **WebSocket API** — Fuer Realtime
- **gRPC** — Nicht unterstuetzt

---

## 11. SDKs

### Client SDKs

| SDK | Version | Plattform |
|---|---|---|
| **Web (JavaScript)** | v23.0.0 | Browser, React, Vue, Angular, Svelte |
| **Flutter** | v22.0.0 | iOS, Android, Web |
| **React Native** | v0.25.0 | iOS, Android (Beta) |
| **Apple** | v15.0.0 | iOS, macOS |
| **Android/Kotlin** | v13.0.0 | Android |

### Server SDKs

| SDK | Version | Sprache |
|---|---|---|
| **Node.js** | v22.1.3 | JavaScript/TypeScript |
| **Python** | v16.0.0 | Python |
| **PHP** | v20.2.1 | PHP |
| **Dart** | v21.3.0 | Dart |
| **Ruby** | v21.1.0 | Ruby |
| **.NET** | v1.0.0 | C# |
| **Go** | v1.0.0 | Go |
| **Swift** | v15.2.0 | Swift |
| **Kotlin** | v14.1.0 | Kotlin |
| **Rust** | v0.2.0 | Rust (Beta) |

### Relevanz fuer TSV

- **Python SDK (v16.0.0):** Vorhanden, fuer FastAPI-Backend nutzbar
- **Web SDK (v23.0.0):** Vorhanden, fuer React-Frontend nutzbar
- **Node.js SDK (v22.1.3):** Vorhanden, fuer Server-Side-Rendering
- Kein dediziertes React-SDK — Standard Web SDK wird verwendet

---

## 12. Realtime

### Capabilities

- **WebSocket-basiert** — Echtzeit-Updates innerhalb von Millisekunden
- **Channel-Subscriptions** — Abonnieren spezifischer Ressourcen (DB, Auth, Storage etc.)
- **Permissions-aware** — User erhaelt nur Updates fuer freigegebene Ressourcen
- **Single Connection** — Ein WebSocket fuer alle Channels

### Limits

| Parameter | Free | Pro |
|---|---|---|
| Concurrent Connections | 250 | 500 |
| Messages/Monat | 2 Mio. | 6 Mio. |

### Bekannte Einschraenkungen

- Bei Channel-Aenderung wird die gesamte WebSocket-Verbindung neu aufgebaut
- Authentifizierung nach Subscription-Erstellung erfordert Neuverbindung
- Subscription-Management muss sorgfaeltig implementiert werden
  (Duplikate und Race Conditions moeglich)

---

## 13. Messaging

### Unterstuetzte Kanaele

| Kanal | Provider |
|---|---|
| **E-Mail** | Mailgun, SendGrid, SMTP |
| **SMS** | Twilio, MSG91, Telesign, Textmagic, Vonage |
| **Push Notifications** | APNs (Apple), FCM (Firebase/Google) |

### Features

- **Topics:** Nachrichten an Gruppen senden
- **Targets:** Spezifische Empfaenger
- **Personalisierung:** Individuelle Kommunikation
- **Use Cases:** Marketing, Updates, Echtzeit-Alerts, 2FA-Codes

### Limits

| Tier | Messages/Monat | Topics |
|---|---|---|
| Free | 1.000 | 1 |
| Pro | Unbegrenzt | Unbegrenzt |

### Zusaetzlich im Pro/Enterprise

- WhatsApp-Integration
- Slack-Integration

---

## 14. Sites (Web Hosting)

Seit Oktober 2025 bietet Appwrite auch Web-Hosting ("Appwrite Sites"):

- **Static Sites:** SPAs, Dokumentation, Blogs
- **SSR:** Next.js, Nuxt, SvelteKit, Astro, Remix, Angular
- **CSR:** Client-Side Rendering
- **Git-Integration:** Auto-Deploy bei Push
- **Preview Deployments:** Automatisch fuer Pull Requests
- **Kostenlos** in allen Tiers (unbegrenzte Sites)

> Positioniert als Open-Source Alternative zu Vercel/Netlify.

---

## 15. Bekannte Limitierungen

### Kritische Limitierungen

| Limitierung | Impact |
|---|---|
| **Kein OIDC Provider** | Kann nicht als zentraler Identity Provider fuer Multi-App SSO dienen |
| **Kein SAML** | Keine Enterprise-SSO-Integration |
| **Kein echtes RBAC** | Nur Teams + Labels, keine feingranularen Rollen-Policies |
| **Kein RLS (Row-Level Security)** | Kein DB-seitiges Berechtigungskonzept wie bei PostgreSQL |
| **Kein SQL-Zugriff** | Keine direkten Datenbank-Queries moeglich |

### Operationale Limitierungen

| Limitierung | Details |
|---|---|
| **Free Tier:** 1 DB, 1 Bucket, 2 Functions | Kann fuer komplexere Projekte restriktiv sein |
| **Storage-Verschluesselung >20 MB** | Dateien ueber 20 MB werden unverschluesselt gespeichert |
| **Cloud-Performance** | Kann 2-10x langsamer sein als Self-Hosted |
| **Offline Sync** | Muss selbst implementiert werden (keine eingebaute Library) |

### Community-genannte Probleme

- Dokumentation teilweise unvollstaendig, fehlende Tutorials
- Auth + DB Interaktion gelegentlich buggy
- Grosser Preissprung zwischen Pro (25 USD) und Enterprise
- Dashboard fehlen teilweise Basic-Controls
- Lernkurve bei komplexeren Setups

---

## 16. Community und Reife

### Projekt-Metriken

| Metrik | Wert |
|---|---|
| **GitHub Stars** | ~55.600 |
| **Lizenz** | BSD 3-Clause |
| **Erste Version** | 2019 |
| **Aktuell** | v1.9.0 (April 2026) |
| **Sprachen** | TypeScript (63.7%), PHP (35%) |
| **Maintainer** | ~550 weltweit |
| **Community-Groesse** | 150.000+ Entwickler |

### Finanzierung

| Runde | Betrag | Datum | Lead-Investoren |
|---|---|---|---|
| Seed | 10 Mio. USD | Sept 2021 | Ibex Investors, Seedcamp, Bessemer Venture Partners |
| Series A | 27 Mio. USD | April 2022 | Tiger Global Management, Bessemer Venture Partners |
| **Gesamt** | **37 Mio. USD** | | |

### Einschaetzung Reife

- **Produktiv einsetzbar:** Ja, seit Cloud GA (2023)
- **Aktive Entwicklung:** Regelmaessige Releases, aktive Community
- **Nachhaltigkeit:** Gut finanziert (37M USD), wachsende Nutzerbasis
- **Risiko:** Noch kein profitables Geschaeftsmodell offensichtlich; VC-funded

---

## 17. Vergleich mit Firebase und Supabase

### Feature-Matrix

| Feature | Appwrite | Supabase | Firebase |
|---|---|---|---|
| **Open Source** | Ja (BSD 3-Clause) | Ja (Apache 2.0) | Nein (Proprietary) |
| **Self-Hosting** | Ja (alle Features) | Ja (eingeschraenkt) | Nein |
| **Datenbank** | MongoDB/MariaDB (Document) | PostgreSQL (Relational) | Firestore (NoSQL) |
| **SQL-Zugriff** | Nein | Ja (direkt) | Nein |
| **Row-Level Security** | Nein | Ja (nativ in PostgreSQL) | Security Rules |
| **Auth** | 30+ OAuth, MFA, JWT | 30+ OAuth, MFA, JWT | Google-first, OAuth |
| **OIDC Provider** | Nein | Nein (in Arbeit) | Nein |
| **Realtime** | WebSocket | PostgreSQL Replication | Native Realtime Sync |
| **Functions** | 11 Runtimes, Docker-isoliert | Edge Functions (Deno) | Cloud Functions |
| **Storage** | Ja, mit Image Transform | Ja, mit Image Transform | Cloud Storage |
| **Messaging** | Email, SMS, Push | Nein (Drittanbieter) | FCM (Push) |
| **GraphQL** | Ja | Ja (via pg_graphql) | Nein |
| **EU Region** | Ja (Frankfurt) | Ja (mehrere EU) | Ja (EU) |
| **Vendor Lock-in** | Gering (Open Source) | Gering (PostgreSQL) | Hoch (Proprietary) |
| **Preis (Einstieg)** | 0 USD (75K MAU) | 0 USD (50K MAU) | 0 USD (Spark Plan) |
| **Preis (Paid)** | 25 USD/Monat | 25 USD/Monat | Pay-as-you-go |
| **Community (Stars)** | ~55K | ~80K | N/A (nicht OSS) |

### Staerken von Appwrite gegenueber Konkurrenz

- **Self-Hosting:** Vollstaendige Feature-Paritaet (Supabase Self-Hosted ist eingeschraenkt)
- **All-in-One:** Auth + DB + Storage + Functions + Messaging + Hosting in einer Plattform
- **Performance Self-Hosted:** Uebertrifft Supabase bei hoher Last auf guenstiger Hardware
- **BSD-Lizenz:** Maximal permissiv
- **Messaging integriert:** Email, SMS, Push — bei Supabase nicht nativ
- **Sites/Hosting:** Integriertes Web-Hosting (Vercel-Alternative)

### Schwaechen von Appwrite gegenueber Konkurrenz

- **Keine relationale DB:** Kein PostgreSQL, kein SQL, kein RLS
- **Kleinere Community** als Supabase
- **Kein OIDC Provider:** Weder Appwrite noch Supabase koennen als IdP dienen
- **Weniger mature** als Firebase
- **Keine Extensions/Plugins:** PostgreSQL-Ecosystem (PostGIS etc.) nicht verfuegbar

---

## 18. Bewertung fuer TSV Wartenberg

### Abgleich mit TSV-Anforderungen

| Anforderung | Prioritaet | Appwrite | Bewertung |
|---|---|---|---|
| OIDC Provider (Multi-App SSO) | **Muss** | **NEIN** | **NICHT ERFUELLT** |
| DSGVO-konform / EU-Daten | Muss | Ja (Frankfurt / Self-Hosted) | Erfuellt |
| Admin-UI fuer Benutzerverwaltung | Muss | Ja (Console) | Erfuellt |
| User Self-Registration | Muss | Ja | Erfuellt |
| MFA / 2FA | Muss | Ja | Erfuellt |
| Social Login (Google, Apple) | Kann | Ja (30+ Provider) | Erfuellt |
| Kein Ops-Aufwand | Soll | Ja (Cloud Free Tier) | Erfuellt |
| Kosten < 30 EUR/Monat | Soll | 0 EUR (Free Tier ausreichend) | Erfuellt |
| Kosten = 0 EUR | Ideal | Ja (Free Tier: 75K MAU) | Erfuellt |

### Fazit

**Appwrite ist als alleinige Auth-Loesung fuer den TSV NICHT geeignet**, weil:

1. **Appwrite kann nicht als OIDC Provider dienen.** Das ist die Kernfunktion, die fuer
   Multi-App SSO (TYPO3, Base44, React-App, FastAPI) benoetigt wird. TYPO3 kann sich
   nicht "bei Appwrite anmelden".

2. **Kein SAML**, kein SCIM — keine Enterprise-SSO-Integration moeglich.

3. **Kein echtes RBAC** — fuer ein Vereins-Management-System mit verschiedenen Rollen
   (Vorstand, Abteilungsleiter, Helfer, Mitglied) muesste man RBAC selbst bauen.

### Aber: Appwrite als BaaS (nicht Auth)

Appwrite koennte als **Backend-Plattform** (Datenbank, Storage, Functions, Messaging)
eingesetzt werden, wenn die Auth ueber eine separate Loesung (Zitadel, Keycloak, Auth0,
Clerk) laeuft:

- **Datenbank:** Fuer Vereinsdaten (Events, Material, Buchungen)
- **Storage:** Fuer Dokumente, Bilder
- **Functions:** Fuer Backend-Logik
- **Messaging:** Fuer Benachrichtigungen
- **Sites:** Fuer Web-Hosting

In diesem Szenario wuerde Appwrite ueber seinen OIDC-Consumer den Login an einen
externen Identity Provider delegieren.

### Empfehlung

| Szenario | Empfehlung |
|---|---|
| Auth-Loesung (OIDC Provider) | **Nicht Appwrite** — Zitadel, Keycloak oder Clerk |
| Backend-Plattform (DB + Storage + Functions) | Appwrite ist eine Option, Alternative: Supabase |
| All-in-One (Auth + Backend) | Supabase hat leichten Vorteil (PostgreSQL, groessere Community) |
| Self-Hosted All-in-One | Appwrite hat Vorteil (volle Feature-Paritaet, guenstiger) |

---

## Quellen

- [Appwrite Pricing](https://appwrite.io/pricing)
- [Appwrite Self-Hosting Docs](https://appwrite.io/docs/advanced/self-hosting)
- [Appwrite GitHub Repository](https://github.com/appwrite/appwrite)
- [Appwrite Auth Docs](https://appwrite.io/docs/products/auth)
- [Appwrite Databases Docs](https://appwrite.io/docs/products/databases)
- [Appwrite Functions Docs](https://appwrite.io/docs/products/functions)
- [Appwrite Storage Docs](https://appwrite.io/docs/products/storage)
- [Appwrite Messaging Docs](https://appwrite.io/docs/products/messaging)
- [Appwrite Realtime Docs](https://appwrite.io/docs/apis/realtime)
- [Appwrite SDKs](https://appwrite.io/docs/sdks)
- [Appwrite Regions](https://appwrite.io/docs/products/network/regions)
- [Appwrite Pricing Update Blog](https://appwrite.io/blog/post/appwrite-pricing-update)
- [Appwrite New Pricing Changelog](https://appwrite.io/changelog/entry/2025-08-08)
- [Appwrite OIDC Issue #4299](https://github.com/appwrite/appwrite/issues/4299)
- [Appwrite vs Supabase vs Firebase (UI Bakery)](https://uibakery.io/blog/appwrite-vs-supabase-vs-firebase)
- [Appwrite Funding (Crunchbase)](https://www.crunchbase.com/organization/appwrite)
- [Appwrite Teams Docs](https://appwrite.io/docs/products/auth/teams)
- [Appwrite Architecture (DEV Community)](https://dev.to/appwrite/30daysofappwrite-appwrite-s-building-blocks-1936)
- [Appwrite MongoDB Support](https://appwrite.io/changelog/entry/2026-04-01)
- [Appwrite Sites Announcement](https://appwrite.io/blog/post/announcing-appwrite-sites)
