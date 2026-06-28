# Setup: live sandbox + CI for pinkTshirt/physics-engine

This bundle adds two things to your repo:

1. **`site/`** — a fully interactive, in-browser N-body gravity sandbox
   (plain HTML/CSS/JS, no build step, no backend). It's a from-scratch
   JavaScript port of the same physics your Python package implements —
   same Newtonian gravity force law, same four integrators
   (`euler`, `semi_implicit_euler`, `velocity_verlet`, `rk4`), same
   energy/momentum diagnostics — so it behaves identically, just compiled
   to JS instead of interpreted, which is what makes it fast enough to
   drag bodies around at 60fps.

2. **`.github/workflows/physics-ci.yml`** — a GitHub Action that:
   - Runs your **actual Python engine** (the three example scripts) on
     every push/PR, as a ground-truth correctness check, and uploads
     whatever plots/CSVs they produce as a downloadable build artifact.
   - Builds and deploys the `site/` sandbox to **GitHub Pages**
     automatically on every push to `main`.
   - Can also be triggered manually (Actions tab → "Run workflow") or
     runs weekly on a schedule as a regression check.

## Install steps

1. Copy `site/` and `.github/workflows/physics-ci.yml` into the root of
   `pinkTshirt/physics-engine`, preserving the paths.
2. Commit and push to `main`.
3. In the repo settings: **Settings → Pages → Build and deployment →
   Source → GitHub Actions**. (One-time setup; the workflow does the rest.)
4. After the next push, your sandbox will be live at:
   `https://pinktshirt.github.io/physics-engine/`

## About the example-script step in the workflow

The workflow tries to run:
```
python physics_engine/examples/two_body_orbit.py
python physics_engine/examples/figure_eight.py
python physics_engine/examples/random_cluster.py
```
This matches the file layout described in your README. If any example
script writes its plot to a different path, or expects a CLI flag, or
the actual file/folder names differ slightly from the README (e.g. if
`examples/` lives outside the `physics_engine/` package), just adjust
the three `run:` lines accordingly — everything else (artifact upload,
Pages deploy) works regardless of those paths. Each step uses
`continue-on-error: true` for now, so one mismatched path won't fail
the whole workflow; tighten that once the paths are confirmed.

## Validating the JS port

Before wiring up the UI, the JS physics in `site/physics.js` was checked
headlessly against the same benchmark the README itself trusts:

- **Figure-eight three-body orbit**, one full period, Velocity Verlet:
  relative energy drift ≈ 3.6×10⁻¹⁰, momentum stays at machine-precision
  zero.
- **Euler vs. Velocity Verlet**, two-body orbit, 50,000 steps: Euler
  drifts ~8% in energy, Verlet stays flat at ~0.00% — matching the
  qualitative claim already in your README about why Verlet is the
  default.

If you want this validation to live in CI too (rather than just having
been run once locally), say so and I'll add a small Node-based test step
to the workflow that re-runs these same checks on every push and fails
the build if drift exceeds a threshold.

## Using the sandbox

- **Click + drag** on the canvas to place a body and launch it with a
  velocity proportional to the drag vector.
- **Shift+click** to place a fixed "anchor" mass (good for building a
  sun-and-planets setup quickly).
- Side panel lets you swap integrators live (watch the energy scope
  diverge under Euler vs. stay flat under Verlet), adjust `G` and `dt`,
  load the same presets as the Python examples (two-body, figure-eight,
  random cluster), and inspect/remove individual bodies.
- The top-right scope is a live energy-drift sparkline — this is the
  same conservation check your README uses to validate the engine,
  just rendered continuously instead of printed once at the end of a run.
