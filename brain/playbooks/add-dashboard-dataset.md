---
schemaVersion: 1
id: add-dashboard-dataset
type: how-to
domain: data-ops
title: How to add a dataset or KPI to the dashboard so both the charts and the agent see it
summary: Export the array (or calculate* helper) from data/dashboard-data.ts, import it in Dashboard.tsx, add it to the useAgentContext value, render a Card with a chart whose index/categories match the row keys, and pick a colour palette
links: [static-dashboard-data-convention, chart-data-key-contract, agent-context-contract, chart-primitive-convention, dashboard-render-flow]
cites:
  - data/dashboard-data.ts:182 :: export const demographicsData
  - data/dashboard-data.ts:235 :: export const calculateProfitMargin
  - components/Dashboard.tsx:16 :: import {
  - components/Dashboard.tsx:43 :: value: {
  - components/Dashboard.tsx:78 :: const colors = {
  - components/Dashboard.tsx:235 :: <Card className="col-span-1 md:col-span-1 lg:col-span-2">
  - components/Dashboard.tsx:246 :: <BarChart
---
# Add a dataset / KPI

1. **Data** — add `export const fooData = [ { <indexKey>: ..., <seriesKey>: number }, ... ]`
   to `data/dashboard-data.ts` (or a `calculateFoo` helper for a KPI; decide number vs
   pre-formatted string deliberately — existing helpers mix both,
   [static-dashboard-data-convention](/brain/rules/static-dashboard-data-convention.md)).
2. **Import** it in `components/Dashboard.tsx`'s data import block.
3. **Agent context** — add it to the `useAgentContext` `value` object (and extend
   `description` if the agent should know what it is). Skipping this is the classic silent
   miss: the chart shows it, the assistant can't
   ([agent-context-contract](/brain/rules/agent-context-contract.md)).
4. **Palette** — add an entry to the `colors` map (hex strings; the charts ignore the
   `--chart-*` CSS vars).
5. **Card** — copy an existing `<Card className="col-span-1 md:col-span-1 lg:col-span-2">`
   block: `CardHeader/CardTitle/CardDescription`, then `CardContent` → `<div
   className="h-60">` → chart. Set `index`/`categories` to the exact row keys
   ([chart-data-key-contract](/brain/rules/chart-data-key-contract.md)); pick the
   wrapper per [chart-primitive-convention](/brain/rules/chart-primitive-convention.md).
6. **KPI tile** — for a KPI, add a tile to the `grid-cols-2 sm:grid-cols-3 lg:grid-cols-6`
   row (six today; a seventh wraps) and add it under `metrics` in the context.

Verify: `pnpm check-types`, `pnpm dev`, ask the sidebar about the new data.
