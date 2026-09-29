---
schemaVersion: 1
id: chart-primitive-convention
type: convention
domain: design-system
title: Charts are hand-rolled Recharts wrappers with a Tremor-style API in components/ui — @tremor/react is declared but never imported
summary: AreaChart, BarChart, PieChart/DonutChart share props (data, index, categories, colors, valueFormatter, show*), each redeclares ChartDataItem, uses hex palettes with fillOpacity 0.1 outlines, and must render inside a sized parent
links: [chart-data-key-contract, design-tokens-convention, add-dashboard-dataset, component-organization-convention]
anchor: ChartDataItem
absent: from "@tremor/react
cites:
  - components/ui/area-chart.tsx:15 :: interface ChartDataItem
  - components/ui/area-chart.tsx:20 :: ChartDataItem[]
  - components/ui/bar-chart.tsx:15 :: interface ChartDataItem
  - components/ui/bar-chart.tsx:20 :: ChartDataItem[]
  - components/ui/pie-chart.tsx:14 :: interface ChartDataItem
  - components/ui/pie-chart.tsx:51 :: ChartDataItem[]
  - components/ui/area-chart.tsx:33 :: export function AreaChart
  - components/ui/area-chart.tsx:115 :: fillOpacity={0.1}
  - components/ui/bar-chart.tsx:70 :: layout === "horizontal" ? "category" : "number"
  - components/ui/pie-chart.tsx:176 :: export function DonutChart
  - components/ui/pie-chart.tsx:180 :: props.innerRadius || 40
  - components/Dashboard.tsx:78 :: const colors = {
  - components/Dashboard.tsx:141 :: h-60
  - package.json:22 :: "@tremor/react"
---
# Chart primitive convention

`components/ui/{area,bar,pie}-chart.tsx` are local wrappers over **Recharts** exposing a
Tremor-like API: `data`, `index`, `categories` (or `category` for pie), `colors`,
`valueFormatter`, `showLegend/showGrid/showXAxis/showYAxis`, `yAxisWidth`.
`@tremor/react` is in `package.json` but **never imported** (declared `absent`) — do not
reach for Tremor components; extend these wrappers.

Shared idiom (copy it for a new chart type):
- `"use client"` + `ResponsiveContainer width/height 100%` — the **parent must have a
  height** (Dashboard wraps each in `<div className="h-60">`), or the chart renders 0px.
- Local `interface ChartDataItem { [key: string]: string | number }` — duplicated in all
  three files (anchor; all six sites cited). It is intentionally loose, which is why
  key drift is silent: [chart-data-key-contract](/brain/rules/chart-data-key-contract.md).
- Styling: outline look (`stroke` = colour, `fillOpacity={0.1}`), grey `#6b7280` ticks,
  `#e5e7eb` grid, white rounded tooltip. Colours are **hex arrays** chosen per chart in
  `Dashboard.tsx`'s `colors` map — the `--chart-1..5` CSS tokens in `globals.css` are
  NOT used by these charts ([design-tokens-convention](/brain/rules/design-tokens-convention.md)).

Gotchas:
- `BarChart layout="horizontal"` is Recharts' meaning — **vertical bars**, category
  x-axis. All three Dashboard bar charts pass it explicitly.
- `DonutChart` uses `props.innerRadius || 40` and `props.outerRadius || "85%"`, so an
  explicit `0` is overridden; use `PieChart` for a full pie.
