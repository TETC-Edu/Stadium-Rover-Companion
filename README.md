# Stadium Logistics Rover — Companion

An interactive HTML companion to the paper student sheet for TETC's "Stadium
Logistics Rover" lesson (90-minute Python intro using the VEX AIM Coding Robot,
9th/10th grade). This is the **HTML companion only** — the paper sheet owns
stadium sketches, measurement tables, exit ticket, and handwritten reflection.

## What this bundle is

A single file, `index.html`, that is the complete, working artifact. No build
step, no dependencies, no backend. Open it in any modern browser and it works.

You're picking it up to:
1. **Continue iterating on it** in Claude Code as the lesson evolves.
2. **Host it somewhere** so kids can reach it from a Chromebook.

This is not a "recreate this design in React" handoff. The HTML *is* the
shipping artifact. Edit it in place.

## Quick start

```bash
# Run locally
cd claude-code-handoff
python3 -m http.server 8000
# open http://localhost:8000
```

Or just double-click `index.html`. (Some browsers restrict the Clipboard API
on `file://` URLs — the "Copy starter code" button falls back to a manual
prompt. Serving it over HTTP/HTTPS fixes that.)

## Hosting options (pick one)

| Option | Cost | Time | Notes |
|---|---|---|---|
| **Netlify Drop** ([netlify.com/drop](https://netlify.com/drop)) | Free | ~30 sec | Drag the folder onto the page, get a URL. No signup needed for a one-off. |
| **GitHub Pages** | Free | ~5 min | Push to a repo, enable Pages on the main branch. Best if you'll iterate often. |
| **Cloudflare Pages** | Free | ~5 min | Wrangler CLI: `npx wrangler pages deploy claude-code-handoff`. |
| **Google Drive** | Free | ~2 min | Upload, share "Anyone with link," use the `/preview` URL. Slightly slower load. |
| **Google Sites** | Free | ~10 min | Embed the file. Heavier than needed, but plays nice with Google Classroom. |

Recommendation for ongoing iteration: **GitHub Pages**, because then Claude
Code can `git push` and the new version is live.

**State note.** The companion saves student progress to `localStorage`, keyed
to the URL origin. Once you pick a host, *keep that URL stable* — moving to a
new URL means kids on the old URL won't see their state on the new one.

## File structure

```
claude-code-handoff/
├── README.md          ← you are here
└── index.html         ← the entire application (~2,400 lines)
    ├── <style>        — TETC design tokens + all component CSS
    ├── <body>         — six <section class="screen"> blocks
    └── <script>       — vanilla JS IIFE, no framework
```

Everything is inlined intentionally. Splitting CSS/JS into separate files is
allowed but not required. If you split, keep the load order the same and don't
add a build step — this needs to stay one-folder-deploy simple.

## What's in the app

### Screens

1. **Landing** — World Cup hook + Path Picker (Beginner / Intermediate / Express)
2. **Level 1 — Blocks** — single delivery, three hint cards
3. **Level 2 — Switch** — interactive Block→Python decoder, three hint cards
4. **Level 3 — Python** — starter scaffold (copyable), 60s timer, 3/2/1 scoreboard, three hint cards
5. **Bonus — Vision** — locked behind L3, vision-sensor scenario, two hint cards
6. **Express Lane** — separate path for the Type-D coder; reference-implementation brief + autosaving editor

One screen is visible at a time; switching is pure CSS `[hidden]` toggle.

### Persistent UI

- **Left sidebar** with the progress rail, collapsible Command Cheat Sheet, collapsible Common Errors, and "Reset my progress" (with confirm modal).
- **Top strip** with breadcrumbs and a path chip ("change" link returns to Landing).

### State model

All persisted in `localStorage` under key `tetc_rover_companion_v1`:

```js
{
  path: 'beginner' | 'intermediate' | 'express' | null,
  screen: 'landing' | 'level1' | 'level2' | 'level3' | 'bonus' | 'express',
  completed:   { level1, level2, level3, bonus, express : bool },
  failure:     { level1, level2, level3 : string },          // textarea logs
  hints:       { <level>: { <cardIdx>: { shown:int, opened:bool } } },
  scores:      { ball, blue, orange : bool },                // L3 scoreboard
  timer:       { remaining:int (sec), running:bool },        // L3 timer
  expressCode: string                                        // Express Lane editor
}
```

The shape is shallow-merged with `defaultState` on load, so adding new keys in
future versions is safe — existing students don't get wiped.

To version-break on purpose (e.g. you reshape the data), bump the key to
`tetc_rover_companion_v2`.

### Hint progression contract

Each hint card has three steps (`.hint-step.h1` / `.h2` / `.h3`).
- Hint 1 = "try first" (cheap observation)
- Hint 2 = "then this" (mechanical fact / point to a thing)
- Hint 3 = "approach" (reframes the problem, never gives the solution)

If you add a hint card, follow this contract. **Don't ever write a Hint 3 that
gives the answer.** The pedagogy depends on the student doing the work.

### Pedagogy anchors baked into the code

These are intentional — please don't "fix" them:

- **Level 2's tedium is preserved.** The hint about "I edited the number but
  nothing changed" tells students to *notice the tedium* — that frustration is
  what motivates variables in L3. Do not add a helper that syncs values.
- **"What happened?"** is the failure-log header, not "what went wrong?" One
  invites observation, the other invites shame.
- **Express Lane is "different work, not harder work."** No "advanced" labels,
  no "challenge mode" energy. If you add Express content, keep that frame.
- **No emoji anywhere.** TETC brand rule. Colored dots and phase chips do the
  job emoji would do in other systems.

## Design tokens

The brand colors live as CSS custom properties at the top of `<style>`:

| Token | Hex | Used for |
|---|---|---|
| `--teal` | `#00ACA8` | Primary brand, Blocks phase, headers |
| `--teal-dark` | `#007096` | Vision/EVALUATE phase, dark surfaces |
| `--magenta` | `#B01E78` | Python phase, pull-quotes, magenta CTAs |
| `--amber` | `#F18E21` | Switch phase, Notice callouts, warn states |
| `--blue` | `#3C87E3` | Step 1 / Partner Testing accent (used sparingly) |
| `--ink` | `#1F2A2B` | Display text, near-black |
| `--body` | `#333333` | Body copy |
| `--muted` | `#5A5A5A` | Secondary text |
| `--border` | `#E4E4E4` | Dividers |
| `--panel` | `#F5F5F5` | Light surface fills |

Type stack is **Calibri → Segoe UI → Helvetica Neue → system-ui**. EMprint is
the real brand font but is proprietary; Calibri is the documented fallback.

## How to iterate (Claude Code tips)

When you ask Claude Code to change something, useful framings:

- **"Update Level 2's third hint to reference the protractor mark"** —
  precise, points at one block.
- **"Add a Level 2 progress bar showing how many numbers they've edited"** —
  ⚠️ this would remove the L2 tedium that L3 depends on. Don't add it.
- **"Add a fourth hint card to Level 3 for 'my robot won't pair'"** — follow
  the 3-step hint contract above.
- **"Add a teacher view that surfaces failure logs"** — would require a
  backend or a shared store; out of scope of this single-file artifact.
  Possible but a different project.

The file is ~2,400 lines but well-sectioned. Key landmarks (search inside the
file):

- `===================== STATE =====================` — state machine
- `===================== SCREENS / NAV =====================` — routing
- `===================== HINTS =====================` — hint card logic
- `===================== L3 TIMER + SCORE =====================` — timer/score
- `===================== BLOCK -> PYTHON DECODER =====================` — L2
- `<section class="screen" id="screen-level1"` — Level 1 markup
  (same pattern for level2, level3, bonus, express)

## Known limitations

- **Single-device state.** A student switching Chromebooks loses progress.
  Acceptable for a single 90-min lesson; not acceptable for a multi-day arc.
  If you need cross-device, you need a backend.
- **No teacher dashboard.** Failure logs live on each student's Chromebook
  only.
- **No accessibility audit yet.** Keyboard nav works (buttons are real
  buttons), but screen-reader testing hasn't happened. If you have students on
  AT, please run a pass before deploying.
- **No print stylesheet** beyond hiding chrome. Designed for screen.

## License / Attribution

Built for TETC (Teen Engineering + Tech Center). Uses the TETC design system
(teal/magenta/amber palette, EMprint→Calibri type, square corners, no
gradients). Brand mark is a text glyph placeholder — swap in the real
TE+TC monogram SVG (`logo-tetc-monogram-transparent.svg`) when you have it
in this folder.
