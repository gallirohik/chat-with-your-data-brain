---
schemaVersion: 1
id: chart-ux-theme-core
plan: chart-ux
parent: chart-ux-theme
kind: subtask
type: Plan Subtask
timestamp: 2026-09-30T00:00:00Z
title: chart-theme module + palette retune — token palette, shared ChartDataItem, gradient ids, per-row chart summary, reduced motion
description: >-
  The shared type and palette are consumed by all three wrappers and sit under the
  chart-data-key contract's surface. The current :root chart tokens fail contrast
  (measured vs white --card: 3.59 / 3.66 / 9.12 / 1.72 / 2.15) and --chart-4/--chart-5 are
  near-identical ambers, so the retune is required, not optional.
approach: "how: tdd skill (.agents/skills/tdd) red→green on chart-theme.test.ts; the palette gate is an in-test oklch→WCAG contrast + OKLab distance check parsed from app/globals.css"
status: todo
track: Theme
validation_tier: full
priority: 2
---
# chart-theme module + palette retune

Create `components/ui/chart-theme.ts` (kebab-case per
[component-organization-convention](/brain/rules/component-organization-convention.md)) exporting:

- `export interface ChartDataItem { [key: string]: string | number }` — the ONE declaration;
  the wrappers import it (their local copies are deleted in their own tasks). Shape stays
  identical (index signature); tightening it is out of scope and would break
  [chart-data-key-contract](/brain/rules/chart-data-key-contract.md) call sites.
- `CHART_PALETTE` written as five **literal** strings: `"var(--chart-1)"` … `"var(--chart-5)"`
  — the `:root` vars, always defined, independent of Tailwind's source scan. **No template-
  literal construction** (`` `var(--chart-${n})` `` is forbidden: it hides the names from the
  scanner and from grep).
- `seriesColor(colors: string[] | undefined, i: number)` — caller colours win (backward
  compat: Dashboard still passes hex until chart-ux-dashboard-cards), else palette, modulo length.
- Exported style constants on tokens: `AXIS_TICK = { fill: "var(--muted-foreground)", fontSize: 12 }`,
  `GRID_STROKE = "var(--border)"`, `CURSOR_STROKE = "var(--border)"` (literal strings).
- `chartGradientId(reactId: string, key: string)` — deterministic and **injective**: every
  character outside `[A-Za-z0-9]` is ENCODED (e.g. `_` + hex code point + `_`), never
  stripped, so `"Sales A"` and `"Sales_A"` cannot collide; output matches `[A-Za-z0-9_-]+`.
- `buildChartSummary({ title?, data, index, categories, valueFormatter, kind })` —
  `kind: "series" | "categorical"`. The chart frame is `role="img"`, which makes the legend,
  tooltip and ticks presentational to assistive tech, so this string IS the accessible
  content:
  - `categorical` (bar, donut — pie passes its singular `category` as `categories: [category]`)
    or any chart with ≤ 12 rows: lists **every row** as `<index value>: <formatted value>`
    (per series when several), plus the series names;
  - `series` with > 12 rows: series names, index range (first → last), formatted min and max
    per series.
- `usePrefersReducedMotion()` — returns `false` on server and first client render, reads
  `matchMedia("(prefers-reduced-motion: reduce)")` in `useEffect` and subscribes to change.

**Palette retune (required).** Replace `:root` `--chart-1..5` in `app/globals.css`. Verified
starting point (computed at plan time with the same oklch→sRGB→WCAG math the test uses; all
in sRGB gamut): `oklch(0.55 0.2 260)` blue 5.02:1 · `oklch(0.58 0.1 185)` teal 4.07:1 ·
`oklch(0.5 0.2 305)` violet 6.66:1 · `oklch(0.62 0.15 55)` orange 3.83:1 ·
`oklch(0.57 0.2 15)` rose 4.96:1; minimum pairwise OKLab distance 0.138 (current palette:
0.075). The builder may tune within the thresholds. The `.dark` block is NOT touched.

## Done-check

- **Seam:** `npx tsx --test components/ui/chart-theme.test.ts` at seam
  `chart-theme exports (CHART_PALETTE, seriesColor, chartGradientId, buildChartSummary, AXIS_TICK, GRID_STROKE, usePrefersReducedMotion) + the :root --chart-* tokens`
  exits 0, written red-first, and asserts at least:
  - **Palette gate:** the test reads `app/globals.css`, isolates the FIRST `:root { … }`
    block (the `.dark` block redeclares `--card` and `--chart-*` and must not be read), and
    **asserts it parsed exactly 5 `--chart-N: oklch(L C H)` values (N = 1..5) and one
    `--card` value** — a regex that matches nothing fails here rather than passing vacuously.
    It converts oklch → OKLab → linear sRGB and **asserts each chart colour is in the sRGB
    gamut** (every linear channel within [−1e-4, 1+1e-4]) BEFORE any clamping; the conversion
    clips only after that assertion, for the luminance step. It then computes WCAG relative
    luminance and asserts each chart colour is **≥ 3:1** against `--card` and every pair is
    **≥ 0.10 apart in OKLab (ΔE_ok)**. Red-first proof: run against the
    current palette it FAILS (contrast 1.72 / 2.15 on `--chart-4`/`--chart-5`, min ΔE_ok 0.075)
    — the red output goes in the Log.
  - `seriesColor(undefined, 6) === CHART_PALETTE[1]`; `seriesColor(["#123456"], 3) === "#123456"`;
    `CHART_PALETTE` deep-equals `["var(--chart-1)", …, "var(--chart-5)"]`.
  - `AXIS_TICK` deep-equals `{ fill: "var(--muted-foreground)", fontSize: 12 }` and
    `GRID_STROKE === "var(--border)"`.
  - `chartGradientId(":r1:", "Sales")` matches `/^[A-Za-z0-9_-]+$/`; differs from
    `(":r1:", "Profit")`, from `(":r2:", "Sales")`, and `chartGradientId(":r1:", "Sales A")`
    differs from `chartGradientId(":r1:", "Sales_A")`; equal across repeated calls.
  - `buildChartSummary` `categorical` over a 5-row fixture (names + `%` formatter) contains
    all 5 `name: value%` pairs (hand-written expected strings);
  - the same call with `title: "Sales by Category"` returns a string that **starts with**
    `Sales by Category` AND still contains all 5 `name: value%` pairs (title is a prefix,
    never a replacement); `series` over a 13-row
    fixture contains the first and last index values and the hand-computed formatted min and max.
  - `renderToStaticMarkup` of a probe component that prints `String(usePrefersReducedMotion())`
    yields `false`.
- `grep -nE "#[0-9a-fA-F]{6}" components/ui/chart-theme.ts` returns nothing (no hex);
  `grep -n 'var(--chart-\${' components/ui/chart-theme.ts` returns nothing (no template-literal
  var construction); `grep -c '"var(--chart-[1-5])"' components/ui/chart-theme.ts` prints ≥ 5.
- The `.dark { … }` block is byte-identical to `main`: `git diff -U0 main -- app/globals.css`
  shows only `:root` `--chart-1..5` lines changed.
- The test file is appended to the `tsx --test` list in `package.json` `scripts.test`, and
  `pnpm test`, `pnpm check-types`, `pnpm build` exit 0.

## Log

## Decisions

- (prism round 1, atlas applied) Palette retune is required; the gate is an in-repo test,
  not an external validator (none is installed).
