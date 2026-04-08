# ADR-006: Zentrale Authentifizierungsloesung

## Status

Ueberarbeitet (2026-03-23) — ersetzt Vorschlag vom 2026-03-22

## Kontext

Die Multi-App-Architektur (ADR-001) erfordert einen zentralen Identity Provider.
Acht Self-Hosted-Loesungen wurden evaluiert (siehe `architektur/auth-evaluation.md`).
Anschliessend wurden neun SaaS/PaaS-Alternativen geprueft (siehe `architektur/auth-saas-paas-evaluation.md`).

### Kernproblem Self-Hosting

Das ehrenamtliche Team kann den Ops-Aufwand fuer einen selbst gehosteten Identity Provider
nicht dauerhaft leisten: Backup, Security-Patches, HA, DR, Monitoring (~4-8 Stunden/Monat).

### Zitadel Cloud Free — warum es nicht reicht

Die SaaS-Version von Zitadel wurde zuerst favorisiert, scheitert aber am Free Tier:

| Aspekt | Erwartet | Tatsaechlich |
|--------|----------|-------------|
| EU-Datenresidenz | Inklusive | Nur ab Pro + Data Location ($200/Mo) |
| Custom Domain | Inklusive | Nur ab Pro ($100/Mo) |
| Infrastruktur | EU | US Google Cloud Platform (CLOUD Act) |

Bei $200/Monat fuer EU-Daten ist Zitadel Cloud fuer einen Verein nicht tragbar.

## Entscheidung

**Kinde** (SaaS) wird als zentraler Identity Provider eingesetzt.
Startphase mit Free Tier, bei Bedarf Upgrade auf Pro ($25/Monat).

### Begruendung

1. **EU-Datenresidenz auf allen Tiers** (inkl. Free, kein Aufpreis)
2. **10.500 MAU kostenlos** — TSV braucht geschaetzt 50-200 MAU
3. **Vollstaendiger OIDC Provider** — Standard-Endpoints verifiziert
4. **MFA, RBAC, Admin-UI** alles im Free Tier
5. **ISO 27001 + SOC 2 Type 2 + GDPR** zertifiziert
6. **Ops-Aufwand = 0** — Backup, HA, DR, Security managed
7. **Standard-OIDC** → Migration zu anderem Provider jederzeit moeglich

### Verworfene Alternativen (aktualisiert)

| Loesung | Grund fuer Ablehnung |
|---------|---------------------|
| **Zitadel Self-Hosted** | Ops-Aufwand (Backup, Security, HA, DR) fuer Ehrenamtliche zu hoch |
| **Zitadel Cloud** | EU-Daten erst ab $200/Mo, GCP = CLOUD Act Risiko |
| Auth0 | MFA erst ab $35/Mo, EU-Daten nur auf teureren Tiers |
| Clerk | Keine EU-Datenresidenz moeglich |
| Ory Network | Ab $70/Mo (kein Free Production Tier) |
| FusionAuth Cloud | Ab $125/Mo |
| AWS Cognito | Admin-UI ungeeignet fuer nicht-technische Vereins-Admins |
| Authentik | Zu viel RAM (2+ GB), braucht Redis (Self-Hosted) |
| Keycloak | Java/JVM = 1.5+ GB RAM, Overkill (Self-Hosted) |
| Custom FastAPI | 3-6 Monate Aufwand, Sicherheitsrisiko |

### Fallback-Strategie

Sollte Kinde den Dienst einstellen oder untragbar werden:
1. **Kurzfristig:** Zitadel Self-Hosted auf Hetzner (OSS, AGPL-3.0)
2. **Migration:** OIDC-Standard → nur Client-Konfiguration aendern, kein Code-Umbau

## Kinde als Single Source of Truth

Kinde verwaltet **alles, was mit Personen und Vereinsstruktur zu tun hat**:
Authentifizierung (Login), Autorisierung (Rollen) UND Vereinsstruktur (Sparten).

Sparten werden als **Kinde Organizations** abgebildet. Damit sind User, Rollen und
Sparten-Zuordnungen fuer alle Apps ueber JWT und Management API verfuegbar.

| Zustaendigkeit | Kinde | tsv-hub |
|---------------|-------|---------|
| Login, Passwort, MFA, SSO | ✓ | — |
| Self-Registration | ✓ | — |
| Account sperren/loeschen | ✓ | — |
| Rollen (admin, spartenleiter, trainer, mitglied) | ✓ | Liest aus JWT |
| Sparten-Zuordnung (User ↔ Sparte) | ✓ | Liest aus JWT |
| Sparten-Daten (Beitragshoehe etc.) | ✓ (Org Properties) | — |
| Fachliche Daten (Events, Material, Buchungen) | — | ✓ |

### Modell: Sparten als Kinde Organizations

```
Kinde Business: "TSV Wartenberg"
├── Org "Gesamtverein" (org_gesamt)   → admin, mitglied
├── Org "Fussball"     (org_fussball) → spartenleiter, trainer, mitglied
├── Org "Tennis"       (org_tennis)   → spartenleiter, trainer, mitglied
└── Org "Turnen"       (org_turnen)   → spartenleiter, trainer, mitglied
```

**Vorteile:**
- Alle Apps (tsv-hub, Beitragseinzug, TYPO3, Dashboard) greifen auf dieselben Daten zu
- Kein Sync zwischen Kinde und tsv-hub noetig
- Beitragseinzug kann Sparten + Beitragshoehe direkt aus Kinde API lesen
- Rollenverwaltung zentral in einem Admin-UI (Kinde Dashboard)

Details: siehe `architektur/user-management-konzept.md`

## Konsequenzen

- **Positiv**: SSO ueber alle Apps, DSGVO-konform (EU-Daten), 0 EUR Kosten (Free Tier),
  kein Ops-Aufwand, professionelles HA/DR/Backup, zentrale User/Rollen/Sparten-Verwaltung
- **Negativ**: Vendor-Abhaengigkeit (kein Open Source), australisches Unternehmen
  (Daten aber in EU), relativ junges Unternehmen (gegruendet 2022)
- **Migration**: User + Sparten aus tsv-hub nach Kinde migrieren, Auth-Code durch OIDC ersetzen
- **tsv-hub wird schlanker**: `mitglied_sparten`-Tabelle entfaellt, `mitglieder`-Tabelle
  behaelt nur `kinde_sub` + app-spezifische Daten, Rollen kommen aus JWT
- **Beitragseinzug**: Kann unabhaengig von tsv-hub auf Kinde API zugreifen
- **Base44 HelferHub**: OIDC/SSO nur ab Elite-Plan ($80/Mo) → separates Auth, nur Registrierungslink

## Quellen

- [Kinde Pricing](https://www.kinde.com/pricing/)
- [Kinde User Management](https://www.kinde.com/user-management/)
- [Kinde OIDC Endpoints (Cloudflare Doku)](https://docs.kinde.com/integrate/third-party-tools/cloudflare-zero-trust/)
- [SaaS/PaaS Evaluation](../auth-saas-paas-evaluation.md)
- [Self-Hosted Evaluation](../auth-evaluation.md)
- [Kinde Organizations](https://docs.kinde.com/build/organizations/orgs-for-developers/)
- [Kinde Custom Properties](https://docs.kinde.com/properties/about-properties/)
- [Kinde Roles per Org](https://docs.kinde.com/manage-users/roles-and-permissions/apply-roles-and-permissions-to-users/)
- [Kinde Token Customization](https://docs.kinde.com/build/tokens/token-customization/)
- [Kinde Management API](https://docs.kinde.com/kinde-apis/management/)
- [User Management Konzept](../user-management-konzept.md)
