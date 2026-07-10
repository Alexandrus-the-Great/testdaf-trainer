# Continuation prompt (paste into Claude Code in the project folder)

Copy everything below the line into a new Claude Code session — works for both
Alexander's machine and Kirill's clone of https://github.com/Alexandrus-the-Great/testdaf-trainer

---

I'm continuing work on the **TestDaF C1+ study system for Kirill** (exam target: January 2027).
Read `CLAUDE.md` first — it has the architecture, build workflow, and known pitfalls.

**State of the project:**
- Live app: https://alexandrus-the-great.github.io/testdaf-trainer/ (GitHub Pages, deploys on push from the `site/` git repo)
- `heute.html` = source of the daily app (edit this, NEVER `site/index.html`). Rebuild with `python tools/build_site.py`, push from `site/`.
- Study content = `README.md` + files `01`–`24` (German). After editing md files: `python tools/build_komplett.py` + LibreOffice headless → docx/pdf.
- The app has: START checkbox with real-date anchoring, behind/ahead tracking with Aufholmodus, movable exam date, spaced repetition (1/3/7 weeks), daily Übungsblock (4 grammar/vocab tasks with fold-out solutions), and a "▸ Material & Spickzettel" fold-out on every task.

**Non-negotiable quality gate — verify BEFORE every push:**
1. **Every task on every day must be an actual clickable task with fold-out material/solutions.**
   Run the audit: extract the `<script>` from `heute.html`, cut at `// ── Rendering`, stub
   `localStorage`, then for weeks [0,1,6,14,15,20,23,25,26] × weekdays Mo–Sa call `tag(date)` and
   assert every task has `m` (material), is a review (`rev`), or is inherently self-contained
   (Übungsblock/Anki/Reservezeit/Tipp-Training). Print any task without material and fix it —
   don't push with gaps. (A working version of this audit exists in the session history as `logic3.js`.)
2. `node --check` on the extracted script — the German-quotes trap (`„Wort"` with ASCII `"` inside
   double-quoted JS strings) has broken the app before. Backticks are safe.
3. Click-test in the preview (`.claude/launch.json` → server `testdaf-heute`, port 8123):
   open a theme-week Tuesday and a Phase-3 Thursday, confirm fold-outs open and charts load.
4. After push: poll the live URL until the new marker string appears.

**Improvement backlog (in rough priority order):**
- Hören-Training: kurze Diktat-/Lückenübungen aus den Musterantworten (Datei 16) im Übungsblock-Stil
- Statistik-Tab in der App: Wochenverlauf der erledigten Aufgaben, Streak, Übungsblock-Trefferquote (localStorage auswerten)
- Übungsblock erweitern: mehr Items pro Modul (Datei 22 + `UEB` in heute.html parallel pflegen, Anki-CSV nachziehen)
- Selbsttest-Modus: 10 zufällige Übungsblock-Aufgaben als "Quiz" mit Auswertung
- Grafiken für weitere der 54 Themen (`grafiken/make_grafiken.py`, SVG-Backend!)
- Optional: Repo-Umbau, damit die Quellen (md, tools, heute.html) mit im GitHub-Repo liegen

Start by running the quality-gate audit (item 1) to confirm the current state, then pick up the backlog — or whatever I ask for next.
