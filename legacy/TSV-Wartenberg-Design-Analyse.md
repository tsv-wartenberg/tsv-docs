# Design-Analyse & Optimierungsvorschläge: tsv-wartenberg.de

## Zusammenfassung der aktuellen Situation

Das Sitepackage basiert auf **TYPO3 mit bootstrap_package v12** (Bootstrap 5). Grundsätzlich ist das eine solide Basis – allerdings gibt es erhebliches Modernisierungspotenzial. Die custom.css enthält ca. 480 Zeilen größtenteils veralteten Code aus Bootstrap-3-Zeiten, das Farbsystem ist inkonsistent, und wichtige moderne Web-Standards fehlen.

---

## 1. Farbsystem bereinigen (Priorität: hoch)

**Problem:** Die Farben sind chaotisch definiert. In `_variables.scss` wird `$primary` erst als `#2a9d8f` (Teal) gesetzt, dann als `#014876` (Dunkelblau) überschrieben. In `constants.typoscript` stehen nochmal andere Werte (`#eb3e4a` Rot, `#013859` Blau). In der `custom.css` tauchen weitere Farben auf: TSV-Grün (`#009120`, `#006813`), Gelb (`#FFFF00`), diverse Grautöne. Es gibt kein konsistentes Farbkonzept.

**Empfehlung:** Ein klares Farbschema auf Basis der TSV-Vereinsfarben definieren – vermutlich Grün als Primärfarbe und ein passender Sekundärton. Alle Farbdefinitionen sollten an genau einer Stelle stehen (`_variables.scss`) und über CSS Custom Properties (`--tsv-primary` etc.) im gesamten Projekt nutzbar sein. Die doppelte Definition von `$primary` und die Farbwerte in `constants.typoscript` müssen synchronisiert werden.

**Konkreter Vorschlag für eine konsistente Palette:**

| Rolle | Farbe | Hex |
|-------|-------|-----|
| Primary (TSV-Grün) | Vereinsgrün | `#009120` |
| Primary Dark | Dunkelgrün für Hover | `#006813` |
| Secondary | Neutrales Dunkelblau | `#014876` |
| Accent | Warmes Grün/Gold | `#72bf44` |
| Hintergrund | Sehr helles Grau | `#f8f9fa` |
| Text | Dunkelgrau | `#212529` |

---

## 2. Legacy-CSS komplett entfernen (Priorität: hoch)

**Problem:** Die `custom.css` enthält massenhaft Bootstrap-3-Code, der mit Bootstrap 5 nicht mehr funktioniert. Beispiele:

- `.navbar-default` Klassen (Bootstrap 3) – in Bootstrap 5 heißt das `.navbar-light` bzw. `.navbar-dark`
- `.label-*` Klassen (Bootstrap 3) – ersetzt durch `.badge` in Bootstrap 5
- SmartMenus-CSS (`.sm-collapsible`, `.caret`, scroll arrows) – über 100 Zeilen für ein Plugin, das vermutlich nicht mehr nötig ist
- Veraltete Vendor-Prefixes (`-webkit-box-shadow`, `-webkit-border-radius`, `-moz-border-radius`)
- `.dropdown:hover .dropdown-menu { display: block }` – bricht die zugängliche Tastaturnavigation

**Empfehlung:** Die gesamte `custom.css` sollte von Grund auf neu geschrieben werden – schlank, nur die tatsächlich benötigten Anpassungen. Der SmartMenus-Code kann komplett entfernt werden, da das bootstrap_package bereits eine funktionierende Mega-Menu-Lösung mitbringt. Das spart allein ca. 200 Zeilen unnötigen Code.

---

## 3. Frame-Klassen modernisieren (Priorität: mittel)

**Problem:** Die benutzerdefinierten Frame-Klassen (`.frame-well`, `.frame-hellblau`, `.frame-swRahmen`, `.frame-jumbotron`, `.frame-indent`) nutzen veraltete Styling-Techniken und wirken visuell nicht zeitgemäß: harte 1px-Borders in Dunkelblau (`#0C2CBB`), bunte Hintergründe (blass-gelb `#f0f0c0`, hellblau `#DBFFFF`, hellbraun `#FDEED2`, blass-grün `#C9FEC2`), kleine Border-Radii und inset box-shadows.

**Empfehlung:** Diese Frames sollten als moderne "Content Cards" umgestaltet werden: subtile Schatten statt harter Rahmen, einheitliche Farbgebung passend zur Vereinspalette, größere border-radius-Werte (8–12px statt 3–4px), und CSS Custom Properties für flexible Anpassungen. Ein System aus 3–4 Varianten (z.B. Standard, Highlight, Info, Warnung) genügt statt der aktuellen 8+ Klassen.

---

## 4. Typografie verbessern (Priorität: mittel)

**Problem:** Aktuell wird `"Helvetica Neue", Helvetica, Arial, sans-serif` verwendet – ein generischer System-Font-Stack ohne eigenen Charakter. Die Header-Texte nutzen einfache `em`-Größen ohne klare typografische Hierarchie.

**Empfehlung:** Entweder einen modernen System-Font-Stack nutzen, der auf allen Betriebssystemen gut aussieht:

```css
font-family: system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
```

Oder eine bewusst gewählte Google Font (z.B. "Inter" oder "Source Sans Pro") für einen professionelleren Auftritt. Dazu eine klare Type Scale definieren mit konsistenten Größenabstufungen für h1–h4 und Fließtext.

---

## 5. Navigation modernisieren (Priorität: hoch)

**Problem:** Die Navigation im Partial `Main.html` unterstützt zwar 3 Ebenen, nutzt aber gemischte `data-toggle`/`data-bs-toggle` Attribute (Bootstrap 3 und 5 gleichzeitig). Die `.dropdown-hover`-Klasse zusammen mit dem CSS `display: block` bei Hover bricht die Bedienbarkeit auf Touch-Geräten und per Tastatur.

**Empfehlung:** Die Navigation sollte nur Bootstrap-5-Attribute verwenden und das Hover-Dropdown-Verhalten entfernt werden. Stattdessen kann ein "Mega Menu"-Ansatz umgesetzt werden, bei dem Dropdowns per Klick geöffnet werden. Für die mobile Ansicht sollte eine "Off-Canvas"-Navigation in Betracht gezogen werden – das bootstrap_package unterstützt dies bereits.

---

## 6. Hero-Bereich / Startseite aufwerten (Priorität: mittel)

**Problem:** Das Template `Example.html` zeigt nur eine einfache Content-Spalte. Es gibt keinen dedizierten Hero-Bereich, keine visuell ansprechende Einstiegsseite mit Call-to-Action.

**Empfehlung:** Ein zusätzliches Backend-Layout für die Startseite erstellen mit einem Hero-Bereich (großes Bild oder Slider mit Vereinsmotto), Quick-Links zu den wichtigsten Abteilungen, einem News-/Terminbereich und einem Bereich für aktuelle Highlights. Das bootstrap_package bietet dafür bereits Content-Elemente wie die "Card Group" oder den "Text with Media"-Typ.

---

## 7. Footer neu gestalten (Priorität: niedrig–mittel)

**Problem:** Der Footer hat in der `custom.css` einen border (`1px solid #0C2CBB`) und hellgrauen Hintergrund – das wirkt wie ein eingerahmter Kasten statt wie ein moderner Website-Footer. Die Konstanten deaktivieren Social Media und Copyright-Bereich.

**Empfehlung:** Den Footer als vollwertige Informationszone gestalten: Logo, Kontaktdaten, Öffnungszeiten/Trainingszeiten, Social-Media-Links, und Quicklinks. Das bootstrap_package unterstützt einen mehrteiligen Footer (content + meta Section). Die Social-Media-Links sollten aktiviert werden (`socialmedia.enable = 1`), und der Copyright-Text sollte nicht mit `&nbsp;`-Abständen formatiert werden, sondern mit proper CSS-Layout.

---

## 8. Build-Tooling aktualisieren (Priorität: niedrig)

**Problem:** Das Projekt nutzt Grunt als Build-Tool – das ist funktional, aber seit Jahren nicht mehr der Standard. Die `package.json` im Build-Ordner enthält nur Grunt-Plugins für Uglify, Watch und Imagemin.

**Empfehlung:** Eine Migration auf ein moderneres Build-Tool (z.B. npm scripts mit PostCSS oder esbuild) wäre langfristig sinnvoll, ist aber kein dringendes Problem. Wichtiger ist, dass der SCSS-Build korrekt funktioniert und das bootstrap_package seine eigene Asset-Pipeline mitbringt.

---

## 9. Performance & SEO (Priorität: mittel)

**Problem:** Das Google-Analytics-Tracking nutzt noch die alte Universal-Analytics-ID (`UA-44947660-1`), die seit Juli 2024 keine Daten mehr sammelt. Außerdem fehlen moderne Performance-Optimierungen.

**Empfehlung:**

- Google Analytics auf GA4 umstellen (oder komplett entfernen, falls nicht mehr gewünscht)
- Cookie-Consent-Banner prüfen – `expiryDays = 20` ist konfiguriert, aber die DSGVO-Konformität sollte überprüft werden
- Lazy Loading für Bilder sicherstellen (Bootstrap-Package unterstützt dies)
- Font-Loading optimieren (falls Google Fonts eingesetzt werden: preconnect + font-display: swap)

---

## 10. Mobile-First überarbeiten (Priorität: mittel)

**Problem:** Die responsive Media-Queries in der `custom.css` nutzen `max-width` mit `em`-Werten – das ist nicht falsch, aber inkonsistent mit dem Bootstrap-5-Breakpoint-System, das `min-width` in `px` nutzt. Das führt zu Konflikten.

**Empfehlung:** Alle benutzerdefinierten Media-Queries auf die Bootstrap-5-Breakpoints umstellen und das Mobile-First-Prinzip konsequent anwenden. Die Header-Textgrößen sollten über CSS `clamp()` statt über vier separate Media-Queries gesteuert werden:

```css
.HeaderText h1 {
  font-size: clamp(2rem, 5vw, 5rem);
}
```

---

## Priorisierte Umsetzungsreihenfolge

1. **Farbsystem bereinigen** – Fundament für alles Weitere
2. **Legacy-CSS entfernen** – reduziert Codebasis massiv und beseitigt Konflikte
3. **Navigation modernisieren** – verbessert Usability sofort
4. **Frame-Klassen neu gestalten** – modernster visueller Effekt
5. **Typografie verbessern** – schneller Gewinn für die Gesamtwirkung
6. **Hero-Bereich erstellen** – macht die Startseite einladender
7. **Mobile-First & Performance** – technische Qualität
8. **Footer neu gestalten** – Abrundung des Gesamtbilds
9. **GA4 & SEO** – Tracking wiederherstellen
10. **Build-Tooling** – langfristige Wartbarkeit
