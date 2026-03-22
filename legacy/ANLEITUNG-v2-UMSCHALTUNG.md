# Anleitung: Zwischen altem und neuem Design umschalten

## Konzept

Beide Sitepackages (v1 und v2) laufen als eigenständige TYPO3-Extensions parallel. Die Umschaltung erfolgt über das TypoScript-Template im TYPO3-Backend – ohne Code-Änderungen, ohne Datenverlust.

---

## Schritt 1: v2-Extension installieren

### Variante A: Manuell (ohne Composer)

1. Den Ordner `tsv_wartenberg_bootstrap_v2` nach `typo3conf/ext/` auf dem Server kopieren
2. Im TYPO3-Backend: **Admin Tools > Extensions**
3. Die Extension **"TSV Wartenberg Bootstrap v2 (Modernisiert)"** aktivieren
4. Cache leeren

### Variante B: Mit Composer

```bash
# Im TYPO3-Projektverzeichnis:
# 1. Extension als lokales Paket hinzufuegen (Pfad anpassen)
composer config repositories.tsv-v2 path ./packages/tsv_wartenberg_bootstrap_v2

# 2. Installieren
composer require tsv-wartenberg/tsv-wartenberg-bootstrap-v2:@dev
```

---

## Schritt 2: Zwischen v1 und v2 umschalten

1. Im TYPO3-Backend: **Web > Template**
2. Root-Seite der Website auswählen
3. Dropdown: **"Info/Modify"** wählen
4. Auf **"Edit the whole template record"** klicken
5. Reiter **"Includes"** öffnen

### Auf v2 (neues Design) wechseln:

1. Unter **"Include static (from extensions)"**:
   - **Entfernen:** `tsv-wartenberg-bootstrap`
   - **Hinzufügen:** `TSV Wartenberg v2 (Modernisiert)`
2. **Speichern**
3. **Cache leeren** (oben rechts: Blitz-Symbol > Flush frontend caches)

### Zurück zu v1 (altes Design):

1. Gleicher Weg, aber umgekehrt:
   - **Entfernen:** `TSV Wartenberg v2 (Modernisiert)`
   - **Hinzufügen:** `tsv-wartenberg-bootstrap`
2. **Speichern + Cache leeren**

---

## Wichtige Hinweise

- **Beide Extensions können gleichzeitig installiert sein** – nur eine sollte im TypoScript-Template aktiv eingebunden sein
- **Inhalte bleiben erhalten** – die Content-Elemente sind vom Sitepackage unabhängig, nur das Design ändert sich
- **Frame-Klassen** (z.B. `frame-well`, `frame-hellblau`) funktionieren in beiden Versionen, sehen aber unterschiedlich aus
- **Backend-Layouts** sind identisch – kein Umkonfigurieren nötig
- Nach dem Umschalten immer **alle Caches leeren**

---

## Was ist in v2 anders?

| Bereich | v1 (Alt) | v2 (Modernisiert) |
|---------|----------|-------------------|
| Farbsystem | Chaotisch, doppelte Definitionen | Konsistent, TSV-Vereinsfarben |
| CSS | ~480 Zeilen Legacy-Code (Bootstrap 3) | ~150 Zeilen sauberes Bootstrap 5 |
| Navigation | Gemischt BS3/BS5, Hover-Dropdowns | Reines Bootstrap 5, Click-Dropdowns |
| Frames | Harte Borders (#0C2CBB), bunte Hintergründe | Moderne Cards mit Schatten + Akzent-Borders |
| Footer | Grau, eingerahmt, ohne Social Media | Vereinsgrün, Social Media aktiviert |
| Typografie | Helvetica Neue | System-UI Font-Stack |
| Google Analytics | Veraltetes UA-Tracking | Deaktiviert (GA4-Migration empfohlen) |
