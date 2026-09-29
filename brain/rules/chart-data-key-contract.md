---
schemaVersion: 1
id: chart-data-key-contract
type: contract
domain: data-ops
title: Chart index/categories props are string keys into the data rows — a mismatch renders an empty chart
summary: Dashboard.tsx passes index="date"|"name"|"region"|"ageGroup" and categories like ["Sales","Profit","Expenses"] that must exactly match row keys in data/dashboard-data.ts; ChartDataItem is an index signature so TypeScript cannot catch drift
links: [static-dashboard-data-convention, chart-primitive-convention, add-dashboard-dataset]
failure: silent
anchor: none  # string-key coupling between chart props and data row keys; no single greppable token
cites:
  - components/Dashboard.tsx:143 :: data={salesData}
  - components/Dashboard.tsx:144 :: index="date"
  - components/Dashboard.tsx:145 :: categories={["Sales", "Profit", "Expenses"]}
  - components/Dashboard.tsx:170 :: index="name"
  - components/Dashboard.tsx:171 :: categories={["sales"]}
  - components/Dashboard.tsx:195 :: category="value"
  - components/Dashboard.tsx:223 :: index="region"
  - components/Dashboard.tsx:248 :: index="ageGroup"
  - components/Dashboard.tsx:249 :: categories={["spending"]}
  - data/dashboard-data.ts:5 :: Sales:
  - components/ui/area-chart.tsx:15 :: interface ChartDataItem
---
# Chart data-key contract

Each chart in `Dashboard.tsx` names its x-axis field (`index`) and series (`categories`, or
`category` for the donut) as **strings** that must equal keys in the corresponding rows of
`data/dashboard-data.ts`:

| chart | data | index | series |
|---|---|---|---|
| Sales Overview (AreaChart) | `salesData` | `date` | `Sales`, `Profit`, `Expenses` (capitalised) |
| Product Performance (BarChart) | `productData` | `name` | `sales` |
| Sales by Category (DonutChart) | `categoryData` | `name` | `value` |
| Regional Sales (BarChart) | `regionalData` | `region` | `sales` |
| Customer Demographics (BarChart) | `demographicsData` | `ageGroup` | `spending` |

**Why it's silent:** the chart wrappers type rows as `ChartDataItem { [key: string]: string | number }`,
so `categories={["sales"]}` against `salesData` (key `Sales`) type-checks and renders an
empty series. Recharts logs nothing. Renaming a data key must be mirrored here — and in
the agent context ([agent-context-contract](/brain/rules/agent-context-contract.md)),
where the key names are what the model reads.
