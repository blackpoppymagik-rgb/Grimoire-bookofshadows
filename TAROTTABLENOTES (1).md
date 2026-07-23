# The Tarot Table — Build Notes

**Book of Shadows v22** · Built 2026-07-08 by Fable
One file, three modules now: Pendulum Training (v20) + Sigil Studio (v21) + Tarot Table (v22).

## Where the spreads module actually was

To answer the question that started this: a spreads module (1-card / 3-card / Celtic Cross /
full) was **not** in the grimoire file you shipped (`grimoirepwa_2.zip`, v19.1) and was not
built in this session before now. The only tarot in the file was the daily single card on the
ritual page. If a spreads module was designed in an earlier chat, it never got merged into
this file. It exists now.

## What shipped

A **Tarot Table** view (landing-page button), three tabs, built on top of the deck you
already have — all 78 cards with their keywords, Light/Shadow texts, Black Poppy lines, and
embedded Rider-Waite-Smith images. No new card content was written; the module reuses your
deck verbatim.

- **Table** — choose a spread, optionally set a question, **Shuffle & Deal**. Cards deal
  face-down (crypto-shuffled, no card repeats within a spread) under their position labels;
  tap to turn each in whatever order the reading wants, or Reveal All. Every turned card
  shows its RWS image, keywords, and an expandable "Read this card" panel: its seat, Light,
  Shadow, and the Black Poppy line. Per your deck philosophy, **no card is dealt reversed** —
  the footer and deck note say so explicitly.
- **Spreads:**
  - **One Card** — The Heart of It.
  - **Three Card** — The Past / The Present / The Future.
  - **Celtic Cross** — the traditional 10 positions, laid in an actual cross on screens
    ≥860px (staff of four to the right), stacking cleanly on phones.
  - **The Full Wheel** — 12 cards, one per astrological house (Self, Value, Voice, Roots,
    Joy, Work, Partnership, Depths, Journey, Calling, Community, Mystery).
- **Readings** — every saved reading records spread, question, all cards with their
  positions, and your notes; searchable across all of it.
- **Guide** — how to read a spread as one sentence, plus an article per spread (the Celtic
  Cross piece is labeled HISTORY and credits Waite's 1910 *Pictorial Key*).

## Data & wiring

`tarot:readings` — `[{id, stamp, iso, spread, question, cards:[{pos,name}], notes}]` —
wired into the vault count (Tarot Readings), JSON backup/restore (`version: "v22"`),
CSV export (`tarot reading` rows), `grimoireSync()`, and `grimoireSave('tarot-reading', …)`.
`sw.js` cache bumped to `grimoire-v22` — deploy both files together as usual.

## Verified

33/33 browser checks: all four spreads deal the right counts with no duplicate cards;
tap-to-flip and Reveal All; save refuses until every card is turned; card detail shows
Light/Shadow/Black Poppy; readings save, search, escape HTML, and persist across reload;
Celtic Cross uses the cross layout on desktop; vault/export shapes correct; **Pendulum and
Sigil Studio confirmed untouched**; no console errors; no mobile horizontal overflow.
