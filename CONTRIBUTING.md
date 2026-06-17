# Contributing to Piano Pathfinder

Thanks for taking an interest! Piano Pathfinder is a small, deliberately simple
project: the entire app — markup, styles, and algorithms — lives in a single file,
[`index.html`](index.html), with no build step, no dependencies, and no backend.
That makes it easy to hack on. This guide explains how.

> A quick note on spirit: this project sits at the intersection of music and
> algorithms. Contributions are welcome from both sides — a pianist who notices a
> fingering that "feels wrong," or a programmer who wants to tighten the search.
> Both kinds of feedback make it better.

---

## Getting started

You only need a browser and (optionally) Python 3 for a local server.

```bash
# Open directly
open index.html

# Or serve locally (recommended — matches the dev setup)
python3 -m http.server 4321
# then visit http://localhost:4321
```

There is nothing to install or compile. Edit `index.html`, refresh the page, and
your change is live.

---

## How the file is organized

Everything is in `index.html`, in three parts:

1. **`<style>`** — a small monochrome ("aged paper") design system. Colors live in
   CSS variables under `:root`; components reuse those variables.
2. **`<body>`** — the six panels (Enter a Melody, Keyboard View, Why This
   Fingering?, Recommended Fingering, Try Examples, How It Works).
3. **`<script>`** — the logic, in labeled sections:
   - `noteParser` — scientific pitch (`C4`, `F#4`, `Bb4`) ↔ MIDI numbers
   - `costFunction` — `transitionCost(...)`, the hand-position movement model
   - `fingeringOptimizer` — `optimize(...)` (DP/Viterbi) and `optimizeAStar(...)`
   - `presets` — the built-in example melodies
   - `UI / RENDERING` — keyboard, path strip, DP/A\* panels, result, playback

If you add logic, keep it inside the matching section and follow the surrounding
style.

---

## The cost model is the heart of the project

Most "interesting" contributions touch `transitionCost(fromMidi, fromFinger,
toMidi, toFinger)`. It returns `{ cost, reasons }`, where `reasons` are the
human-readable strings shown in the step-by-step analysis.

The model is a **hand-position model**: each finger has a neutral keyboard offset
(`REST`), and the dominant cost is the wrist displacement a move forces, plus
penalties for thumb-under / finger-over crossings, same-finger re-articulation,
tangled fingerings, and large leaps.

If you change the cost model, the bar is: **it must stay musically sensible.** The
canonical scales are the regression test:

| Melody | Expected fingering |
|--------|--------------------|
| C major ascending | `1 2 3 1 2 3 4 5` |
| C major descending | `5 4 3 2 1 3 2 1` |

A change that makes a scale come out as a two-finger oscillation is a regression,
even if the total cost is lower.

---

## Testing your change

There's no test framework — keep it lightweight. Two checks cover most cases.

**1. Logic check (headless).** The pure functions can be pulled out of the file and
exercised with Node. Confirm the presets still produce sensible fingerings and that
A\* agrees with the DP:

```bash
node -e '
const fs = require("fs");
const js = fs.readFileSync("index.html","utf8").match(/<script>([\s\S]*?)<\/script>/)[1];
const pure = js.substring(0, js.indexOf("const $ = sel"));
const M = new Function(pure + "; return {parseNotes, optimize, frontierSearch, optimizeBFS, PRESETS};")();
for (const [name, str] of Object.entries(M.PRESETS)) {
  const notes = M.parseNotes(str);
  const dp  = M.optimize(notes, {}, "dp");
  const dij = M.frontierSearch(notes, {}, false, "dijkstra");
  const a   = M.frontierSearch(notes, {}, true, "astar");
  const ok  = [dij, a].every(r => Math.abs(r.totalCost - dp.totalCost) < 1e-9);
  console.log(name.padEnd(20), dp.fingers.join(" ").padEnd(28), "DP=Dijkstra=A*:", ok);
}'
```

DP, Dijkstra, and A\* explore the same weighted graph, so they must always report
the same optimal cost — a mismatch means a bug. (BFS deliberately won't match: it
ignores edge weights and is only a baseline.)

**2. Browser check.** Reload the page and confirm:
- the presets load and optimize without errors (open the console — it should be clean),
- the keyboard, DP table, and result panel render,
- switching to "Watch it search (animated)" shows the A\* grid and **Animate** works,
- locking a finger on a note chip re-optimizes the rest.

---

## Style

- **Vanilla everything.** No frameworks, no build tools, no external dependencies —
  the single-file, open-and-run property is a feature. Please keep it.
- Match the surrounding code: 2-space indent, `const`/`let`, small focused
  functions, and comments only where the *why* isn't obvious.
- Colors must use the existing CSS variables; the UI is intentionally monochrome
  (black, white, and grayscale). New accent colors will be asked to change.
- Keep user-facing copy in the warm, plain-language voice the rest of the site uses
  — write for a curious pianist, not a compiler.

---

## Submitting a change

1. Describe **what** changed and **why** — and, for anything touching the cost model
   or optimizer, paste the before/after fingerings for the canonical scales.
2. Keep pull requests focused; one idea per PR is easier to review.
3. Make sure both checks above pass and the console is clean.

---

## Ideas worth picking up

From the roadmap, in rough order of impact:

- Left-hand mode (mirror the rest position) and two-hand fingering
- Chord / polyphony support
- MIDI or MusicXML import
- A fingering-comparison view (show two strategies side by side)
- Better difficulty scoring
- Practice mode; export the result as an image

Found a melody whose recommended fingering feels wrong to play? That's one of the
most valuable reports you can file — open an issue with the notes and the fingering
you'd expect instead.

---

Maintained by [wuisabel-gif](https://github.com/wuisabel-gif).
