# Match Tap — Reaction Task

A continuous-performance / 1-back reaction task. Blue/red squares and circles flash one
at a time. **Tap (or press Space) only when a stimulus repeats** the one immediately before
it (same color *and* shape).

Measures:
- **Reaction time** on correct taps (mean, median, range)
- **False negatives** — missed repeats
- **False positives** — taps on non-repeats
- **d′** sensitivity (signal-detection, log-linear corrected)

Single static `index.html`, no build step, no dependencies. Per-trial data exports to CSV.
Works on phone (tap) and desktop (click / spacebar).
