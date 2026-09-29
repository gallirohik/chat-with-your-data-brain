---
id: hardcoded-conversion-rate-kpi
type: Improvement
schemaVersion: 1
priority: P2
category: product
status: open
title: "Conversion Rate KPI is a hard-coded constant presented as derived data"
summary: "calculateConversionRate divides customers by customers*8.13, so it always shows about 12.3% - and that fabricated figure is shown as a KPI tile and fed to the agent as a metric"
fix: "Either add a visitors series to the dataset and compute it, or relabel the tile and agent-context field as an assumed rate (or drop it)"
leverage: { impact: medium, effort: low }
blast_radius: [data-ops, agent-runtime]
cites:
  - data/dashboard-data.ts:225 :: 8.13
  - components/Dashboard.tsx:53 :: conversionRate
  - components/Dashboard.tsx:110 :: Conversion Rate
found: 2026-09-30
description: "calculateConversionRate divides customers by customers*8.13, so it always shows about 12.3% - and that fabricated figure is shown as a KPI tile and fed to the agent as a metric"
tags: [product, P2]
timestamp: 2026-09-30
---
# Hard-coded conversion rate

Verified: `visitors = totalCustomers * 8.13`, then `customers / visitors` — the data cancels
out and the result is always `12.3%`
([static-dashboard-data-convention](/brain/rules/static-dashboard-data-convention.md)).

Why it matters in a "chat with your data" app: the value is passed into `useAgentContext`
under `metrics`, so the agent will confidently analyse, compare and explain a number that no
data produced — and if someone swaps in real sales data, this KPI will still read 12.3%.
It is the one KPI that silently lies. Ten-minute fix: rename it to make the assumption
explicit, or add a `Visitors` column and compute it (follow
[add-dashboard-dataset](/brain/playbooks/add-dashboard-dataset.md) so the charts and the
agent both see it).

<!-- okf:citations:start (generated — the frontmatter `cites:` DSL is the source of truth; do not hand-edit) -->

# Citations

[1] [data/dashboard-data.ts:225](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/data/dashboard-data.ts#L225) — `8.13`
[2] [components/Dashboard.tsx:53](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/components/Dashboard.tsx#L53) — `conversionRate`
[3] [components/Dashboard.tsx:110](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/components/Dashboard.tsx#L110) — `Conversion Rate`

<!-- okf:citations:end -->
