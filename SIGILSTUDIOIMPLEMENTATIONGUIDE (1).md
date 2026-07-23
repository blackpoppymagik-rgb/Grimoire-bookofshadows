# Sigil Studio — Implementation Guide

**Book of Shadows v21** · Built 2026-07-08 by Fable · Spec: *Black Poppy Grimoire — Module Specification: Sigil Studio*
Ships in the same `grimoire.html` as the Pendulum Training module (v20) — one file, both modules.

---

## 1 · What shipped

A **Sigil Studio** view, reached from the landing page, with five sections behind the same
accessible tab system as Pendulum Training (arrow keys, 44px touch targets, both themes):

| Tab | What it does |
|---|---|
| **Studio** | The drawing table. Freehand monoline canvas (mouse, pen, finger — pointer events with `touch-action: none` so the page never scrolls mid-stroke). **Mirror mode**, **rotational symmetry** (off/×2/×4/×6/×8, combinable with mirror), **grid snap**, **negative-space preview**, stroke weight slider, **undo/redo/clear**, and **SVG + PNG export**. Live symmetry preview while you draw. |
| **Intention** | The workshop: ten theme chips (Protection … Transformation), the four reflection questions from the spec, and a working intention that **travels with every sigil you save**. Plus a live **letter-reduction** tool: type the sentence, strike vowels/repeats (toggleable), take the surviving letters to the canvas. |
| **Symbols** | A curated library — 25 hand-built monoline symbols across Nature, Animals, Celestial, Geometry — each stamps onto the canvas at one tap, to be traced and made yours. Below it, the **Personal Symbol Dictionary**: save the current canvas as a named symbol with its meaning; the dictionary tracks first use, usage count, and linked sigils automatically. |
| **Methods** | Six labeled readings (HISTORY / SCIENCE / PRACTICE, kept distinct per the Canon rule): where sigils come from (sigillum → Agrippa → Spare), semiotics and why symbols work, intention vs. expectation, the six construction methods, design principles with the recognition and scalability tests, and an ethics piece on borrowed symbols. |
| **Gallery** | Private gallery with search, favorites filter, and SVG thumbnails. Each sigil opens its **Meaning Journal** page — intention, prompts, symbols used, tags, reflections (add any time), **version history** (re-saving keeps up to 12 prior versions), load-back-into-Studio for editing, per-sigil SVG/PNG export. Below: **Pattern Notes**. |

### Design Assistant (Observe)
On-device measurement of the current canvas — no API, no ranking, observations only:
symmetry used, quadrant ink weight ("the lower left quadrant carries 47% of the ink"),
centroid offset, stroke economy, small-size readability warning, rough ink coverage /
negative space. Phrased exactly per spec: observations, never judgments.

### Pattern Notes (the "AI Pattern Recognition" requirement)
Computed locally across the saved gallery (needs ≥3 sigils): recurring symbol motifs,
open-vs-dense composition trend (average strokes early vs. recent), symmetry habit.
Rendered under a permanent "noticing is the whole job" disclaimer.

## 2 · Data model & storage keys

```
sigilstudio:sigils     [{ id, title, stamp, iso, intention, prompts{theme,text,nurture,
                          challenge,values,feel}, strokes[{pts,width,mirror,rot,sym}],
                          symbolsUsed[], settings{mirror,rot}, tags[], fav,
                          journal[{stamp,text}], versions[{stamp,strokes}] }]
sigilstudio:symbols    [{ id, name, meaning, category, strokes, firstUse, usage, linkedSigils[] }]
sigilstudio:intention  { theme, text, nurture, challenge, values, feel }
```

Strokes are stored as normalized polylines in a 1000×1000 space with their symmetry
settings, so every sigil re-renders losslessly as SVG anywhere — thumbnails, gallery,
exports — and stays theme-aware (`currentColor` in the UI, ink-black in exports).

Wired into every existing data path: vault live counts (Studio Sigils, Personal Symbols),
JSON backup/restore (`version: "v21"`), CSV export (`studio sigil`, `personal symbol` rows),
`grimoireSync()`, and `grimoireSave()` on every save (types `sigilstudio-sigil`,
`sigilstudio-symbol`).

## 3 · What changed in the files

- `grimoire.html`: changelog + title → **v21**; landing gets a "Sigil Studio" pill; one CSS
  block, one `<section id="sigilStudioView">`, one JS engine block; the 12 export/import/
  sync/vault touch-points listed above. Shares the module UI classes (`pend-tab`,
  `pend-sheet`, `pend-chip`) with Pendulum Training — they are the grimoire's generic
  module kit now.
- `sw.js`: `CACHE_NAME` → `grimoire-v21` (deploy both files together, as always).

## 4 · Verification performed

- `node --check` on the full inline script — clean.
- **Desktop drive-through (36/36 checks):** drawing commits strokes; mirror + ×4 rotation
  expands one stroke into 9 rendered copies; undo/redo; assistant reports symmetry and
  coverage; intention saves and travels to saved sigils; letter reduction ("I keep my own
  borders" → `K P M Y W N B R D S`); library stamping; personal symbol saved and rendered
  in the dictionary; gallery save / re-save-as-version-2 / save-as-new; meaning journal
  reflections (XSS probe rendered inert); favorites filter; tag search; real SVG file
  download; vault counts; export shape; persistence across reload; **Pendulum module
  unaffected**.
- **Mobile (iPhone 13 emulation):** no horizontal overflow on any tab; tap-targets 44px;
  symbol stamping by touch; tap and real CDP touch-drag both commit strokes; page does
  not scroll while drawing. One real bug was found by this pass and fixed:
  `setPointerCapture` could throw on fast touch sequences and kill the stroke — now guarded.

## 5 · Deferred (from the spec's own future list, plus judgment calls)

- **PDF export** — SVG + PNG ship; PDF needs a library the no-dependency rule forbids.
  (SVG imports cleanly into Cricut/engraving workflows, which the spec lists as future.)
- **Photos / voice notes on journal pages** — same localStorage-budget reasoning as the
  Pendulum module; the journal takes text reflections today.
- **Full layer system** (lock/reorder/transform-selection) — undo/redo + stamp-and-refine
  covers the working need; a real layer UI is a v-next project.
- **Community inspiration features** — need a server; the data model is ready.

## 6 · Design notes

Calm studio, not a symbol generator: nothing is auto-generated, every mark is drawn or
deliberately stamped-then-traced. No instant-magic claims anywhere — the Methods articles
say plainly that repetition and meaning-making are the mechanism. History, science, and
practice are labeled and kept separate; the ethics article addresses cultural symbols
directly (the curated library deliberately sticks to universal forms). Empty states are
invitations. Silver stays the metallic; no gold anywhere.
