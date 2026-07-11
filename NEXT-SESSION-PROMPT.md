# Continuation prompt (paste into Claude Code in the project folder)

Copy everything below the line into a new Claude Code session — works for both
Alexander's machine and Kirill's clone of https://github.com/Alexandrus-the-Great/testdaf-trainer

---

I'm continuing work on the **TestDaF C1+ study system for Kirill** (exam target: January 2027).
Read `CLAUDE.md` first — it has the architecture, build workflow, and known pitfalls.

**State of the project:**
- Live app: https://alexandrus-the-great.github.io/testdaf-trainer/ (GitHub Pages, deploys on push from the `site/` git repo)
- `heute.html` = source of the daily app (edit this, NEVER `site/index.html`). Rebuild with `python tools/build_site.py`, push from `site/`.
- The working folder `C:\Users\alexa\Documents\Claude code` is itself a git repo (branch `master`, local only) — commit there too after changes.
- Study content = `README.md` + files `01`–`25` (German). After editing md files: `python tools/build_komplett.py` + LibreOffice headless → docx/pdf
  (convert to a temp dir and copy back — in-place overwrite silently fails when a soffice instance lingers; kill soffice first).
  The Komplett-HTML hides every "**Lösung:**" behind a click (JS in build_komplett.py); docx/pdf show everything.
- The app (all interactive, everything tap-to-reveal):
  - START checkbox with real-date anchoring, behind/ahead tracking with Aufholmodus, movable exam date, spaced repetition (1/3/7 weeks)
  - daily Übungsblock: 4 of 11 grammar/vocab modules (`UEB`), fold-out solutions — file 22 mirrors them, anki-grammatik.csv has 92 cards
  - **Mo** Lückentext der Woche (`LUECKEN`: 12 gap texts in Wissenschaftssprachliche-Strukturen style, each gap tap-to-toggle, "Alle Lücken aufdecken" — file 22 mirrors them)
  - **Fr** Lesetraining (`LESEN`: 12 own C1 texts, Ja/Nein/„Text sagt dazu nichts" with fold-out solutions — file 25 mirrors them)
  - **Sa** Reproduktions-Trainer (`REPRO`/`KERN_STICHWORT`: full model essays / weekly Kernsätze hidden behind keywords, "Zeige den ganzen Text")
  - Redemittel cheat sheets carry a **🗣️ Satzbau-Training** (`BSP`: Redemittel + Stichworte → build the full sentence, fold-out solution);
    the **gap variant of the same sentences** appears as a 5′ 🔁 task on a DIFFERENT weekday (`BSP_WDH`: Mo Grafik · Mi Schablone · Do Zsf · Fr Redemittel · Sa Sprechen7) — deliberate spaced repetition, do NOT merge the two variants onto one day (Alexander's explicit requirement)
  - a "▸ Material & Spickzettel" fold-out on every task

**Non-negotiable quality gate — verify BEFORE every push:**
1. **`node tools/audit.js` must report 0 errors** (currently 666 tasks checked). It verifies: every task on every
   day has fold-out material (or is rev/self-contained), Mo has the Lückentext, Fr the Lesetext, Sa W1–12 the
   keyword trainer, 5 Redemittel-Lücken reviews per week (rev-marked, tappable gaps), week↔module mapping valid,
   12 reading texts + 12 gap texts complete, every BSP category has its Satzbau block.
   **Extend the audit whenever you add a new content type** — that's how gaps were caught so far.
2. `node --check` on the extracted `<script>` — the German-quotes trap (`„Wort"` with ASCII `"` inside
   double-quoted JS strings) has broken the app before. Backticks are safe.
3. Click-test in the preview (`.claude/launch.json` → server `testdaf-heute`, port 8123):
   open a Monday (Lückentext), a Friday (Lesetext), a Saturday (Reproduktions-Trainer), confirm taps reveal/hide.
4. After push: poll the live URL until the new marker string appears (usually live in 10–20 s).

**Improvement backlog (in rough priority order):**
- Hören-Training: kurze Diktat-/Lückenübungen aus den Musterantworten (Datei 16) im Übungsblock-Stil — der einzige Prüfungsteil ohne eigenes interaktives Element
- Mehr Beispielsätze pro BSP-Kategorie + wochenweise Rotation, damit die 🔁 Redemittel-Lücken nicht jede Woche identisch sind
- Statistik-Tab in der App: Wochenverlauf der erledigten Aufgaben, Streak, Übungsblock-Trefferquote (localStorage auswerten)
- Übungsblock erweitern: mehr Items pro Modul (Datei 22 + `UEB` in heute.html parallel pflegen, Anki-CSV nachziehen)
- Selbsttest-Modus: 10 zufällige Übungsblock-Aufgaben als "Quiz" mit Auswertung
- Mehr LESEN-/LUECKEN-Texte (aktuell je 12, rotieren ab W13 bzw. wiederholen sich nach 12 Wochen)
- Grafiken für weitere der 54 Themen (`grafiken/make_grafiken.py`, SVG-Backend!)
- Optional: Repo-Umbau, damit die Quellen (md, tools, heute.html) mit im GitHub-Repo liegen

Start by running the quality-gate audit (item 1) to confirm the current state, then pick up the backlog — or whatever I ask for next.
