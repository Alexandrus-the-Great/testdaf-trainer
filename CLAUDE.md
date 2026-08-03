# TestDaF-Trainer für Kirill — Projektkontext

Lern-System für die TestDaF-Vorbereitung (Ziel: TDN 5 / C1+, Prüfung Januar 2027).
Live: https://alexandrus-the-great.github.io/testdaf-trainer/

## Architektur

- **`heute.html`** — die QUELLE der Tages-App (Vanilla JS, eine Datei, keine Dependencies).
  Enthält: Studienplan-Logik (`tag()`), Spaced Repetition (`wiederholungen()`, 1/3/7 Wochen),
  Tempo-Tracking (`tempo()`: Rückstand, Aufholmodus, Prüfungsziel), Übungsblock (`UEB`, `uebungsblock()`),
  Lesetraining (`LESEN`, `lesetext()` — freitags, Datei 25 parallel pflegen!), Reproduktions-Trainer
  (`REPRO`/`KERN_STICHWORT`, `reprotrainer()` — samstags: Sätze hinter Stichworten, nur in der App),
  Lückentexte (`LUECKEN`, `lueckentext()` — montags, `{Wort}` = Lücke; Abschnitt in Datei 22 parallel pflegen!),
  Redemittel-Beispielsätze mit Lücken (`BSP` + `lz()` — werden bei Skript-Start an `MAT.*` angehängt),
  Material-Spickzettel (`MAT`, `KERNSAETZE`), **Korrektur-Tab** (`KORR_TYPEN`/`KORR_ZITAT`/`korrPrompt()`,
  `renderKorr()` — baut den Prüfer-Prompt; Entwurf unter `tdk-korr`).
  Fortschritt in `localStorage` (`tdk-<datum>`, `tdk-start`, `tdk-ende`, `tdk-ueb-<datum>`, `tdk-korr`).
- **`skills/testdaf-korrektur/`** — Projekt-Skill: korrigiert einen Übungstext nach Datei 26.
  Bewusst NICHT global (`~/.claude/skills/`) — sie trägt eine Methode und gehört diesem Projekt.
- **`hausaufgaben/<datum>/`** — Kirills Texte, Aufgaben-Screenshots, `korrektur.md`. **Bleibt lokal**:
  enthält die urheberrechtlich geschützten g.a.s.t.-Beispielaufgaben. `build_site.py` kopiert eine
  feste Liste und fasst diesen Ordner nicht an — so lassen.
- **`01…26-*.md`** — die Lernmaterialien (Deutsch). `README.md` erklärt jede Datei.
  **Datei 26 ist die Quelle der Wahrheit für jede KI-Korrektur** — App-Tab und Skill bauen ihren
  Prompt daraus. Wird sie geändert, `KORR_*` in `heute.html` mitziehen (`audit.js` prüft das).
  Datei 25 wurde aus `LESEN` generiert (Generator: Session-Scratchpad `gen25.js`-Muster) — bei
  Änderungen an den Lesetexten beide Stellen synchron halten.
- **`grafiken/`** — 19 Übungsdiagramme (SVG) + `make_grafiken.py` (matplotlib, **SVG-Backend nutzen** —
  das Agg/PNG-Backend ist auf diesem Rechner durch App Control blockiert).
- **`tools/build_komplett.py`** — baut `TestDaF-Kirill-Komplett.html` aus allen md-Dateien
  (Anker `#datei-XX` pro Datei; danach mit LibreOffice headless nach docx/pdf konvertieren).
- **`tools/build_site.py`** — baut den Ordner `site/` (= das Git-Repo, GitHub Pages):
  injiziert die SVGs als Data-URIs in `heute.html` → `site/index.html` (Marker: `const G = {};`),
  kopiert Komplettpaket, PDF und beide Anki-CSVs, setzt `noindex`.

## Workflow für Änderungen

1. `heute.html` bzw. md-Dateien bearbeiten (NICHT `site/index.html` — wird überschrieben!)
2. **Qualitätstor:** `node tools/audit.js` (0 Fehler!) + `node --check` gegen das extrahierte `<script>`
3. Bei md-Änderungen: `python tools/build_komplett.py` + LibreOffice-Konvertierung
   (der HTML-Build klappt alle „**Lösung:**"-Stellen zu — Klick deckt auf; docx/pdf zeigen alles)
4. `python tools/build_site.py`
5. `cd site && git add -A && git commit && git push` → Pages deployt automatisch (~1 Min.)

Die Skripte enthalten absolute Windows-Pfade (`C:\Users\alexa\…`) — beim Klonen auf einen anderen
Rechner zuerst die `ROOT`-Konstanten anpassen.

## Stolperfallen (alle schon einmal passiert)

- **Prüfungsformat NIE aus dem Gedächtnis schreiben.** Am 31.07.2026 stand an vier Stellen im
  Repo, die digitale Zusammenfassung sei „Aufgabe 1" mit 25 Minuten. Richtig ist: Aufgabentyp 1 =
  argumentativer Text (mind. 200 W.), Aufgabentyp 2 = Zusammenfassung aus Lesetext **und Grafik**
  (100–150 W.), **je 30 Minuten**. Quelle ist die Demo-Version in `hausaufgaben/`, nicht die
  Erinnerung. Ein falsches Format trainiert monatelang die falsche Aufgabe.
- **Es gibt keine amtlichen Banddeskriptoren fürs Schreiben.** Das Institut veröffentlicht
  Bewertungs*fragen* und die 0–20-Skala, mehr nicht. Jede TDN-Zahl in einer Korrektur ist eine
  Schätzung und muss so beschriftet sein — sonst klingt Erfundenes amtlich.

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
Hören wird NICHT mit erfundenen Items trainiert — nur echte Modelltests (testdaf.de) + Taktik (Datei 24).
Fürs Lesen gibt es zusätzlich eigene Übungstexte im Teil-3-Stil (Datei 25 / `LESEN`) — als Ergänzung;
echte Modelltests bleiben auch hier Pflicht.
