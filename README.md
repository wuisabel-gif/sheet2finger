# 🎹 Piano Pathfinder

I've always loved two things: music and algorithms.

When I'm practicing piano, I often find myself thinking about fingering the same
way I think about optimization problems. Every finger choice affects the next one.
A comfortable move now might create an awkward stretch later. A small thumb-under
can save a large hand shift. Over time, I started wondering:

> **What if piano fingering could be treated as a pathfinding problem?**

Piano Pathfinder is my attempt to explore that idea.

Given a melody, it searches through possible fingerings and recommends one that
minimizes unnecessary movement, awkward stretches, and difficult crossings. You can
see the result directly on a piano keyboard, lock in finger choices you prefer, and
instantly see how the solution changes.

It's designed for students, teachers, and curious pianists who want to understand
not only *what* fingering works, but *why* it works.

---

## ✨ Live Demo

A single-file web application with no dependencies, build tools, or backend.

```bash
# Open directly
open index.html

# Or run a local server
python3 -m http.server 4321
```

Then visit:

```text
http://localhost:4321
```

Everything runs entirely in the browser. No accounts, no installation, and no MIDI
hardware required.

---

## 🧠 The Idea

Under the hood, each note becomes a layer in a graph. For every note, there are
five possible states:

- Thumb (1)
- Index (2)
- Middle (3)
- Ring (4)
- Pinky (5)

Moving from one note to the next carries a cost based on how awkward that movement
would be for a pianist.

Some movements are naturally comfortable:

- neighboring fingers in the same hand position
- smooth scale patterns
- standard thumb-under transitions

Others are more expensive:

- large stretches
- tangled crossings
- repeated use of the same finger
- excessive hand shifts

The goal is simple: **find the fingering with the lowest total movement cost.**

In computer science terms, it's a shortest-path problem solved with dynamic
programming. In piano terms, it's trying to find the fingering that feels the most
natural.

---

## 🎛️ Features

**Melody Input** — type a right-hand melody using scientific pitch notation:

```text
C4 D4 E4 F4 G4 A4 B4 C5
```

Supports sharps (`F#4`), flats (`Bb4`), and spaces or commas between notes.

**Keyboard Visualization**

- Interactive piano keyboard
- Finger numbers displayed directly on the keys
- Click any key to hear the note
- Visual display of the recommended path

**Fingering Optimization**

- Dynamic Programming (Viterbi-style)
- Optional A\* graph-search mode
- Real hand-position cost model

**Algorithm Explorer** — see exactly how the optimizer reaches its decision:

- DP cost table
- Optimal path highlighting
- Transition-by-transition cost breakdown
- A\* frontier visualization and animation

**Interactive Editing** — don't agree with a fingering? Lock any finger choice and
the optimizer recomputes the rest of the passage around your preference.

**Preset Examples**

- C Major Scale
- G Major Scale
- Chromatic Scale
- Simple Arpeggio
- Für Elise Opening

**Playback** — listen to the melody directly in the browser using the Web Audio API.

---

## 🎼 Why the Cost Model Matters

A naïve optimizer often produces strange results that are mathematically valid but
musically terrible. The hand-position model encodes common piano technique so that
the recommendations resemble what a pianist would actually play.

For example, an ascending C major scale naturally produces:

```text
1 2 3 1 2 3 4 5
```

instead of repeatedly alternating between two fingers.

The goal isn't to claim there is only one correct fingering. The goal is to provide
a sensible starting point that respects both the music and the mechanics of the hand.

---

## 🗂️ Project Structure

```text
sheet2finger/
├── index.html      # Entire application
├── README.md
└── .claude/
    └── launch.json
```

---

## 🛣️ Future Ideas

- Left-hand mode
- Two-hand fingering
- Chord optimization
- MIDI import
- MusicXML import
- Fingering comparison mode
- Difficulty scoring improvements
- Practice mode
- Export results as images

---

Built by **[wuisabel-gif](https://github.com/wuisabel-gif)**.

© 2026 [wuisabel-gif](https://github.com/wuisabel-gif). All rights reserved.
