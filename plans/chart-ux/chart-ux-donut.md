---
schemaVersion: 1
id: chart-ux-donut
plan: chart-ux
parent: chart-ux
kind: task
type: Plan Task
timestamp: 2026-09-30T00:00:00Z
title: Donut/Pie — solid slices with gaps, centre label, hover highlight, legend with values, per-slice accessible summary
description: >-
  The category donut draws 3px outlined slices over a 10% fill with centre text that
  competes with the ring; it reads as unfinished.
approach: "how: tdd skill red→green on pie-chart.test.tsx via initialDimension (SF-1); consume chart-theme + chart-chrome"
status: todo
track: Wrappers
validation_tier: standard
blocked_by: [chart-ux-bar]
---
# Donut / Pie upgrade

Serialized after chart-ux-bar (shared `scripts.test` append).

In `components/ui/pie-chart.tsx` (API stays `category` singular + `index`):

- Delete the local `ChartDataItem` and `CustomTooltip`; import from `./chart-theme` /
  use `ChartTooltip`.
- Solid slices (no `fillOpacity`), separated by a 2px `var(--card)` stroke plus
  `cornerRadius` ~4 — the gap is background, not an outline.
- Centre label via Recharts `<Label position="center">` content: `centerText` (existing
  prop) as the caption in `var(--muted-foreground)`, plus new optional `centerValue?: string`
  rendered larger above it. Only when `innerRadius > 0`.
- Hover highlight: active slice via `activeIndex`/`activeShape` grows the outer radius by
  ~4px; others unchanged.
- Legend via `ChartLegend` WITH formatted values (name · value).
- `isAnimationActive={prefersReducedMotion ? false : undefined}` (NEVER an explicit `true`: Recharts 2.15.4 defaults to `!Global.isSsr`, and an explicit `true` renders 0 pie sectors under SSR and risks a hydration mismatch).
- Wrap in `ChartFrame`; label =
  `buildChartSummary({ title: ariaLabel, kind: "categorical", categories: [category], … })`
  (`ariaLabel` is a title prefix, never a replacement) — the singular
  `category` is adapted here, so every slice's `name: value` is in the label.
- `DonutChart`: switch `props.innerRadius || 40` / `props.outerRadius || "85%"` to `??`.
  Behaviour change only for an explicit `0`/`""`; the only call site passes `innerRadius={45}`
  (`Dashboard.tsx:203`) — verified at plan time.
- **Additive props only:** `centerValue?`, `ariaLabel?`, `initialDimension?`.

## Done-check

- **Seam:** `npx tsx --test components/ui/pie-chart.test.tsx` at seam
  `<DonutChart>/<PieChart data category index … initialDimension>` → SSR markup exits 0, red-first, asserting:
  - **default render** (no reduced motion — `usePrefersReducedMotion()` is `false` on the
    server) of a 5-row fixture shows exactly 5 `recharts-sector` elements with solid fills
    from the passed colours and no `fill-opacity="0.1"` — this fails if the wrapper passes an
    explicit `isAnimationActive={true}` (verified at plan time: explicit true → 0 sectors);
  - `centerText="Categories"` and `centerValue="5"` both appear in the markup; with
    `innerRadius={0}` on `PieChart` neither is rendered;
  - **legend scoped:** the substring of the element carrying `data-slot="chart-legend"`
    (inside `recharts-legend-wrapper`; Legend content renders under SSR — verified at plan
    time) contains each of the 5 names AND its `valueFormatter` output (e.g. `35%`) — so the
    assertion cannot pass from the aria-label alone;
  - the markup has `role="img"` and an `aria-label` that contains **all 5 names and their
    formatted values** (hand-written expected `name: value%` strings) — proving the singular
    `category` is adapted, not dropped;
  - rendered with `ariaLabel="Sales by Category"`, the `aria-label` contains
    `Sales by Category` AND still all 5 `name: value%` pairs (title prefix, not replacement).
- The Dashboard DonutChart call site compiles unchanged (`pnpm check-types` exits 0, no edit
  to `Dashboard.tsx` in this task).
- Test appended to `scripts.test` (serially); `pnpm test` and `pnpm build` exit 0.
- `grep -nE "fillOpacity=\{0\.1\}|#[0-9a-fA-F]{6}|interface ChartDataItem|innerRadius \|\|" components/ui/pie-chart.tsx` returns nothing.

## Log

## Decisions
