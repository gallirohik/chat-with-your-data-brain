---
schemaVersion: 1
id: chart-ux-area
plan: chart-ux
parent: chart-ux
kind: task
type: Plan Task
timestamp: 2026-09-30T00:00:00Z
title: AreaChart — gradient fills, stronger stroke, crosshair cursor, themed tooltip and compact legend
description: >-
  The Sales Overview area chart is the screenshot's main complaint: 10%-opacity washes with
  hard outlines and a white bordered tooltip.
approach: "how: tdd skill red→green on area-chart.test.tsx via initialDimension (SF-1); consume chart-theme + chart-chrome"
status: done
track: Wrappers
validation_tier: standard
blocked_by: [chart-ux-theme-core, chart-ux-theme-chrome]
---
# AreaChart upgrade

In `components/ui/area-chart.tsx`:

- Delete the local `ChartDataItem`; import it from `./chart-theme`.
- Per series a `<linearGradient>` in `<defs>` with id `chartGradientId(useId(), category)`,
  top stop ~0.35 opacity → bottom ~0.02, `fill="url(#…)"`; stroke 2px in the series colour;
  `activeDot` r=4 with a `var(--card)` 2px ring; `dot={false}`. Gradients are always on
  (no toggle prop — no call site needs one).
- Tooltip → `content={<ChartTooltip valueFormatter=… />}`, `cursor` = 1px dashed vertical
  line on `CURSOR_STROKE`.
- Legend → `content={<ChartLegend />}`, top-right, compact.
- Grid/ticks from `AXIS_TICK` / `GRID_STROKE`; `isAnimationActive={prefersReducedMotion ? false : undefined}` (NEVER an explicit `true`: Recharts 2.15.4 defaults to `!Global.isSsr`, and an explicit `true` renders 0 pie sectors under SSR and risks a hydration mismatch).
- Wrap in `ChartFrame`; label = `buildChartSummary({ title: ariaLabel, kind: "series", … })` — `ariaLabel`
  is a title PREFIX, never a replacement, so the data content always survives.
- **Additive props only:** `ariaLabel?: string`,
  `initialDimension?: { width: number; height: number }` (passed to `ResponsiveContainer`).
  Every existing prop, name and default behaviour kept (`colors` still honoured; default
  palette becomes `CHART_PALETTE`).

## Done-check

- **Seam:** `npx tsx --test components/ui/area-chart.test.tsx` at seam
  `<AreaChart data index categories … initialDimension>` → SSR markup exits 0, red-first, asserting:
  - one `<linearGradient` per category, each id referenced by a `fill="url(#<id>)"`, ids
    unique within the markup;
  - with `colors={["#3b82f6"]}` the gradient stop / stroke use `#3b82f6` (backward compat);
    without `colors` they use `var(--chart-1)`;
  - the markup contains `role="img"` and an `aria-label` naming each category AND at least
    one hand-written formatted data value from the fixture (e.g. `$2,890`) — a label built
    from `categories.join()` alone fails;
  - rendered with `ariaLabel="Sales Overview"`, the `aria-label` contains `Sales Overview`
    AND still the category names and that formatted value (title prefix, not replacement);
  - rendered WITHOUT `initialDimension` the markup still contains the frame (no crash).
- The Dashboard call site (`components/Dashboard.tsx` AreaChart) compiles unchanged:
  `pnpm check-types` exits 0 with no edit to `Dashboard.tsx` in this task.
- Test appended to `scripts.test` (serially, after theme-chrome); `pnpm test` and `pnpm build` exit 0.
- `grep -nE "fillOpacity=\{0\.1\}|#[0-9a-fA-F]{6}|interface ChartDataItem" components/ui/area-chart.tsx` returns nothing.

## Log

- 2026-09-30 — done, prism PASS (standard). Red `90d0611` (6/6 fail on assertions vs the old `area-chart.tsx`) → green `1e2aa94`: `AreaChart` consumes `chart-theme` + `chart-chrome` — per-series `<linearGradient>` (opacity 0.35→0.02, id `chartGradientId(useId(), category)`, valid in `url(#…)` and hydration-stable), 2px stroke, `activeDot` r=4 with a `var(--card)` ring, dashed `CURSOR_STROKE` cursor, `ChartTooltip`/`ChartLegend`, `ChartFrame` with `buildChartSummary({title: ariaLabel, kind: "series"})`; additive props `ariaLabel?`, `initialDimension?`; `isAnimationActive={prefersReducedMotion ? false : undefined}`. `Dashboard.tsx` untouched and byte-identical to main. Prism mutation-tested 17 mutants (15 killed); tests tightened after (every gradient stop carries the series colour; title-prefix test uses a non-overlapping title and asserts `Series: Sales, Profit`). Unspecified but accepted: chart margin 10→(4/8/0/0) and XAxis `dy=10`→`tickMargin=8`. Residual (live pass in chart-ux-verify): hover ring/cursor, real rendered height inside `h-60`, visual effect of the margin change, and an explicit `isAnimationActive={true}` is not test-guarded for area (SF-1: Area renders the same under SSR either way).

## Decisions

- (prism round 1) `showGradient` prop dropped — YAGNI, no call site uses it.
