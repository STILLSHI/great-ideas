# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single self-contained web page, **"The Orrery of Ideas"** — built from `spec.md`. It collects the great *ways of thinking* (not facts) from antiquity to today, organizes them, and synthesizes new ideas by combining old ones. The whole deliverable is **`index.html`**: inline CSS + vanilla JS, no build step, no dependencies, no network calls except Google Fonts.

- `spec.md` — the original brief.
- `index.html` — the entire application.

## Running it

Open `index.html` in a browser. Some browsers block `file://` for certain features; if so, serve it:

```bash
python3 -m http.server 8000   # then open http://localhost:8000/index.html
```

There are no tests, linters, or build commands — it's a static page.

## Architecture

Everything lives in `index.html`. The structure that matters:

- **Three data arrays** in the `<script>` block are the single source of truth — edit these, not the markup:
  - `IDEAS` — the ~74 historical ideas. Each: `{name, who, year, era, cat, idea, way}`. `era` is an index into `ERAS`; `cat` is a key into `CATS`. `idea` is the concept; **`way`** is the transferable "way of thinking" (the page's whole thesis — every idea must carry one).
  - `MODES` — the nine recurring meta-patterns (first principles, inversion, etc.).
  - `SYNTH` — the new combined ideas. Each lists its `parents` (names of source ideas) plus the synthesized `name`/`body`.
- `CATS` (9 categories) and `ERAS` (7 ages, ancient→contemporary) drive the filters, card colors, and the astrolabe. Category colors are CSS custom properties `--c-<key>` in `:root`; the JS reads them by that naming convention (`--c-${cat}`), so keep keys and variable names in sync.
- **Rendering** is plain template-literal `innerHTML` — sections (`#modes-grid`, `#atlas-grid`, `#synth-grid`) are populated from the arrays on load. Stat counts in the hero are derived from array lengths, not hardcoded.
- **The astrolabe** (`buildAstro`, signature element): an SVG built in JS. Rings = ages (innermost ancient → outermost contemporary); each idea is a point placed on its era-ring at an angle distributed within that era, colored by category. Hover shows a tooltip; click calls `jumpToIdea()` which resets filters and scroll-flashes the matching card.
- **Filtering** (`applyFilters`): combined AND across category chip + era chip + search box, toggling a `.hidden` class on cards.

## Conventions

- Apostrophes/quotes appear throughout the idea text, so all data strings use **backticks** (template literals) — keep that to avoid escaping bugs.
- Accessibility/quality floor is intentional: responsive to mobile, visible focus states, and `prefers-reduced-motion` disables the astrolabe sweep, reveal animations, and the card flash. Preserve these when editing.
- Design system is in `:root` custom properties (palette, the Fraunces/Spectral/Space Mono type triad). Change tokens there, not inline.
