# CLAUDE.md — Match Tap

Single-file browser game that measures reaction time and errors on a consecutive-repeat
attention task. See `README.md` for the player-facing description. This file is for working
in the repo.

## Ground rules

- **Everything lives in `index.html`.** One file: HTML, CSS in a `<style>`, and vanilla JS in
  one IIFE at the bottom. No build step, no framework, no dependencies, no bundler. Keep it
  that way unless explicitly asked to change it.
- **No em dashes** in any copy, comments, or commit messages (Adrian's standing rule). Use
  commas, colons, parentheses, or separate sentences.
- Plain, modern browser JS (ES2020+). It targets mobile Safari/Chrome and desktop, so keep it
  touch-first (`pointerdown`, large tap targets, `touch-action: manipulation`).

## How the code is organized (`index.html`)

- Three `<section>` screens toggled by an `active` class: `#start` (4-step welcome), `#play`
  (HUD + stimulus stage), `#results` (animated dashboard).
- The JS IIFE holds it all. Key pieces:
  - `TIMING` constant: `{stimMin:100, stimMax:250, isiMin:200, isiMax:5000}` (ms). `cfg` merges
    this with `{trials}`. There is no speed selector; timing is fixed.
  - `buildSequence(trials)`: builds a stream of **runs**. `P_SINGLE` (0.5) is the chance a run
    is a one-off; `P_EXTEND` (0.45) extends a repeating run by one more, giving indeterminate
    run lengths. A trial is a target iff it equals the immediately previous stimulus.
  - `nextTrial()` / `endTrial()` / `respond()`: the trial loop. Each trial picks a random flash
    duration and a random ISI per trial. RT is `performance.now()` minus the onset, where onset
    is set on a double `requestAnimationFrame` to align with paint. Spacebar works only while
    `running`.
  - `finish()`: computes metrics (mean/median RT, accuracy, hit/false-alarm rates, d′ via
    `normInv`), then populates and animates the results dashboard.
  - Results helpers: `animateCount` (eased rAF count-up), `verdictFor` (label from mean+acc),
    `buildRTChart` (inline SVG, line draws in via `getTotalLength` + `stroke-dashoffset`).
  - Welcome controller: `showStep` + dots + Back/Continue; `startDemo`/`stopDemo` run the
    animated rule demo on step 2; the last step's button calls `startGame()`.
- CSV export columns: `trial, color, shape, is_repeat, reaction_ms, flash_ms, isi_ms, outcome`.
- Personal best is cached in `localStorage` under `matchtap_best`.

## Deploy (GitHub Pages)

Repo: `github.com/adriangilliam/reaction-match-task`, served from `main` branch root.
`gh` is authenticated as `adriangilliam`. Standard loop:

```bash
cd /Users/adriangilliam/Dev/reaction-match-task
git -c commit.gpgsign=false commit -aqm "message"   # see signing note below
git push -q origin main
```

Pages rebuilds automatically (usually under 2 minutes). Pages was already enabled; you do not
need to re-enable it. If you ever do, it is:
`echo '{"source":{"branch":"main","path":"/"}}' | gh api --method POST repos/adriangilliam/reaction-match-task/pages --input -`

### Commit signing gotcha

Adrian's global git config turns on SSH commit signing with a passphrase-locked key, which makes
plain `git commit` fail in this non-interactive environment. Commit with
`git -c commit.gpgsign=false ...` (local override only; it does not touch his global config).

### Confirm a deploy actually went live

The URL returns 200 even on the old cached version, so poll for new *content*, not status. Use a
cache-buster and grep for a string unique to your change:

```bash
URL="https://adriangilliam.github.io/reaction-match-task/index.html"
until curl -s "$URL?cb=$(date +%s%N)" | grep -q 'YOUR_UNIQUE_STRING'; do sleep 8; done
```

## Verifying changes

- Always `node --check` the JS before pushing (extract the `<script>` body) and grep for stale
  references after refactors.
- Visual checks use the Chrome MCP tools: navigate with a cache-busting query (`?v=N`), resize to
  ~402x848 to confirm mobile, screenshot.
- To exercise the **results** screen without a slow manual run, inject a driver with the
  `javascript_tool`: click `#nextBtn` to advance the intro, then `dispatchEvent` synthetic
  `pointerdown`s on `#stage` at intervals. A full 60-trial run takes ~85s because gaps go up to
  5s, so wait in the background, then screenshot. (Random auto-taps produce lots of false alarms
  and a negative d′; that is expected, not a bug.)

## Conventions

- Match the existing terse style: compact CSS, short helper functions, comments only where the
  intent is not obvious.
- Keep the dark theme tokens in `:root` (`--bg/--panel/--ink/--muted/--good/--bad/--accent/--line`).
- The task model and tuning knobs are `TIMING`, `P_SINGLE`, `P_EXTEND`. Change those to retune
  difficulty rather than scattering magic numbers.
