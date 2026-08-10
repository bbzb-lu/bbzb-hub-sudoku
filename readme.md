# BBZB Sudoku

Sudoku-Embed für SharePoint. Statische Seite, gehostet über GitHub Pages —
keine Build-Schritte, kein Backend.

## Einbetten

In SharePoint das **Einbetten**-Webpart verwenden (Embed-Code, *nicht* das URL-Feld):

```html
<iframe
  src="https://bbzb-lu.github.io/bbzb-hub-sudoku/"
  width="480"
  height="350"
  frameborder="0"
  scrolling="no"
  style="border:none;overflow:hidden;">
</iframe>
```

Das Layout ist fluid: Spielfeld links, Bedienung rechts, alles über `min()`/`clamp()`
aus dem Viewport abgeleitet. Geprüft bei **480×350** (Desktop) und **350×250**
(Mobile, wenn SharePoint den Container herunterskaliert) — in beiden Fällen ohne
Scrollbalken und ohne abgeschnittene Inhalte. Unter ~320×230 wird es unbrauchbar klein.

GitHub Pages aktivieren unter: Settings → Pages → Branch `main`, Ordner `/` (root).

## Spielablauf

Der Titelbildschirm zeigt nur Icon, Titel, einen Satz und zwei Wege:
**EINZELSPIEL** und **RENNEN**. Die Schwierigkeit und der Renn-Code liegen
einen Schritt weiter, damit die Landing-Page ruhig bleibt.

**Einzelspiel** — Schwierigkeit wählen, `SPIELEN`. Das Rätsel kommt von der
öffentlichen [dosuku-API](https://sudoku-api.vercel.app/api/dosuku). Ist die
API nicht erreichbar, wird lokal eines erzeugt (`OFFLINE-MODUS`).

**Rennen** — `ERSTELLEN` schreibt einen Code wie `M-7K2QX9` ins Feld und kopiert
ihn in die Zwischenablage; das Spiel startet dabei **nicht**. Alle geben den Code
ein und drücken gemeinsam `START`, damit niemand einen Vorsprung hat. Wer denselben
Code eingibt, bekommt exakt dasselbe Rätsel. Der Code steht während des Spiels in
der Seitenleiste (Klick kopiert ihn) und wird am Ende zusammen mit der Zeit
eingeblendet.

**Fehler zeigen** — optionaler Schalter; ist er an, werden falsch gesetzte
Zahlen rot markiert. Standardmässig aus.

Eingabe per Zahlenfeld oder Tastatur (`1`–`9`, `Backspace`, Pfeiltasten).

## Aufbau

| Datei        | Inhalt                                                   |
|--------------|----------------------------------------------------------|
| `index.html` | Markup: Titelbildschirm, Spielbildschirm, Gewinn-Overlay  |
| `style.css`  | Design-Tokens, `.ts-*` Titelbildschirm, Spiel-Styles       |
| `script.js`  | Generator, Solver, Share-Codes, Spiellogik                |
| `CLAUDE.md`  | Design-Vorgaben für alle BBZB-Embeds                      |

Der Titelbildschirm nutzt den gemeinsamen `.ts-*`-Baukasten, der in allen
BBZB-Embeds gleich aussieht: Pixel-Icon mittig oben, Titel, ein Satz, dann ein
oder zwei CTAs. Was eine Auswahl braucht, liegt auf Schritt 2
(`.ts-inner--step`). Skaliert über eine einzige Einheit (`--ts-u`, aus der
Containergrösse) und kommt ohne Media Queries aus.
Vorlage und Anleitung: `../title-screen-draft/`.

Ein Rätsel-Code ist ein Seed: daraus werden Lösung und Löcher deterministisch
erzeugt, mit geprüft eindeutiger Lösung. Deshalb ergibt derselbe Code überall
dasselbe Feld. Richtwerte für die Anzahl vorgegebener Zahlen: leicht 40,
mittel 34, schwer 24 — angelehnt an die Bänder der API.

## Lokal testen

```bash
npx serve .
# → http://localhost:3000
```

Über `file://` blockieren Browser die lokalen RawPixel-Schriften.
