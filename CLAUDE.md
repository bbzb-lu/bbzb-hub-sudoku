# BBZB SharePoint Embed — Architektur-Template

Dieses Repo ist die Vorlage für alle BBZB SharePoint Embeds. Jedes Embed ist ein statisches GitHub-Pages-Projekt mit identischer Dateistruktur und Optik.

Die Embeds sollen visuell stimmig mit dem **physischen BBZB Schulplaner 26/27** sein. Alle Design-Entscheidungen orientieren sich an diesem Dokument.

---

## Dateistruktur

```
mein-embed/
├── index.html          ← Markup (nur Struktur, keine Styles/Scripts inline)
├── style.css           ← Alle Styles (Design-Tokens als CSS-Variablen)
├── script.js           ← Alle Logik
├── CLAUDE.md           ← Diese Datei
├── font/
│   ├── RawPixel.otf
│   ├── RawPixel-Bold.otf
│   ├── RawPixel-Italic.otf
│   └── RawPixel-BoldItalic.otf
└── icons/              ← Pixel-Art PNGs (aus PSD via sips konvertiert)
    ├── tomate.png
    ├── hourglass_icon.png
    └── ...
```

Der `font/`- und `icons/`-Ordner werden 1:1 aus diesem Repo kopiert.

---

## Design-Tokens

Alle Farben sind als CSS-Variablen in `style.css` definiert und müssen in jedem Embed identisch sein:

```css
:root {
  /* Primärpalette */
  --blue:        #4A6FA5;   /* Embed-Blau – Titelbars, Borders, Labels      */
  --blue-vivid:  #3B4FC8;   /* Schulplaner-Blau – für dunkle Vollhintergründe */
  --blue-light:  #C8D6E8;   /* Inaktive Progress-Dots, dezente Flächen      */
  --pink:        #E84393;   /* Akzent – Buttons, aktive Dots, Highlights     */
  --yellow:      #FFD000;   /* Aufmerksamkeit – Popup-Chrome, Warnungen      */
  --dark:        #1A1A2E;   /* Text, Schatten, Outlines                      */

  /* Hintergründe */
  --card-bg:     #FFFFFF;   /* Standard Karteninhalt                         */
  --cream:       #FFF9E6;   /* Warme Alternative zu Weiss (Popup-Inhalte)    */

  /* Typografie */
  --font:        'RawPixel', monospace;
}
```

### Farbverwendung aus dem Schulplaner

| Farbe         | Hex       | Wann verwenden                                                      |
|---------------|-----------|---------------------------------------------------------------------|
| Embed-Blau    | `#4A6FA5` | Titlebar, Borders, Labels – der Standard für alle Embeds            |
| Schulplaner-Blau | `#3B4FC8` | Nur wenn ein Embed einen **dunklen Vollhintergrund** braucht      |
| Pink          | `#E84393` | Primäre CTA-Buttons, aktive States, Dots, Feiertage im Kalender     |
| Gelb          | `#FFD000` | Popup-/Alert-Fenster-Chrome, Schulferien, Warnhinweise, Akzentpfade |
| Dunkel        | `#1A1A2E` | Pixel-Schatten (immer `4–6px solid`), Body-Text                     |
| Cream         | `#FFF9E6` | Warme Inhalts­flächen statt reinem Weiss (z. B. Popup-Inhalt)       |

---

## Schulplaner Designsprache

Diese Regeln sind aus dem physischen Schulplaner 26/27 abgeleitet und gelten für alle digitalen Embeds.

### Pixel-Art Grundprinzipien
- Alle Elemente sind **pixelscharf** – kein `border-radius`, kein `box-shadow` mit Blur
- Schatten sind **harte Offset-Schatten**: `4–6px solid var(--dark)` (kein `blur`)
- `image-rendering: pixelated` auf allen Icon-Bildern
- Buttons "drücken sich ein": Hover verschiebt um `+2px`, Active um `+4px`, Schatten verkleinert sich entsprechend

### 3D-Raised-Panel Shading (Kernregel)
Alle Karten und Buttons nutzen **asymmetrische Borders** für den Raised-Effekt aus dem Schulplaner:

```
Lichtquelle: oben links
Top  + Left  → --blue  (helle Seite)
Right + Bottom → --pink  (Schatten-Seite)
```

```css
/* Karte */
border-top:    4px solid var(--blue);
border-left:   4px solid var(--blue);
border-right:  4px solid var(--pink);
border-bottom: 4px solid var(--pink);
box-shadow: 5px 5px 0 var(--dark);

/* Button */
border-top:   3px solid var(--dark);
border-left:  3px solid var(--dark);
border-right: 3px solid var(--pink);
border-bottom: 3px solid var(--pink);
```

**Gelbe Popup-Variante** (Image #4 aus dem Schulplaner): Titlebar gelb, Schatten-Seite kippt zu blau:
```css
border-top:    4px solid var(--yellow);
border-left:   4px solid var(--yellow);
border-right:  4px solid var(--blue);
border-bottom: 4px solid var(--blue);
```
Verwende CSS-Klasse `.card--popup` — bereits in `style.css` definiert.

### Fenster-Farbvarianten

Der Schulplaner definiert **drei Fenster-Varianten** je nach Kontext. Alle teilen dieselbe Grundstruktur, unterscheiden sich in Titelleisten-Farbe und Border-Behandlung.

---

**Variante A — Blau** `.card` (Standard-Embed)
```
Titlebar:  --blue   (weisser Text, 3 pinke Dots)
Border:    asymmetrisch – top/left --blue, right/bottom --pink
Body:      --cream + Graph-Paper-Grid
Einsatz:   Standard für alle Embeds
```
```css
border-top: 4px solid var(--blue);  border-left: 4px solid var(--blue);
border-right: 4px solid var(--pink); border-bottom: 4px solid var(--pink);
box-shadow: 5px 5px 0 var(--dark);
```

---

**Variante B — Pink** `.card--pink`
```
Titlebar:  --pink   (weisser Text, 3 blaue Dots)
Border:    uniform --blue (alle Seiten gleich)
Body:      --cream
Einsatz:   Notifications, Feiertage, persönliche Hinweise
```
```css
border: 4px solid var(--blue);
box-shadow: 5px 5px 0 var(--dark);
/* Titlebar */
.card--pink .titlebar { background: var(--pink); }
.card--pink .dot      { background: var(--blue); }
```

---

**Variante C — Gelb** `.card--yellow`
```
Titlebar:  --yellow  (dunkler Text --dark, Pixel-Close-Button rechts)
Border:    uniform --blue, etwas dicker (5px)
Body:      --cream
Scrollbar: Track --cream, Thumb --pink, Arrows --dark
Einsatz:   Info-Popups, QR-Fenster, Warnungen, Browser-Widget
```
```css
border: 5px solid var(--blue);
box-shadow: 5px 5px 0 var(--dark);
/* Titlebar */
.card--yellow .titlebar      { background: var(--yellow); }
.card--yellow .titlebar-text { color: var(--dark); }
.card--yellow .dot           { background: var(--blue); border-color: var(--dark); }
```

---

**Scrollbar-Pattern** (aus Variante C / `webbrowser2_icon`):
```
Track:   --cream, umrandet mit --blue (4px)
Thumb:   --pink, umrandet mit --blue (3px)
Pfeile:  Pixel-Icons in --dark
```
```css
::-webkit-scrollbar       { width: 16px; background: var(--cream); border-left: 4px solid var(--blue); }
::-webkit-scrollbar-thumb { background: var(--pink); border: 3px solid var(--blue); }
```

---

**Übersichtstabelle:**

| Modifier       | Titlebar   | Titlebar-Text | Border-Style | Body     | Einsatz              |
|----------------|------------|---------------|--------------|----------|----------------------|
| `.card`        | `--blue`   | weiss         | asymmetrisch | `--cream`| Standard-Embed       |
| `.card--pink`  | `--pink`   | weiss         | uniform blau | `--cream`| Alerts, Feiertage    |
| `.card--yellow`| `--yellow` | `--dark`      | uniform blau | `--cream`| Info, QR, Browser    |

### Hintergrundmuster
Zwei Muster werden aus dem Schulplaner übernommen:

**Punkt-Raster** (auf dunklem Hintergrund, z. B. `--blue-vivid`):
```css
background-image: radial-gradient(circle, rgba(255,255,255,0.15) 1px, transparent 1px);
background-size: 18px 18px;
```

**Karopapier-Raster** (auf hellem Hintergrund, z. B. `--card-bg`):
```css
background-image:
  linear-gradient(var(--blue-light) 1px, transparent 1px),
  linear-gradient(90deg, var(--blue-light) 1px, transparent 1px);
background-size: 20px 20px;
```

### Typografie

| Anwendung           | Gewicht    | Grösse      | Stil                        |
|---------------------|------------|-------------|-----------------------------|
| Seitentitel         | Bold 700   | 1.4–2rem    | Uppercase, letter-spacing   |
| Titlebar-Text       | Bold 700   | 0.85–1rem   | Uppercase, letter-spacing 2 |
| Labels / Phase      | Bold 700   | 0.75–0.9rem | Uppercase, letter-spacing 3 |
| Body / Beschreibung | Regular 400| 0.8–0.9rem  | Normal case                 |
| Timer-Display       | Bold 700   | 2.4–3.4rem  | Nur Ziffern, letter-spacing |

Alle Texte: `font-family: var(--font)` — **RawPixel**, nicht Google Fonts.

### Pomodoro-spezifische Identität (Schulplaner S. 10)
Der Schulplaner zeigt auf Seite 10 die Pomodoro-Methode mit dem BBZB-Pixel-Computer und der Floppy-Disk. Das digitale Pomodoro-Embed soll diese Verbindung erkennbar machen:
- `icons/pomodoro_floppy-disk.png` passt als Icon für das Pomodoro-Embed
- Tomaten-Icon (`icons/tomate.png`) ist die primäre visuelle Identität der Methode
- Gelb (`#FFD000`) ist der Akzent der Caution/Warnung im Schulplaner-Kontext

---

## Karten-Komponente

Der Body ist **immer transparent** – der Hintergrund wird von SharePoint gesteuert.
Die `.card` füllt den gesamten iframe (`width: 100%; height: 100%`).

Grundstruktur jedes Embeds:

```html
<div class="card">

  <!-- Titlebar: immer gleich aufgebaut -->
  <div class="titlebar">
    <div class="dots">
      <div class="dot"></div>
      <div class="dot"></div>
      <div class="dot"></div>
    </div>
    <span class="titlebar-text">TITEL DES EMBEDS</span>
    <img src="icons/passendes-icon.png" class="titlebar-icon" alt="">
  </div>

  <!-- Inhaltsbereich: hier kommt der embed-spezifische Content rein -->
  <div class="card-body">
    <!-- ... -->
  </div>

</div>
```

### CSS-Klassen die immer verfügbar sind

| Klasse           | Verwendung                                          |
|------------------|-----------------------------------------------------|
| `.card`          | Haupt-Container, füllt iframe                       |
| `.titlebar`      | Blaue Titelleiste mit Dots                          |
| `.dots` / `.dot` | Die drei pinken Pixel-Quadrate oben links           |
| `.titlebar-text` | Titel-Text in der Leiste                            |
| `.titlebar-icon` | 22×22px Icon rechts in der Leiste                   |
| `.card-body`     | Flex-Container für den Inhalt (zentriert)           |
| `.btn-ctrl`      | Blauer Pixel-Button mit Hover → Pink                |
| `.hero-icon`     | 60×60px Icon als Haupt-Visual im Inhalt             |

---

## Buttons

```html
<!-- Primär-Aktion (pink) -->
<button class="start-btn">START</button>

<!-- Steuer-Button (blau, Hover → pink) -->
<button class="btn-ctrl">II</button>
```

### Button-Style (einheitlich für alle Buttons)

Alle Buttons — egal ob Primär (START) oder Steuerung (II, >>) — nutzen **dasselbe Aufbauprinzip**:

```
border top/left  → --dark   (Lichtseite)
border right/bottom → Button-Farbe (Schattenrahmen, dezent)
box-shadow       → harter Offset-Schatten (--dark)
inset bottom     → dunkleres Tiefenband (Tiefe/3D-Wirkung)
```

```css
/* Primär-Button (gross, pink) */
border-top:    4px solid var(--dark);
border-left:   4px solid var(--dark);
border-right:  4px solid var(--pink);   /* gleiche Farbe wie BG */
border-bottom: 4px solid var(--pink);
box-shadow:
  5px 5px 0 var(--dark),
  inset 0 -6px 0 rgba(0, 0, 0, 0.28);

/* Control-Button (klein, blau) */
border-top:    4px solid var(--dark);
border-left:   4px solid var(--dark);
border-right:  4px solid var(--pink);   /* Akzentfarbe als Schatten */
border-bottom: 4px solid var(--pink);
box-shadow:
  4px 4px 0 var(--dark),
  inset 0 -5px 0 rgba(0, 0, 0, 0.25);
```

Die Schattenrahmen-Farbe (right/bottom border) entspricht der Akzentfarbe — bei pink-Buttons `--pink`, bei blauen Buttons `--pink` (Kontrast). Für gelbe Buttons: `--blue`.

**Hover:** Farbe wechselt + `translate(2px, 2px)` + Schatten um 2px kleiner.  
**Active:** `translate(4–5px, 4–5px)` + Schatten `0 0 0`.

---

## Icons einbinden

Icons liegen in `icons/` als PNG mit Transparenz. `image-rendering: pixelated` ist global gesetzt.

Empfohlene Grössen:
- Titlebar-Icon: `22×22px`
- Hero-Icon (Hauptbild): `60×60px`
- Kleine Inline-Icons: `24–32px`

Relevante Icons:

| Datei                       | Verwendung                                   |
|-----------------------------|----------------------------------------------|
| `tomate.png`                | Pomodoro / Arbeit                            |
| `tomate falling.png`        | Animation / fallende Tomate                  |
| `hourglass_icon.png`        | Timer / Pause / Warten                       |
| `pomodoro_floppy-disk.png`  | Schulplaner-Verbindung / Pomodoro-Identität  |
| `notification_icon.png`     | Hinweise / Alerts (Sprechblase)              |
| `caution_icon.png`          | Warnungen (gelber Kontext)                   |
| `glühbirne_icon.png`        | Tipps & Tricks                               |
| `music_note_icon.png`       | Audio / Musik                                |
| `mail_icon.png`             | Kontakt / Nachrichten                        |
| `file_icon.png`             | Dokumente / Downloads / Formulare            |
| `computer_icon.png`         | IT / Desktop                                 |
| `location_icon.png`         | Standort / Karte                             |
| `sharepoint_icon.png`       | SharePoint-Verweise                          |
| `schulnetz_icon.png`        | schulNetz-Verweise                           |
| `instagram_icon.png`        | Social Media                                 |
| `youtube_icon.png`          | Social Media / Video                         |

---

## Neues Embed erstellen

1. Dieses Repo forken / als Template verwenden
2. `index.html` anpassen (Titel, passendes Icon wählen)
3. `script.js` mit der Embed-Logik füllen
4. `style.css`: Design-Tokens **nicht ändern** – nur neue Klassen hinzufügen
5. GitHub Pages aktivieren: Settings → Pages → Branch `main`, Root `/`
6. In SharePoint einbetten (Embed-Code, **nicht** URL-Feld):

```html
<iframe
  src="https://alexejwaser.github.io/REPO-NAME/"
  width="480"
  height="340"
  frameborder="0"
  scrolling="no"
  style="border:none;overflow:hidden;">
</iframe>
```

Breite und Höhe an den SharePoint-Spaltenbereich anpassen. Typische Grössen:
- Kompaktes Widget: `480 × 300`
- Standard-Spalte: `480 × 400`
- Vollbreite: `width="100%"`, Höhe fix

---

## Icons aus PSD konvertieren (macOS)

Neue PSD-Assets in `icons backup/` ablegen, dann:

```bash
cd "icons backup"
for f in *.psd; do
  sips -s format png "$f" --out "../icons/${f%.psd}.png" > /dev/null
done
```

---

## Font lokal testen

Bei `file://`-URLs kann der Browser lokale Fonts blockieren. Für lokale Tests:
```bash
npx serve .
# → http://localhost:3000
```
