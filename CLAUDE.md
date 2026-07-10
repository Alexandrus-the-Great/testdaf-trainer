# TestDaF-Trainer für Kirill — Projektkontext

Lern-System für die TestDaF-Vorbereitung (Ziel: TDN 5 / C1+, Prüfung Januar 2027).
Live: https://alexandrus-the-great.github.io/testdaf-trainer/

## Architektur

- **`heute.html`** — die QUELLE der Tages-App (Vanilla JS, eine Datei, keine Dependencies).
  Enthält: Studienplan-Logik (`tag()`), Spaced Repetition (`wiederholungen()`, 1/3/7 Wochen),
  Tempo-Tracking (`tempo()`: Rückstand, Aufholmodus, Prüfungsziel), Übungsblock (`UEB`, `uebungsblock()`),
  Material-Spickzettel (`MAT`, `KERNSAETZE`). Fortschritt in `localStorage` (`tdk-<datum>`, `tdk-start`, `tdk-ende`).
- **`01…24-*.md`** — die Lernmaterialien (Deutsch). `README.md` erklärt jede Datei.
- **`grafiken/`** — 19 Übungsdiagramme (SVG) + `make_grafiken.py` (matplotlib, **SVG-Backend nutzen** —
  das Agg/PNG-Backend ist auf diesem Rechner durch App Control blockiert).
- **`tools/build_komplett.py`** — baut `TestDaF-Kirill-Komplett.html` aus allen md-Dateien
  (Anker `#datei-XX` pro Datei; danach mit LibreOffice headless nach docx/pdf konvertieren).
- **`tools/build_site.py`** — baut den Ordner `site/` (= das Git-Repo, GitHub Pages):
  injiziert die SVGs als Data-URIs in `heute.html` → `site/index.html` (Marker: `const G = {};`),
  kopiert Komplettpaket, PDF und beide Anki-CSVs, setzt `noindex`.

## Workflow für Änderungen

1. `heute.html` bzw. md-Dateien bearbeiten (NICHT `site/index.html` — wird überschrieben!)
2. Bei md-Änderungen: `python tools/build_komplett.py` + LibreOffice-Konvertierung
3. `python tools/build_site.py`
4. `cd site && git add -A && git commit && git push` → Pages deployt automatisch (~1 Min.)

Die Skripte enthalten absolute Windows-Pfade (`C:\Users\alexa\…`) — beim Klonen auf einen anderen
Rechner zuerst die `ROOT`-Konstanten anpassen.

## Stolperfallen (alle schon einmal passiert)

- **Deutsche Anführungszeichen in JS:** in `"…"`-Strings niemals `„Wort"` mit ASCII-Schlusszeichen —
  das beendet den String. In doppelten Anführungszeichen `„…“` (U+201E/U+201C) verwenden; in
  Template-Literals (Backticks) ist ASCII `"` okay. Nach JEDER Änderung: `node --check` gegen das
  extrahierte `<script>`.
- **Datums-Logik:** `iso()` ist bewusst lokal (kein `toISOString()` — UTC-Verschiebung!),
  `plusTage()` nutzt `setDate()` (DST-sicher). Nicht „vereinfachen".
- **Plan-Logik testen:** Logikteil des Scripts bis `// ── Rendering` abschneiden, `localStorage` stubben,
  mit Node Szenarien durchrechnen (Beispiele: frühere Commits/Session).

## Kontext

Der Plan: 6 Tage/Woche × 90 Min., Start als „Woche 0" per Checkbox in der App, 4 Phasen bis C1-Stand
(Sa der Woche 22), Prüfung ~Woche 26. Alle Übungsinhalte basieren bewusst auf den eigenen 54 Themen-Texten.
Lesen/Hören wird NICHT mit erfundenen Items trainiert — nur echte Modelltests (testdaf.de) + Taktik (Datei 24).
