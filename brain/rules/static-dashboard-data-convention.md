---
schemaVersion: 1
id: static-dashboard-data-convention
type: convention
domain: data-ops
title: All dashboard data is static in data/dashboard-data.ts; KPIs come from calculate* helpers with mixed return types
summary: Five exported arrays plus six calculate* helpers are the entire data layer — no fetching, no DB, no API; conversion rate is a hard-coded ratio; lib/user-info.ts is dead mock code
links: [chart-data-key-contract, agent-context-contract, add-dashboard-dataset, dashboard-render-flow]
absent: user-info
absent: fetch(
absent: prisma
cites:
  - data/dashboard-data.ts:2 :: export const salesData
  - data/dashboard-data.ts:90 :: export const productData
  - data/dashboard-data.ts:124 :: export const categoryData
  - data/dashboard-data.ts:153 :: export const regionalData
  - data/dashboard-data.ts:182 :: export const demographicsData
  - data/dashboard-data.ts:211 :: calculateTotalRevenue
  - data/dashboard-data.ts:225 :: 8.13
  - data/dashboard-data.ts:226 :: toFixed(1) + "%"
  - data/dashboard-data.ts:232 :: toFixed(2)
  - components/Dashboard.tsx:32 :: calculateTotalRevenue()
  - lib/user-info.ts:1 :: export const getUser
---
# Static dashboard data

"Your data" is a TypeScript module, `data/dashboard-data.ts`:

- **Datasets** (exported `const` arrays): `salesData` (monthly; keys `date`, `Sales`,
  `Profit`, `Expenses`, `Customers` — note the capitalised metric keys), `productData`
  (`name`, `sales`, `growth`, `units`), `categoryData` (`name`, `value` %, `growth`),
  `regionalData` (`region`, `sales`, `marketShare`), `demographicsData` (`ageGroup`,
  `percentage`, `spending`).
- **KPIs** — `calculateTotalRevenue/Profit/Customers` (numbers, reduce over `salesData`),
  `calculateConversionRate` and `calculateProfitMargin` (strings ending `%`),
  `calculateAverageOrderValue` (string via `toFixed(2)`). They are called once per render
  in `Dashboard.tsx` and passed to both the KPI tiles and the agent context.

**Gotchas**
- `calculateConversionRate` divides customers by `customers * 8.13` — it is a constant
  (~12.3%) regardless of data. Don't present it as derived.
- Mixed return types: any consumer doing math on rate/AOV/margin must parse strings.
- There is no fetching layer at all — `fetch(` and any ORM (`prisma`) appear nowhere in
  code (declared `absent`). Replacing the mock with real data means introducing a data
  source AND keeping the agent-context shape
  ([agent-context-contract](/brain/rules/agent-context-contract.md)).
- `lib/user-info.ts` (`getUser`, `getUsers`) is **imported by nothing** (`user-info`
  declared `absent` from code imports) — leftover demo code, not a pattern for user data.

How to add a dataset: [add-dashboard-dataset](/brain/playbooks/add-dashboard-dataset.md).
