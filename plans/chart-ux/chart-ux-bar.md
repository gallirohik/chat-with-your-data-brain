---
schemaVersion: 1
id: chart-ux-bar
plan: chart-ux
parent: chart-ux
kind: task
type: Plan Task
timestamp: 2026-09-30T00:00:00Z
title: BarChart — solid rounded bars, hover emphasis, responsive width, per-row accessible summary
description: >-
  Three of five dashboard charts are bars rendered as 30px outlines with a 10% fill; they
  read as placeholders.
approach: "how: tdd skill red→green on bar-chart.test.tsx via initialDimension (SF-1); consume chart-theme + chart-chrome"
status: done
track: Wrappers
validation_tier: standard
blocked_by: [chart-ux-area]
---
# BarChart upgrade

Serialized after chart-ux-area (both append to `package.json` `scripts.test`); transitively
after the theme subtasks.

In `components/ui/bar-chart.tsx`:

- Delete the local `ChartDataItem`; import from `./chart-theme`.
- Solid fill in the series colour, no stroke; corner radius on the value end only —
  `[6,6,0,0]` for `layout="horizontal"` (Recharts' meaning: VERTICAL bars, category x-axis —
  see [chart-primitive-convention](/brain/rules/chart-primitive-convention.md) gotcha) and
  `[0,6,6,0]` for `layout="vertical"`.
- Replace fixed `barSize` 30/20 with `maxBarSize` (~48) so bars scale with the card.
- Hover emphasis: track the active index (`onMouseMove`/`onMouseLeave`), non-active bars
  drop to ~0.55 opacity; tooltip `cursor` = `var(--muted)` band.
- Tooltip/legend via `ChartTooltip`/`ChartLegend`; `AXIS_TICK`/`GRID_STROKE`;
  `isAnimationActive={prefersReducedMotion ? false : undefined}` (NEVER an explicit `true`: Recharts 2.15.4 defaults to `!Global.isSsr`, and an explicit `true` renders 0 pie sectors under SSR and risks a hydration mismatch).
- Wrap in `ChartFrame`; label =
  `buildChartSummary({ title: ariaLabel, kind: "categorical", … })` (`ariaLabel` is a title
  prefix, never a replacement) — every bar's `label: value` is in it.
- **Additive props only:** `ariaLabel?`, `initialDimension?`. Existing props/defaults kept.
  (No `showValues` — no call site needs it.)

## Done-check

- **Seam:** `npx tsx --test components/ui/bar-chart.test.tsx` at seam
  `<BarChart data index categories layout … initialDimension>` → SSR markup exits 0, red-first, asserting:
  - a 3-row, 1-category fixture renders exactly 3 matches of `recharts-bar-rectangle"`
    (closing quote included, so the `recharts-bar-rectangles` group class does not count)
    whose fill is the passed colour, and NO `fill-opacity="0.1"` / `stroke-width="2"` on them;
  - without `colors` the fill is `var(--chart-1)`;
  - **rounded value end only:** in the default `layout="horizontal"` each
    `recharts-bar-rectangle` path `d` contains exactly TWO `A 6,6` arc commands (value end
    rounded, base square — a fully rounded bar would have four, an unrounded one zero and
    uses `h`/`v`; shapes verified at plan time); `layout="vertical"` also renders (category
    y-axis) with exactly two `A 6,6` arcs per rectangle;
  - the markup has `role="img"` and an `aria-label` that contains every row's index value
    AND its formatted value (e.g. `North: $1,200` for each of the 3 rows — hand-written
    expected strings);
  - rendered with `ariaLabel="Regional Sales"`, the `aria-label` contains `Regional Sales`
    AND still every `name: value` pair (title prefix, not replacement).
- The three Dashboard BarChart call sites compile unchanged (`pnpm check-types` exits 0,
  no edit to `Dashboard.tsx` in this task).
- Test appended to `scripts.test` (serially); `pnpm test` and `pnpm build` exit 0.
- `grep -nE "fillOpacity=\{0\.1\}|barSize=|#[0-9a-fA-F]{6}|interface ChartDataItem" components/ui/bar-chart.tsx` returns nothing.

## Log

- 2026-09-30 — done, prism PASS (standard). Red `c5c681e` (6/6 fail on assertions vs the old `bar-chart.tsx`) → green `9edbae0`: solid `seriesColor` fills (no stroke, no 10% opacity), radius `[6,6,0,0]` horizontal / `[0,6,6,0]` vertical (exactly two `A 6,6` arcs per bar), `maxBarSize` 48 replaces `barSize`, hover emphasis via `onMouseMove` active index → per-bar `<Cell fillOpacity>` (1 / 0.55; Cells keep the series fill), `ChartTooltip`/`ChartLegend`/`ChartFrame`, `buildChartSummary(kind: "categorical")` so every `name: value` is in the aria-label, `isAnimationActive={prefersReducedMotion ? false : undefined}`. Additive props only (`ariaLabel?`, `initialDimension?`); `Dashboard.tsx` untouched. Beyond spec (accepted): the `layout="vertical"` axes now swap correctly (the old code put `dataKey=index` on XAxis even then) — no Dashboard call site uses vertical. Prism read Recharts 2.15.4 source for the hover path (no stale dim, no render loop) and mutation-tested (9 killed; survivors were things the Done-check does not require). Post-verdict: `d`-commit drops Recharts' default `#ccc` stroke on the hover band (`stroke: "none"`) and pins `maxBarSize`. Residual (live pass in chart-ux-verify): real hover, vertical-layout corner placement (implementation verified by probe, not test-guarded), negative values in vertical layout (no call site).

## Decisions

- (prism round 1) `showValues` prop dropped — YAGNI, no call site uses it.
