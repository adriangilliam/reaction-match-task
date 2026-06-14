# Match Tap

A browser-based attention and reaction test. Colored shapes flash by one at a time; you tap
whenever a shape repeats the one right before it. It measures how fast you notice repeats and
how often you are fooled.

**Live:** https://adriangilliam.github.io/reaction-match-task/

It is a single static `index.html`. No build step, no dependencies, no framework. Works on
phone (tap) and desktop (click or spacebar).

## The task

Four possible stimuli: red or blue, square or circle. They appear one at a time. A shape can
repeat consecutively for an **indeterminate number of times** (a "run"). Your job:

- **Tap (or press Space) every time a shape matches the one immediately before it** (same color
  AND shape), and stop the moment it changes.
- Each repeat in a run is its own target. If a shape shows three times in a row, the 2nd and
  3rd both require a tap, and each missed one counts as a separate miss.

This is a continuous-performance task in the spirit of a 1-back, but the run lengths and the
timing are deliberately randomized so you cannot settle into a rhythm.

### Timing

Every trial is a short random flash followed by a random blank gap, both drawn fresh each time:

- **Flash (on-screen time):** random 100 to 250 ms
- **Gap (inter-stimulus interval):** random 200 to 5000 ms

The response window for a trial runs from its onset until the next stimulus appears, so you can
respond during the flash or the blank that follows.

### Settings

- **Length:** Short (30), Standard (60), or Long (100) trials.
- Timing is fixed (fast). There is no speed selector.

## Scoring and metrics

Each trial is classified independently:

| Outcome | Meaning |
|---|---|
| Hit | You tapped on a repeat |
| Miss (false negative) | A repeat went untapped |
| False tap (false positive) | You tapped on a non-repeat |
| Correct rejection | You correctly ignored a non-repeat |

Reported on the results screen:

- **Mean and median reaction time** (measured from stimulus onset, aligned to the paint frame)
- **Accuracy** = (hits + correct rejections) / total trials
- **Hit rate** and **false-alarm rate**
- **d′ (sensitivity)** from signal-detection theory, log-linear corrected for 0% / 100% edge
  cases. Computed as `z(hitRate) - z(falseAlarmRate)` using an inverse-normal approximation.
- A reaction-time-over-the-run chart (one point per correct tap, with the median marked)

## Data export

The results screen has an **Export CSV** button. One row per trial:

```
trial, color, shape, is_repeat, reaction_ms, flash_ms, isi_ms, outcome
```

`reaction_ms` is blank when there was no tap. Your best mean RT is also cached in the browser
(`localStorage`) and shown on the final intro screen.

## Screens

1. **Welcome** — a four-step intro (the promise, the rule with a live animated demo, the catch,
   and a Begin step with the Length selector).
2. **Play** — a minimal HUD (trial number, progress, live hit / miss / false-tap counts) over a
   full-screen stimulus stage.
3. **Results** — an animated dashboard: an accuracy ring around a count-up mean RT, a gradient
   verdict, the reaction-time chart, a signal-detection breakdown, and Play again / Export CSV.

## Run it locally

It is a static file, so anything that serves the directory works:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

Or just open `index.html` directly in a browser.

## Deployment

Hosted on GitHub Pages from the `main` branch root of
`github.com/adriangilliam/reaction-match-task`. Push to `main` and Pages rebuilds automatically
(usually under two minutes). See `CLAUDE.md` for the exact deploy and verification steps.
