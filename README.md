# Stadium Logistics Rover — Companion

The interactive companion for TETC's "Stadium Logistics Rover" lesson — a
90-minute Python intro using the VEX AIM Coding Robot (9th/10th grade).
This site is the whole lesson artifact (there is no paper sheet).

**Live:** https://tetc-edu.github.io/Stadium-Rover-Companion/
**Repo:** https://github.com/TETC-Edu/Stadium-Rover-Companion (org: TETC-Edu)

## Deploying changes

```bash
git add <file> && git commit -m "describe the change" && git push
# GitHub Pages picks it up in ~60s; hard-refresh to see it
```

Keep the URL stable — student progress lives in `localStorage`, keyed to the
origin. Moving hosts wipes everyone's state.

## Files

```
index.html   — the entire app (CSS + markup + vanilla JS IIFE, no build step)
field.html   — standalone Field Reference page, linked from the sidebar
blocks.html  — "What are blocks?" primer for absolute beginners. A reference,
               NOT a level: no progress, no completion gate. Optional link at
               the top of Level 1, plus one in the sidebar.
fonts/       — EMprint Regular + Semibold (real TETC brand font, shipped as web fonts)
logo-tetc.png, favicon.png
```

No frameworks, no build, works offline once loaded. Chromebook-first;
mobile gets a hamburger drawer at ≤980px.

## The lesson

Students sketch a stadium on a 100×100 cm dry-erase mat with an Expo marker
(three zones, three cargo, a rover START), measure every leg of the rover's
path, then code three deliveries. Each delivery is **two legs**: drive to the
cargo (rover grabs it), then carry it to its zone. The next delivery starts
wherever the last one ended — no return trips, no "supply room."

One rule: each cargo starts at least 30 cm from its delivery zone.

Same challenge three times, one new idea per level:

1. **Level 1 — Blocks.** Sketch and measure the field, then code all three
   deliveries in VEXcode Blocks.
2. **Level 2 — Switch.** Re-sketch the field, convert blocks to Switch, fix
   every number by hand. Re-sketch and convert come first, then "How to Read
   Python" — the WHO.WHAT(DETAILS) anatomy with a you-try decode challenge —
   then the block→Python decoder.
3. **Level 3 — Python.** Re-sketch again, type sequential Python from
   scratch. No loops, no variables. Ends with "Send your engineer's log" —
   a mailto that emails all three "What happened?" logs to
   taylor.eads@tetc.org.

Path picker on the landing page routes by experience: never coded → L1,
used blocks → L2, coded in text → L3.

## State model

`localStorage` key `tetc_rover_companion_v2`:

```js
{
  path: 'beginner' | 'intermediate' | 'advanced' | null,
  screen: 'landing' | 'level1' | 'level2' | 'level3',
  completed: { level1, level2, level3 : bool },
  failure:   { level1, level2, level3 : string },   // "What happened?" logs
  hints:     { <level>: { <cardIdx>: { shown, opened } } }
}
```

Loads shallow-merge against defaults, so adding keys is safe. To version-break
on purpose, bump to `_v3`.

## Rules that are intentional (don't "fix" these)

- **One new idea per level.** Never combine cognitive jumps.
- **L2's manual number-fixing stays painful.** No helpers that sync values —
  feeling the repetition is the point.
- **Hint cards are 3 steps** (try first / then this / approach). Hint 3
  reframes; it never gives the answer.
- **Left/right turn language only.** No compass headings, no +/- angles,
  no `turn_to()`.
- **Kid-friendly words.** If a 9th grader wouldn't say it, rewrite it.
  Banned: tedium, enumerate, scaffold, abstraction, sequential, iterate,
  heading, bearing, method, object, parameter, syntax.
- **No scoring, no timer, no vision sensor, no Express Lane.** All removed
  deliberately; vision is saved for a future lesson.
- **"What happened?"** is the failure-log header — observation, not shame.
- **No emoji.** TETC brand rule.

## Brand

EMprint (Regular 400 / Semibold 600–900) with Calibri fallback,
`font-display: swap`. Colors live as CSS custom properties at the top of each
file's `<style>` — teal/magenta/amber palette, square corners, no gradients.

## Known limitations

- Single-device state; switching Chromebooks loses progress.
- No teacher dashboard (mailto log is the v1 of that; Sheets/Firebase deferred).
- No screen-reader audit yet.
