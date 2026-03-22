# Base44 — Plattformbewertung und Architekturentscheidung

## Was ist Base44?

Base44 ist ein AI-powered No-Code/Low-Code App Builder. Apps werden per Prompt beschrieben, Base44 generiert Frontend, Backend, Datenbank und Hosting. Alles laeuft auf der Base44-Plattform — es gibt keinen separaten Deployment-Prozess.

## Kostenmodell

### Preisstufen (Stand Maerz 2026)

| Plan | Preis/Monat | Message Credits | Integration Credits |
|------|-------------|-----------------|-------------------|
| Free | $0 | Limitiert | Limitiert |
| Starter | $16 | Mehr | Mehr |
| Builder | $40 | Noch mehr | Noch mehr |
| Elite | $80 | Viel | Viel |
| Enterprise | $160+ | Individuell | Individuell |

### Credit-System

- **Message Credits**: Jeder Prompt an die KI kostet 1 Credit (Entwicklung, Debugging)
- **Integration Credits**: Jede Nutzer-Aktion mit Integration (LLM-Call, File-Upload, E-Mail) kostet 1 Credit (Produktion)

### Kostenentwicklung

Die Kosten skalieren **stufenweise, nicht linear**:
- $0 → $16 → $40 → $80 → $160 — Verdopplung pro Stufe
- Integration Credits steigen nicht proportional mit
- **Keine Pay-as-you-go-Option** — Credits koennen nicht nachgekauft werden
- Bei Limit-Erreichen: Upgrade oder auf naechsten Abrechnungszyklus warten

### Rechenbeispiel Produktion

100 aktive Nutzer × 10 Integrationsaktionen/Tag × 30 Tage = **30.000 Credits/Monat**
→ Mindestens Elite-Plan ($80/Monat) erforderlich

## Technische Eigenschaften

### Was Base44 bietet

- **Datenbank**: Integriert, kein separater Zugang, kein SQL
- **Auth**: Eingebaut (E-Mail/Passwort, Social Login)
- **File Storage**: Integriert, keine separate GB-Abrechnung
- **Hosting**: Automatisch, App ist sofort nach Erstellung live
- **SDK**: `@base44/sdk` (npm) fuer externen API-Zugriff auf Entities

### Was Base44 NICHT bietet

- **Kein DB-Zugang**: Kein SQL, kein direkter Datenbankzugriff
- **Kein Backend-Export**: Nur Frontend-Code ist exportierbar
- **Keine Infrastruktur-Kontrolle**: Kein Zugriff auf Server, Logs, Uptime
- **Kein Self-Hosting**: Apps laufen ausschliesslich auf Base44-Infrastruktur
- **Kein Datenstandort-Wahl**: Keine Garantie fuer Datenstandort (DSGVO-relevant!)

## Vendor Lock-in — Kritische Bewertung

### Das Hauptrisiko

> Selbst bei einem Code-Export erhaelt man **nur das Frontend**. Backend-Logik, Datenbank und Kern-Funktionalitaet leben in Base44's geschlossenem SDK.

- Bei Base44-Ausfaellen sind **alle** gehosteten Apps betroffen
- Keine Kontrolle ueber Verfuegbarkeit der Infrastruktur
- Migration weg von Base44 erfordert komplette Neuentwicklung des Backends

### DSGVO-Bedenken

- Datenstandort unklar (wahrscheinlich US-Cloud)
- Kein Auftragsverarbeitungsvertrag (AVV) fuer Vereine
- Personenbezogene Daten von Vereinsmitgliedern auf fremder Infrastruktur
- Fuer die Helfer-App akzeptabel (wenig personenbezogene Daten), fuer Mitgliederverwaltung **nicht empfohlen**

## Vergleich: Base44 vs. Self-Hosting

| Kriterium | Base44 | Self-Hosting (Hetzner/Proxmox) |
|-----------|--------|-------------------------------|
| **Time-to-Market** | Minuten | Tage bis Wochen |
| **Kosten (Start)** | $0-$16 | ~$5/Monat (Hetzner CX22) |
| **Kosten (100 User)** | $80+/Monat | ~$5/Monat (gleich) |
| **Kosten (1000 User)** | $160+/Monat | ~$10/Monat |
| **Datenkontrolle** | Keine | Volle Kontrolle |
| **DSGVO** | Problematisch | Einfach (Daten in DE) |
| **Vendor Lock-in** | Hoch | Keiner |
| **Wartungsaufwand** | Keiner | Selbst verantwortlich |
| **Skalierung** | Stufenweise (teuer) | Linear (guenstig) |
| **Backend-Zugang** | Nein | Ja (voller Code) |
| **DB-Zugang** | Nein | Ja (PostgreSQL) |
| **Uptime-Kontrolle** | Nein | Ja |

## Empfehlung fuer TSV Wartenberg

### Base44 nutzen fuer:

- **Prototypen und Proof-of-Concepts** — schnell eine Idee validieren
- **Einfache Tools** ohne personenbezogene Daten — z.B. Helferschichten-Planung
- **Temporaere Apps** — Anmeldung fuer ein einzelnes Event
- **Apps mit wenig Integrations-Bedarf** — unter 1.000 Credits/Monat

### Self-Hosting nutzen fuer:

- **Mitgliederverwaltung** (personenbezogene Daten → DSGVO)
- **Benutzerverwaltung / Auth** (sicherheitskritisch)
- **Buchfuehrung / Finanzen** (sensible Daten)
- **Alles was langfristig produktiv laeuft** (Kosteneffizienz)
- **Alles was hohe Verfuegbarkeit braucht** (Kontrolle)

### Aktuelle Entscheidung: HelferHub auf Base44

Die Helfer-App (`helferhub.base44.app`) bleibt vorerst auf Base44 weil:
- Schnell erstellt, bereits funktionsfaehig
- Wenig personenbezogene Daten (nur Name + Kontakt)
- Geringe Nutzerzahl (< 50 Helfer pro Event)
- Integration ueber `@base44/sdk` ins Dashboard moeglich

**Exit-Strategie:** Sollte die App zu teuer werden oder Base44 Probleme machen, kann die Helfer-Funktionalitaet in tsv-hub nachgebaut werden (Schichten + Zuordnungen sind dort bereits implementiert).

## Technische Integration

### SDK-Zugriff

```typescript
import { createClient } from "@base44/sdk"

const base44 = createClient({ appId: "699788636dd9fc3c5e8a3a25" })
await base44.auth.loginViaEmailPassword(email, password)

// Entities lesen
const schichten = await base44.entities.Schichten.list()
const offene = await base44.entities.Schichten.filter({ status: "offen" })
```

### Einschraenkungen der Integration

- **Auth ist getrennt**: Base44 hat eigenes User-System, nicht mit tsv-auth verbunden
- **Kein Webhook-Support**: Keine Echtzeit-Benachrichtigungen bei Datenänderungen
- **Rate Limits**: Credits werden pro API-Call verbraucht
- **Entity-Namen muessen bekannt sein**: Kein Schema-Discovery ohne Login

## Quellen

- [Base44 SDK Dokumentation](https://docs.base44.com/developers/references/sdk/getting-started/overview.md)
- [Base44 Integrations](https://docs.base44.com/Integrations/Using-integrations)
- [Base44 Pricing](https://base44.com/pricing)
