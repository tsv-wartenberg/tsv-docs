# Auth SaaS/PaaS Evaluation — Managed Alternativen zu Zitadel Self-Hosted

**Stand:** 2026-03-23
**Kontext:** ADR-006 schlaegt Zitadel Self-Hosted vor. Diese Analyse prueft, ob eine
managed SaaS/PaaS-Loesung den Ops-Aufwand (Backup, Security, HA, DR) eliminieren kann.

## Ausgangslage

### Was Self-Hosting bedeutet (Zitadel auf Hetzner)

Der TSV muesste folgende Ops-Aufgaben **selbst** uebernehmen:

| Aufgabe | Aufwand | Risiko bei Vernachlaessigung |
|---------|---------|------------------------------|
| OS-Patches & Updates | Monatlich | Sicherheitsluecken |
| Docker-Image-Updates | Bei Releases | Bekannte CVEs |
| PostgreSQL-Backup | Taeglich (Cron) | Datenverlust |
| Backup-Restore-Tests | Quartalsweise | Backup unbrauchbar |
| SSL-Zertifikate | Auto (Certbot) | Seite nicht erreichbar |
| Firewall & Haertung | Initial + laufend | Unbefugter Zugriff |
| Monitoring & Alerting | Einrichten + pflegen | Ausfaelle unbemerkt |
| Disaster Recovery | DR-Plan + Uebung | Langer Totalausfall |
| Hochverfuegbarkeit | Nicht moeglich (Single Server) | SPOF |
| Kapazitaetsplanung | Bei Wachstum | Performance-Probleme |

**Kernproblem:** Ehrenamtliches Team, kein 24/7 Ops-Personal.

### Anforderungen an eine Auth-Loesung

| Anforderung | Prioritaet | Kommentar |
|-------------|-----------|-----------|
| OIDC Provider (Multi-App SSO) | Muss | TYPO3, Base44, React, FastAPI |
| DSGVO-konform / EU-Daten | Muss | Mitgliederdaten = personenbezogen |
| Admin-UI fuer Benutzerverwaltung | Muss | Nicht-technische Admins |
| User Self-Registration | Muss | Mitglieder registrieren sich selbst |
| MFA / 2FA | Muss | Sicherheitsanforderung |
| Social Login (Google, Apple) | Kann | Nice-to-have |
| Kein Ops-Aufwand (Backup, HA, DR) | Soll | Hauptmotivation dieser Analyse |
| Kosten < 30 EUR/Monat | Soll | Ehrenamtlicher Verein |
| Kosten = 0 EUR (Free Tier) | Ideal | Unter ~200 aktive User realistisch |

### Geschaetzte Nutzerzahlen TSV Wartenberg

| Metrik | Schaetzung |
|--------|-----------|
| Vereinsmitglieder gesamt | 200-800 |
| Registrierte User (alle Apps) | 100-400 |
| Monthly Active Users (MAU) | 50-200 |
| Daily Active Users (DAU) | 10-50 |

---

## Bewertete SaaS/PaaS-Loesungen

### 1. Zitadel Cloud (Schweiz) — SaaS-Version des Self-Hosted-Favoriten

| Aspekt | Details |
|--------|---------|
| **Anbieter** | CAOS AG, Zuerich, Schweiz |
| **Free Tier** | 100 DAU, unbegrenzte User, 3 Identity Provider |
| **Pro** | $100/Monat (25.000 DAU) |
| **EU-Daten** | Ja — Region waehlbar (EU, CH, US, AU), Add-on $100/Mo bei Pro |
| **OIDC Provider** | Ja, OpenID Certified |
| **Admin-UI** | Ja, vollstaendig |
| **Self-Registration** | Ja |
| **MFA** | Ja (TOTP, FIDO2, SMS) |
| **Social Login** | Ja (Google, GitHub, Apple) |
| **Ops-Aufwand** | Keiner — vollstaendig managed |

**Bewertung fuer TSV:**
- Free Tier mit 100 DAU **reicht fuer den TSV** (geschaetzt 10-50 DAU)
- EU-Datenresidenz im Free Tier unklar — muss geprueft werden
- Gleiche Software wie Self-Hosted → einfache Migration in beide Richtungen
- **Risiko:** Free Tier koennte eingestellt oder eingeschraenkt werden

**Kosten TSV:** **0 EUR/Monat** (Free Tier ausreichend)

---

### 2. Kinde (Australien, global)

| Aspekt | Details |
|--------|---------|
| **Anbieter** | Kinde (Melbourne, Australien) |
| **Free Tier** | 10.500 MAU, kein Kreditkarte noetig |
| **Pro** | $25/Monat + $0.0175/MAU ueber 10.500 |
| **EU-Daten** | Ja — Datenresidenz waehlbar auf allen Tiers |
| **OIDC Provider** | Ja (OAuth2, OIDC, SAML, Entra ID) |
| **Admin-UI** | Ja, unbegrenzte Dashboard-Seats |
| **Self-Registration** | Ja |
| **MFA** | Ja |
| **Social Login** | Ja (Google, Apple, GitHub, Slack) |
| **RBAC** | Ja (alle Tiers) |
| **Ops-Aufwand** | Keiner — vollstaendig managed |
| **Zertifizierungen** | ISO 27001, SOC 2 Type 2, GDPR, HIPAA |

**Bewertung fuer TSV:**
- Free Tier mit 10.500 MAU ist **massiv ueberdimensioniert** fuer den TSV
- EU-Datenresidenz **inklusive** auf allen Tiers (kein Aufpreis!)
- Sehr gute OIDC-Unterstuetzung — sollte mit TYPO3 + Base44 funktionieren
- Australisches Unternehmen, aber Daten in EU speicherbar
- Custom Domains erst ab Pro ($25/Mo)
- **Risiko:** Junges Unternehmen, weniger etabliert als Auth0/Keycloak

**Kosten TSV:** **0 EUR/Monat** (Free Tier ausreichend)

---

### 3. Auth0 / Okta (USA)

| Aspekt | Details |
|--------|---------|
| **Anbieter** | Okta Inc., USA (ehemals Auth0) |
| **Free Tier** | 25.000 MAU — aber ohne MFA und RBAC! |
| **Essentials** | $35/Monat (MFA, RBAC, Prod/Dev Environments) |
| **Professional** | $240/Monat |
| **EU-Daten** | Ja — Data Residency verfuegbar (Paid) |
| **OIDC Provider** | Ja, Industriestandard |
| **Admin-UI** | Ja, sehr ausgereift |
| **Self-Registration** | Ja |
| **MFA** | Nur ab Essentials ($35/Mo) |
| **Ops-Aufwand** | Keiner — vollstaendig managed |

**Bewertung fuer TSV:**
- Free Tier grosszuegig bei MAU, aber **MFA fehlt** → fuer Produktion ungeeignet
- Essentials ($35/Mo) ist die realistische Mindest-Stufe
- US-Unternehmen — DSGVO-Compliance ueber DPA, aber Daten potenziell in USA
- EU Data Residency nur auf teureren Paid-Tiers
- Marktfuehrer, extrem stabil und gut dokumentiert
- **Risiko:** Vendor Lock-in, steigende Preise (Okta-Historie)

**Kosten TSV:** **~35 EUR/Monat** (Essentials fuer MFA)

---

### 4. Clerk (USA)

| Aspekt | Details |
|--------|---------|
| **Anbieter** | Clerk Inc., USA |
| **Free Tier** | 50.000 MRU (Monthly Retained Users) |
| **Pro** | $25/Monat + $0.02/MRU ueber 50.000 |
| **EU-Daten** | Nein — keine Regionswahl, nur DPF-Zertifizierung |
| **OIDC Provider** | Eingeschraenkt — Enterprise Connections nur auf Paid |
| **Admin-UI** | Ja |
| **Self-Registration** | Ja |
| **MFA** | Ja (alle Tiers) |
| **Ops-Aufwand** | Keiner — vollstaendig managed |

**Bewertung fuer TSV:**
- Free Tier sehr grosszuegig (50.000 MRU)
- **Keine EU-Datenresidenz** → DSGVO-Risiko fuer Mitgliederdaten
- OIDC als Provider eingeschraenkt — primaer als Auth-SDK gedacht
- Fokus auf React/Next.js — weniger geeignet fuer TYPO3/Base44 Integration
- **Nicht empfohlen** wegen fehlender EU-Daten und eingeschraenktem OIDC

**Kosten TSV:** 0 EUR/Monat — aber DSGVO-problematisch

---

### 5. Ory Network (Deutschland) — Cloud-Version von Ory Kratos+Hydra

| Aspekt | Details |
|--------|---------|
| **Anbieter** | Ory Corp, Muenchen, Deutschland |
| **Free Tier** | Nur Development (kein Production) |
| **Production** | Ab $70/Monat ($0.12/aDAU) |
| **EU-Daten** | Ja — PII-Residenz in EU, US, Japan waehlbar |
| **OIDC Provider** | Ja, Hydra ist OpenID Certified |
| **Admin-UI** | Ja (in Cloud-Version, nicht in OSS) |
| **Self-Registration** | Ja |
| **MFA** | Ja (TOTP, WebAuthn, SMS) |
| **Ops-Aufwand** | Keiner — vollstaendig managed |

**Bewertung fuer TSV:**
- Deutsches Unternehmen → bester DSGVO-Standort
- **Kein kostenloser Production-Tier** → mindestens $70/Monat
- Exzellente Technik (Go, hochperformant)
- Fuer einen ehrenamtlichen Verein zu teuer
- **Nicht empfohlen** wegen Kosten

**Kosten TSV:** **~70 EUR/Monat** (zu teuer)

---

### 6. Descope (USA/Israel)

| Aspekt | Details |
|--------|---------|
| **Anbieter** | Descope, USA (Gruender aus Israel) |
| **Free Tier** | 7.500 MAU |
| **Pro** | $249/Monat (jaehrlich) |
| **EU-Daten** | Ja — Multi-Region Data Residency |
| **OIDC Provider** | Ja (SAML + OIDC Federation) |
| **Admin-UI** | Ja, Drag-and-Drop Flow Builder |
| **MFA** | Ja (alle Methoden) |
| **Ops-Aufwand** | Keiner — vollstaendig managed |

**Bewertung fuer TSV:**
- Free Tier mit 7.500 MAU reicht fuer TSV
- EU Data Residency verfuegbar
- Innovative Drag-and-Drop Flows
- **Risiko:** US-Unternehmen, noch recht jung, Pro-Sprung auf $249/Mo sehr steil
- OIDC-Provider-Faehigkeit muss im Detail geprueft werden (primaer CIAM-fokussiert)

**Kosten TSV:** **0 EUR/Monat** (Free Tier ausreichend)

---

### 7. AWS Cognito (Amazon, EU Frankfurt)

| Aspekt | Details |
|--------|---------|
| **Anbieter** | Amazon Web Services |
| **Free Tier** | 50.000 MAU (User Pools), dauerhaft kostenlos |
| **EU-Daten** | Ja — Region eu-central-1 (Frankfurt) |
| **OIDC Provider** | Ja |
| **Admin-UI** | AWS Console (technisch, nicht fuer Nicht-Techniker) |
| **Self-Registration** | Ja |
| **MFA** | Ja (TOTP, SMS) |
| **Ops-Aufwand** | Gering — managed, aber AWS-Konfiguration komplex |

**Bewertung fuer TSV:**
- Free Tier extrem grosszuegig (50.000 MAU, dauerhaft)
- Daten in Frankfurt → DSGVO-konform
- **Admin-UI ist die AWS Console** → fuer Vereins-Admins ungeeignet
- Konfiguration komplex (CloudFormation, IAM Policies)
- Vendor Lock-in in AWS-Oekosystem
- **Nicht empfohlen** wegen Komplexitaet und fehlender benutzerfreundlicher Admin-UI

**Kosten TSV:** **0 EUR/Monat** — aber hohe Komplexitaet

---

### 8. FusionAuth Cloud (USA)

| Aspekt | Details |
|--------|---------|
| **Anbieter** | FusionAuth, USA |
| **Free Tier** | Nur Self-Hosted (unbegrenzt), Cloud ab $125/Monat |
| **EU-Daten** | Ja — EU-Hosting-Region verfuegbar (Cloud) |
| **OIDC Provider** | Ja (OAuth2, OIDC, SAML) |
| **Admin-UI** | Ja, sehr gut |
| **Ops-Aufwand** | Keiner (Cloud) / Hoch (Self-Hosted) |

**Bewertung fuer TSV:**
- Cloud zu teuer ($125/Mo Minimum)
- Self-Hosted kostenlos, aber gleicher Ops-Aufwand wie Zitadel
- **Nicht empfohlen** wegen Kosten

**Kosten TSV:** **~125 EUR/Monat** (Cloud) oder 0 EUR (Self-Hosted + Ops)

---

### 9. SuperTokens (Indien/USA)

| Aspekt | Details |
|--------|---------|
| **Anbieter** | SuperTokens, Indien/USA |
| **Free Tier** | 5.000 MAU (Cloud), unbegrenzt (Self-Hosted) |
| **Pro** | $0.02/MAU ueber 5.000 |
| **EU-Daten** | Unklar — keine dokumentierte EU-Residenz |
| **OIDC Provider** | Eingeschraenkt — Enterprise SSO (OIDC) nur auf Paid |
| **Admin-UI** | Eingeschraenkt |
| **Ops-Aufwand** | Keiner (Cloud) |

**Bewertung fuer TSV:**
- Guenstiges Preismodell
- **Keine klare EU-Datenresidenz** → DSGVO-Risiko
- OIDC als Provider eingeschraenkt
- **Nicht empfohlen** wegen DSGVO und eingeschraenktem OIDC

---

## Gesamtvergleich

### Ranking nach Eignung fuer TSV Wartenberg (vorlaeufig, siehe aktualisierte Empfehlung unten)

```
                   Eignung fuer TSV
                   ◄── gering                    hoch ──►

  Clerk         ··|                               Keine EU-Daten
  SuperTokens   ····|                             DSGVO unklar
  Cognito       ········|                         Zu komplex
  FusionAuth CL ········|                         Zu teuer
  Ory Network   ··········|                       Zu teuer
  Zitadel CL    ··············|                   EU=$200/Mo!
  Auth0 Ess.    ··················|               $35/Mo, EU unklar
  Descope       ····················|             Gut, aber jung
  Zitadel SH    ··························|       Ops, aber volle Kontrolle
  Kinde Free    ······························|   ★ BESTE SaaS-Option
  Kinde Pro     ································| ★ EMPFEHLUNG ($25/Mo)
```

### Detailvergleich Top-Kandidaten (aktualisiert nach Klaerung)

| Kriterium | Kinde Free | Kinde Pro | Zitadel Self-Hosted | Zitadel Cloud Pro |
|-----------|-----------|-----------|-------------------|-----------------|
| **Kosten/Monat** | **0 EUR** | **~23 EUR** | 0 EUR (+5-11 EUR Server) | ~185 EUR |
| **Free Tier** | 10.500 MAU | 10.500 MAU | — | — |
| **Reicht fuer TSV?** | Ja | Ja | Ja | Ja |
| **EU-Daten** | **Ja (inkl.)** | **Ja (inkl.)** | Ja (Hetzner DE) | Ja (GCP EU, CLOUD Act!) |
| **Custom Domain** | Nein | **Ja** | Ja | Ja |
| **OIDC Provider** | Ja | Ja | OpenID Certified | OpenID Certified |
| **Admin-UI** | Sehr gut | Sehr gut | Sehr gut | Sehr gut |
| **MFA** | Ja | Ja | Ja | Ja |
| **Self-Registration** | Ja | Ja | Ja | Ja |
| **Ops-Aufwand** | **Keiner** | **Keiner** | Hoch (4-8 h/Mo) | Keiner |
| **Backup** | Managed | Managed | Selbst (pg_dump) | Managed |
| **HA / DR** | Managed | Managed | Nicht moeglich | Managed |
| **Security** | Managed | Managed | Selbst | Managed |
| **Herkunft** | Australien | Australien | Schweiz | Schweiz |
| **DSGVO-Staerke** | Hoch (EU-Daten) | Hoch (EU-Daten) | Sehr hoch | Mittel (GCP) |
| **Vendor Lock-in** | Mittel | Mittel | Keiner (OSS) | Gering (OSS) |
| **Open Source** | Nein | Nein | Ja (AGPL-3.0) | Ja (AGPL-3.0) |

---

## Ops-Aufwand: SaaS vs. Self-Hosted

### Was bei SaaS/PaaS ENTFAELLT

| Aufgabe | Self-Hosted | SaaS |
|---------|------------|------|
| Server-Wartung (OS, Docker) | Ehrenamtliche | Anbieter |
| Datenbank-Backup | Cron + Monitoring | Anbieter |
| Backup-Restore-Tests | Manuell | Anbieter |
| SSL-Zertifikate | Certbot + Monitoring | Anbieter |
| Security-Patches | Regelmaessig | Anbieter |
| DDoS-Schutz | Nicht vorhanden | Anbieter |
| Hochverfuegbarkeit (HA) | Nicht moeglich (1 Server) | Anbieter (99.5%+ SLA) |
| Disaster Recovery | Kein DR-Plan | Anbieter |
| Monitoring & Alerting | Selbst aufbauen | Anbieter |
| Kapazitaetsplanung | Manuell | Automatisch |
| **Geschaetzter Zeitaufwand** | **4-8 Stunden/Monat** | **0 Stunden/Monat** |

### Was bei SaaS/PaaS BLEIBT

| Aufgabe | Kommentar |
|---------|-----------|
| OIDC-Konfiguration | Einmalig: Clients fuer TYPO3, Base44, tsv-hub |
| User-Migration | Einmalig: Bestehende User importieren |
| Rollen-/Rechte-Pflege | Laufend: Neue Rollen, Sparten-Zuordnung |
| App-Integration | Einmalig pro App: OIDC-Client konfigurieren |

---

## Klaerung offener Fragen (Stand 2026-03-23)

### Frage 1: EU-Datenresidenz bei Zitadel Cloud Free?

**Antwort: NEIN — nur ab Pro ($100/Mo) + Data Location Add-on ($100/Mo)**

- Free Tier erlaubt **keine Regionswahl**. Der Default ist US (Google Cloud Platform).
- Regionswahl (EU, CH, US, AU) ist ein Pro-Feature.
- Data Location Add-on kostet zusaetzlich $100/Mo auf dem Pro-Tier.
- **Gesamtkosten fuer Zitadel Cloud mit EU-Daten: $200/Monat (~185 EUR)**
- Selbst mit EU-Region: Daten liegen auf **GCP (US-Unternehmen)** → CLOUD Act Risiko.
  Quelle: [GitHub Discussion #10941](https://github.com/zitadel/zitadel/discussions/10941),
  [European Alternatives](https://european-alternatives.eu/product/zitadel)

**DSGVO-Bewertung:** Zitadel Cloud Free ist fuer personenbezogene Mitgliederdaten
eines deutschen Vereins **nicht DSGVO-konform** einsetzbar (US-Default, kein DPA im Free Tier).

### Frage 2: Custom Domain bei Zitadel Cloud Free?

**Antwort: NEIN — nur ab Pro ($100/Mo)**

- Free Tier nutzt eine Zitadel-Subdomain (z.B. `tsv-wartenberg-xyz.zitadel.cloud`)
- Custom Domain (auth.tsv-wartenberg.de) ist im Pro-Tier inkludiert.
  Quelle: [GitHub Discussion #7049](https://github.com/zitadel/zitadel/discussions/7049)

### Frage 3: OIDC-Client-Limit im Free Tier?

**Antwort: Kein dokumentiertes Limit gefunden.**

- Alle Auth-Methoden sind im Free Tier verfuegbar.
- Unbegrenzte User und Organizations.
- Limit liegt bei 100 DAU und 3 externen Identity Providern (eingehend).
- Anzahl eigener OIDC-Clients (ausgehend an TYPO3, Base44, tsv-hub) scheint unbegrenzt.

### Zusatzfrage: Kann Kinde als OIDC Provider fungieren?

**Antwort: JA — vollstaendig OIDC-konform als Provider.**

Kinde stellt alle Standard-OIDC-Endpoints bereit:
- Discovery: `https://<subdomain>.kinde.com/.well-known/openid-configuration`
- JWKS: `https://<subdomain>.kinde.com/.well-known/jwks`
- Authorization: `https://<subdomain>.kinde.com/oauth2/auth`
- Token: `https://<subdomain>.kinde.com/oauth2/token`

Verifiziert durch: [Kinde + Cloudflare Zero Trust Doku](https://docs.kinde.com/integrate/third-party-tools/cloudflare-zero-trust/)

**Custom Domain bei Kinde:** Erst ab Pro ($25/Mo) — Free nutzt `<name>.kinde.com`.

---

## Aktualisierte Empfehlung (nach Klaerung)

### WICHTIG: Zitadel Cloud Free faellt als Primaer-Empfehlung aus!

Die Klaerung der offenen Fragen hat die Bewertung grundlegend veraendert:

| Aspekt | Vorher angenommen | Tatsaechlich |
|--------|-------------------|-------------|
| EU-Daten (Free) | Unklar → evtl. ja | **Nein** — US Default, EU erst ab $200/Mo |
| Custom Domain (Free) | Unklar | **Nein** — erst ab $100/Mo (Pro) |
| CLOUD Act | Nicht betrachtet | **Risiko** — GCP auch in EU-Region |

### Neues Ranking

```
                   Eignung fuer TSV (aktualisiert)
                   ◄── gering                    hoch ──►

  Clerk         ··|                               Keine EU-Daten
  SuperTokens   ····|                             DSGVO unklar
  Cognito       ········|                         Zu komplex
  FusionAuth CL ········|                         Zu teuer
  Ory Network   ··········|                       Zu teuer
  Zitadel CL    ··············|                   EU=$200/Mo!
  Auth0 Ess.    ··················|               $35/Mo, EU unklar
  Descope       ····················|             Gut, aber jung
  Zitadel SH    ··························|       Ops, aber volle Kontrolle
  Kinde Free    ······························|   ★ BESTE SaaS-Option
  Kinde Pro     ································| ★ EMPFEHLUNG ($25/Mo)
```

### Empfehlung 1: Kinde (Free oder Pro)

**Warum Kinde jetzt die beste SaaS-Option ist:**

1. **EU-Datenresidenz auf ALLEN Tiers** (inkl. Free, kein Aufpreis)
2. **10.500 MAU kostenlos** — massiv ueberdimensioniert fuer TSV
3. **Vollstaendiger OIDC Provider** — TYPO3, Base44, FastAPI kompatibel
4. **MFA, RBAC, Admin-UI** alles im Free Tier
5. **ISO 27001 + SOC 2 Type 2 + GDPR** zertifiziert
6. **0 EUR/Monat** (Free) oder **$25/Monat** (Pro, fuer Custom Domain)
7. **Ops-Aufwand = 0** — Backup, HA, DR, Security alles managed

**Free vs. Pro:**

| Feature | Free (0 EUR) | Pro ($25/Mo) |
|---------|-------------|-------------|
| MAU | 10.500 | 10.500 + $0.0175/extra |
| EU-Daten | Ja | Ja |
| OIDC Provider | Ja | Ja |
| MFA | Ja | Ja |
| RBAC | Ja | Ja |
| Admin-UI | Ja | Ja |
| Custom Domain | Nein (`tsv.kinde.com`) | Ja (`auth.tsv-wartenberg.de`) |
| Webhooks | 1 | Unbegrenzt |
| Environments | 2 (Prod+Dev) | 3+ |
| Custom OAuth2 | Nein | Ja |

**Empfehlung:** Mit **Free starten**, bei Bedarf auf **Pro ($25/Mo)** upgraden.

**Nachteile:**
- Kein Self-Hosted-Fallback (kein Open Source)
- Australisches Unternehmen (Daten aber in EU)
- Relativ junges Unternehmen (gegruendet 2022)
- Vendor Lock-in (aber OIDC-Standard = einfache Migration)
- Nicht OpenID Certified (aber OIDC-Endpoints vorhanden)

### Empfehlung 2: Zitadel Self-Hosted (Fallback)

Falls SaaS grundsaetzlich abgelehnt wird oder Kinde den Betrieb einstellt:

**Vorteile:**
- Volle Kontrolle ueber Daten (Hetzner DE)
- Open Source (AGPL-3.0) → kein Vendor Lock-in
- OpenID Certified
- 0 EUR Softwarekosten

**Nachteile (unveraendert):**
- Ops-Aufwand: Backup, Security, HA, DR, Monitoring
- Geschaetzt 4-8 Stunden/Monat ehrenamtliche Arbeit
- Single Point of Failure (ein Server)
- Kein professionelles DR

### Empfehlung 3: Zitadel Cloud Pro (Premium-Option)

Falls Budget vorhanden und Zitadel-Oekosystem gewuenscht:
- $100/Mo (Pro) + $100/Mo (EU Data Location) = **~185 EUR/Monat**
- Custom Domain inklusive
- OpenID Certified, Schweizer Unternehmen
- **Aber:** Daten auf GCP → CLOUD Act Bedenken bleiben

### Nicht empfohlen fuer TSV

| Loesung | Grund |
|---------|-------|
| Zitadel Cloud Free | US-Default, keine EU-Daten, keine Custom Domain |
| Auth0 | MFA kostet $35/Mo, EU-Daten nur auf teureren Tiers |
| Clerk | Keine EU-Datenresidenz |
| Ory Network | $70/Mo Minimum |
| FusionAuth Cloud | $125/Mo Minimum |
| AWS Cognito | Zu komplex, keine benutzerfreundliche Admin-UI |
| SuperTokens | DSGVO unklar, eingeschraenktes OIDC |
| Descope | Vielversprechend aber jung, US-basiert |

---

## Naechste Schritte

1. **Kinde Free Tier testen (Proof of Concept):**
   - Account erstellen auf kinde.com
   - EU-Region waehlen bei Setup
   - OIDC-Client fuer tsv-hub (FastAPI) konfigurieren
   - OIDC-Kompatibilitaet mit TYPO3-Extension testen
   - Base44 HelferHub OIDC-Anbindung pruefen
   - Admin-UI Tauglichkeit fuer Vereins-Admins bewerten

2. **Entscheidung treffen:**
   - Kinde SaaS vs. Zitadel Self-Hosted
   - Budget-Freigabe fuer Kinde Pro ($25/Mo) bei Bedarf?

3. **ADRs aktualisieren:**
   - ADR-006 ueberarbeiten: Kinde statt/neben Zitadel
   - ADR-003 ggf. anpassen: Auth-Service nicht mehr auf Hetzner

## Quellen

- [Zitadel Cloud Pricing](https://zitadel.com/pricing)
- [Kinde Pricing](https://www.kinde.com/pricing/)
- [Auth0 Pricing](https://auth0.com/pricing)
- [Clerk Pricing](https://clerk.com/pricing)
- [Ory Network Pricing](https://www.ory.com/pricing)
- [Descope Pricing](https://www.descope.com/pricing)
- [AWS Cognito Pricing](https://aws.amazon.com/cognito/pricing/)
- [FusionAuth Pricing](https://fusionauth.io/pricing)
- [SuperTokens Pricing](https://supertokens.com/pricing)
- [Auth0 Plans Update](https://auth0.com/blog/auth0-plans-got-an-upgrade/)
- [Clerk GDPR](https://clerk.com/legal/gdpr)
- [Clerk New Plans 2026](https://clerk.com/changelog/2026-02-05-new-plans-more-value)
- [Zitadel Billing Help](https://help.zitadel.com/pricing-and-billing-of-zitadel-services)
- [Auth0 Pricing Guide](https://www.saasworthy.com/blog/auth0-pricing-plans-guide)
- [Top 10 Auth Services 2026](https://www.nucamp.co/blog/top-10-authentication-services-and-libraries-in-2026-from-free-to-enterprise)
- [Zitadel Custom Domain Discussion](https://github.com/zitadel/zitadel/discussions/7049)
- [Zitadel Cloud Act / Data Sovereignty Discussion](https://github.com/zitadel/zitadel/discussions/10941)
- [Zitadel auf European Alternatives](https://european-alternatives.eu/product/zitadel)
- [Zitadel Cloud Instances Doku](https://zitadel.com/docs/guides/manage/cloud/instances)
- [Kinde + Cloudflare Zero Trust (OIDC Provider)](https://docs.kinde.com/integrate/third-party-tools/cloudflare-zero-trust/)
- [Kinde Custom Domain Doku](https://docs.kinde.com/build/domains/organization-custom-domain/)
