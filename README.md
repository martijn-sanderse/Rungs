# Rungs

A lightweight calisthenics progress tracker. Log workouts, and see exactly
which rung of each progression you're on — and when the data says you're
ready for the next one.

Built around the category system from *Overcoming Gravity* (Steven Low):
vertical push, vertical pull, horizontal push, horizontal pull, core, legs.
Each category is a chain ("progression") of exercises in increasing
difficulty; you climb one rung at a time.

## Running it

`rungs.html` is a single, dependency-free file. No build step, no server.

- **On a phone**: open it in Safari/Chrome, then "Add to Home Screen" for an
  app-like icon.
- **Anywhere else**: just open the file in any modern browser.
- **Docker**: serve it as a static file (e.g. nginx) — see "Porting" below.

It's built with [Preact](https://preactjs.com/) + [htm](https://github.com/developit/htm),
loaded from a CDN (esm.sh) — no bundler, no `npm install`.

## Data model

- **Exercise** — a single movement in a progression chain.
  `{ id, category, progressionOrder, name, unit ('reps'|'sec'), targetSets, targetReps, cue }`
- **Routine** — a named group of Exercises trained together in one session.
  `{ id, name, exerciseIds: [...] }`
  At log time, each exercise in a routine resolves to whichever exercise is
  currently *active* in its category — so advancing a progression doesn't
  require editing your routines.
- **Workout** — one logged training session.
  `{ id, routineId, date, entries: [{ exerciseId, sets: [{ value, difficulty, toFailure }] }] }`

Progression state (which exercise is "active" per category) is stored
separately from the exercise definitions themselves, so advancing just
moves a pointer forward.

## Advancement logic

The Progress tab looks at your most recent logged workout for the active
exercise in each category. If you hit target sets/reps and every set felt
"easy," the rung glows and suggests advancing — but advancing always
requires you to manually confirm via the "Advance →" button. Nothing moves
automatically.

## Storage / porting

All persistence goes through two functions near the top of the `<script>`
block: `storageGet` and `storageSet`. Currently they wrap `localStorage`,
which is why the file works standalone with zero setup. To port this to a
real backend (e.g. for a Docker deployment with a proper database or
multi-device sync), these two functions are the only thing that needs to
change — the rest of the app (UI, progression logic, data model) is
storage-agnostic.

## Extending

- **Adding exercises**: currently done by editing `SEED_EXERCISES` in the
  script directly. A UI for adding exercises is a planned later iteration.
- **New categories**: add an entry to the `CATEGORIES` array (with a CSS
  color variable) and it flows through the whole app automatically.

## Status

Personal project, actively evolving. No warranty, no guarantees — it's a
training log, not a certified program.
