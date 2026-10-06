# AGENTS.md

Single-file, offline static web app (Spanish, Rioplatense) that generates 1248×208 PNG
"cenefa" images for a stage LED. No package manager, build, test, lint, or typecheck.
Everything ships in `index.html`.

## Hard constraints
- `index.html` is the entire app AND the GitHub Pages entrypoint. Deployed from the repo
  root on push to `master` (`.github/workflows/static.yml`). Do not rename it, split it,
  or add a build step.
- Must keep working offline via `file://`: fonts are inlined as base64 `@font-face`, there
  are no network calls and no external dependencies. Never add a CDN/import/fetch.
- UI strings, messages, and errors are Spanish. Keep new user-facing text in Spanish.
- Default branch is `master` (not `main`).

## index.html layout (one 649-line file)
- ~L1-111: `<style>` (CSS + base64 fonts `PonchitoText` and `CenefaSans`).
- L112-335: `<script type="module">` — all app logic.
- L336-341: `<script type="application/json" id="initialSchedule">` — real event program data.
- L342-363: `<body>` markup (all IDs referenced by the script live here).
- L364-649: `<script type="text/plain" id="fontLicense">` (DejaVu license; do not delete).

## Logic quirks
- `drawBoard` renders the exact 1248×208 canvas; modes `three`/`two`/`one` change panel
  widths (`[624,312,312]` / `[748,500]` / `[1248]`) and palette.
- `wrapText`/`fitText` shrink font to fit but never split a word; a single too-wide word
  throws. Preserve this behavior.
- `makeZip`/`crc32` are a hand-rolled ZIP writer (batch export) — no library.
- `readSchedule`/`parseDelimited` parse pasted Sheets/CSV/TSV, auto-detect separators,
  hide `Locución`-type rows (`isLocution`), and split days via a `Día` column or weekday row.
- `document.fonts.load('900 48px PonchitoText')` runs before the first `render()`.
- If `document.modelContext` exists, two WebMCP tools are registered
  (`read_stage_board`, `select_stage_artist`). Don't remove.

## Verify changes
- Open `index.html` in a browser (double-click / `file://`). No automated tests exist.
- Check the PNG export still produces exactly 1248×208.

## Known gotcha
- The footer "Descargar app para usar sin conexión" link points to
  `Ponchito_Cenefa_Offline_V3.html`, which does not exist in the repo (stale after the
  `cenefa-sin-conexion.html` → `index.html` rename). Don't "fix" it by renaming `index.html`.
