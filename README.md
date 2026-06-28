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
