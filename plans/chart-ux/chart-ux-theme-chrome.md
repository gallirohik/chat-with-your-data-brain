---
schemaVersion: 1
id: chart-ux-theme-chrome
plan: chart-ux
parent: chart-ux-theme
kind: subtask
type: Plan Subtask
timestamp: 2026-09-30T00:00:00Z
title: ChartTooltip, ChartLegend and ChartFrame — token-styled, accessible chart chrome
description: >-
  Replaces the three copies of white bordered tooltip contentStyle and the grey inline
  legend formatter; the frame gives every chart a text alternative so colour is never the
  only encoding.
approach: "how: tdd skill red→green; frontend-design skill for the visual pass, brain tokens win over its defaults"
status: done
track: Theme
validation_tier: standard
blocked_by: [chart-ux-theme-core]
---
# Chart chrome components

Create `components/ui/chart-chrome.tsx` (`"use client"` not required — render-only):

- `ChartTooltip` — a Recharts `Tooltip content` renderer (`active`, `payload`, `label`,
  plus `valueFormatter`, optional `indexFormatter`). Token classes: `bg-popover
  text-popover-foreground border rounded-lg shadow-md`, label in `text-muted-foreground`,
  one row per series: colour swatch (`background: <entry.color>`) + series name + formatted
  value (`tabular-nums`, right-aligned). Name AND value always present — colour is not the
  only encoding. Returns `null` when inactive/empty.
- `ChartLegend` — a Recharts `Legend content` renderer: swatch + name (+ optional formatted
  value, used by the donut), `text-muted-foreground text-xs`, wraps on narrow widths. Its
  root carries `data-slot="chart-legend"` (the `card.tsx` data-slot convention) so tests can
  scope assertions to the legend.
- `ChartFrame` — `<figure role="img" aria-label={label} className="h-full w-full">` wrapping
  the `ResponsiveContainer`; `label` is always `buildChartSummary(…)` output; a caller `ariaLabel` becomes its
  `title` prefix, never a replacement. Because `role="img"` makes children presentational, the label is the ONLY
  accessible content — it carries every row for categorical charts (see theme-core). Not a
  tab stop; Recharts `accessibilityLayer` stays off (see epic Decisions).
- If any Recharts focus outline is suppressed (clicked sector/bar), it is replaced by a
  `:focus-visible` ring on `--ring` — never a bare `outline: none`.

## Done-check

- **Seam:** `npx tsx --test components/ui/chart-chrome.test.tsx` at seam
  `<ChartTooltip> / <ChartLegend> / <ChartFrame> rendered markup` exits 0, red-first, asserting:
  - active tooltip with payload `[{ name: "Sales", value: 1200, color: "var(--chart-1)" }]`,
    label `"Jan 22"` and a `$`+`toLocaleString` formatter renders `Jan 22`, `Sales`, `$1,200`,
    a swatch styled `var(--chart-1)`, and the `bg-popover` class;
  - inactive tooltip renders the empty string;
  - legend root has `data-slot="chart-legend"` and renders every payload name; with values
    enabled it renders each formatted value;
  - `ChartFrame` renders `role="img"` and the given `aria-label` verbatim.
- The test file is appended to `scripts.test` (serially — this leaf runs after theme-core);
  `pnpm test`, `pnpm check-types`, `pnpm build` exit 0.
- `grep -nE "bg-white|gray-[0-9]|#[0-9a-fA-F]{6}" components/ui/chart-chrome.tsx` returns nothing.

## Log

- 2026-09-30 — done, prism PASS (standard tier, round 2). Red `c8c2bea` (module missing) → green `a3f923e`: `components/ui/chart-chrome.tsx` (`ChartTooltip`, `ChartLegend` with `data-slot="chart-legend"`, `ChartFrame` `<figure role="img">`), test appended to `scripts.test`; token classes only (`bg-popover`, `text-popover-foreground`, `text-muted-foreground`, `border` — `--popover` already mapped in `@theme inline`). Round 1 ITERATE: Recharts 2.15.4 Pie tooltip entries carry NO `color` (`Pie.js:531-537`; the Cell fill is merged onto `entry.payload.fill`, `Pie.js:447-449`), so the spec's own "background: <entry.color>" would have shown an invisible swatch on the donut. Fixed red-first (`0179c91` → `970cf96`): `entry.color ?? entry.payload?.fill`; `d35da84` pins that `color` wins over `payload.fill` (an Area/Bar data row can have a column named `fill`). ChartLegend values come from `entry.payload.value`, which only Pie legend entries carry (documented; intended for the donut). Prism mutation-tested 11 mutants, all killed. Surprise: the plan spec's wording was the root cause of I-1. Residual: pie tooltip colour proven against a hand-built payload + Recharts source, not a live hover — chart-ux-verify's browser pass covers it.

## Decisions
