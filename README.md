# Car Travel Simulator

A seedable **Monte Carlo simulator of car round-trips** rendered as a phosphor "radar scope." Your home sits at the center; each trip is a randomly-shaped trace out and back; accidents are marked in red — and the app tallies **where accidents happen by distance from home**.

It's also a small lesson in **exposure bias**: raw accident counts pile up near home not because the roads there are riskier, but because every trip crosses the inner rings — twice. Toggle the chart to a per-road *rate* and the illusion flattens out.

**▶ Live demo:** `https://<your-username>.github.io/car-travel-simulator/`

<!-- Recommended: drop a screenshot at docs/screenshot.png and uncomment the line below.
     It doubles as the og:image for sharing the link on LinkedIn. -->
<!-- ![Car Travel Simulator](docs/screenshot.png) -->

---

## What it does

Every run simulates `n` round-trips from home:

1. A **destination distance** is drawn from a distribution you choose (the trip's "length").
2. A random **direction** and a **curvy, randomly-shaped path** out and back are generated.
3. With your chosen **per-trip probability**, an accident occurs at a uniformly-random point along the path.
4. The accident's distance from home decides which **ring** it lands in.
5. A live bar chart accumulates the per-ring statistics as the run animates.

Set the **seed** and any run is perfectly reproducible.

---

## Features

- **Seedable RNG** — deterministic, reproducible runs from a text seed.
- **Choice of distance distribution** — Uniform, Normal, Exponential, **Gamma**, Triangular, and Beta, with a live PDF preview of the shape you've configured.
- **Configurable everything** — number of trips, accident probability, number of distance rings, path curviness, and animation speed (plus an instant mode for large `n`).
- **Accumulating traces** — each trip animates brightly, then dims and stays on the scope until the next run.
- **Three chart metrics** — Absolute count, Relative share (%), and exposure-normalized Rate per unit of road.
- **Fully self-contained** — the published `index.html` makes **zero network requests**; it runs offline and on `file://`.

---

## Controls

| Control | What it does |
| --- | --- |
| **Seed** | Text seed for the RNG; same seed → identical run. |
| **Trips (n)** | How many round-trips to simulate. |
| **Accident prob / trip** | Bernoulli probability of one accident per trip. |
| **Distance distribution** | Shape the trip-length distribution is drawn from (with parameters + live preview). |
| **Distance rings (x)** | Number of equal-step concentric rings around home. |
| **Path curviness** | How much the random paths wander from a straight line. |
| **Speed / Instant** | Animation pace, or skip animation entirely. |
| **Run / Pause / Reset** | Start a fresh run, pause/resume, or clear the scope. |

### The three chart metrics

- **Absolute** — raw accident count per ring. Inner rings look dangerous; that's *exposure*, not risk.
- **Relative %** — each ring's share of all accidents (sums to 100%). Still driven by how much road sits in each ring.
- **Rate / road** — accidents per unit of road traversed in each ring (exposure-normalized). This should look roughly flat: per-trip risk in the model is uniform along the path. The gap between this and the Absolute bars is the whole point.

---

## Distributions

Trip-length is drawn on a normalized `[0, 1]` distance domain (`0` = home, `1` = scope edge), then clamped to `[0.05, 0.92]`.

- **Uniform** `(a, b)`
- **Normal** `(mean, std)`
- **Exponential** `(mean)` — density highest at home.
- **Gamma** `(shape k, scale)` — the "exponential that starts low." At `k = 1` it *is* the exponential; raise `k` above 1 to pull the density off the origin into a hump.
- **Triangular** `(min, mode, max)`
- **Beta** `(α, β)` — the bounded-domain shape-shifter (bell, skew, U-shaped).

> Note: Gamma, Normal, and Exponential are unbounded, so configurations with a high mean will clamp at the outer ring. The shape preview shows the *true* (unclamped) density — if its tail runs off the right edge, expect mass to pile up against the edge in the sim.

---

## Run it locally

No build, no dependencies, no server required:

```bash
# clone, then just open the file
open index.html        # macOS
# or double-click index.html, or drag it into a browser
```

Everything (React, the compiled app, and the CSS) is inlined into `index.html`.

---

## Deploy to GitHub Pages

1. Push `index.html` to the repository root.
2. **Settings → Pages → Build and deployment → Deploy from a branch.**
3. Branch: `main`, folder: `/ (root)`. Save.
4. Live in ~1 minute at `https://<your-username>.github.io/car-travel-simulator/`.

No base-path configuration is needed — nothing is loaded by relative URL.

---

## How it works

- **Rendering:** two stacked HTML `<canvas>` layers — a background layer for rings, home, and committed (dim) traces, and a foreground layer for the bright, animating current trip. Past traces accumulate without re-rendering the whole scene.
- **Paths:** each leg is a Catmull–Rom spline through randomly-perturbed waypoints, with an envelope that keeps the endpoints exact (home and destination).
- **Determinism:** a `xmur3` string hash seeds a `mulberry32` PRNG; all randomness (distances, directions, path shapes, accident occurrence and location) draws from that single stream, so a seed fully reproduces a run — animated or instant.
- **Chart:** a dependency-free inline SVG bar chart (no charting library), so it can't fail to size or render.

---

## Project structure

```
.
├── index.html        # Self-contained, published app (what GitHub Pages serves)
└── car-travel-sim.jsx# React source of truth — edit this, then rebuild index.html
```

`index.html` is a build artifact: the JSX is pre-compiled (classic React runtime), and React, ReactDOM, the app, and the exact Tailwind CSS are inlined. Edit `car-travel-sim.jsx` for changes.

---

## Built with

Plain **React** (no framework) on HTML Canvas + SVG, styled with **Tailwind CSS**. Compiled with Babel and inlined into a single static file.

---

## License

MIT — see [`LICENSE`](LICENSE). (Add an `MIT` license file via GitHub's "Add file → Create new file → LICENSE" if you haven't yet.)
