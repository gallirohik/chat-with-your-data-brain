---
schemaVersion: 1
id: agent-context-contract
type: contract
domain: agent-runtime
title: useAgentContext is the agent's only window onto the dashboard — two registrations, shape is the prompt
summary: Dashboard.tsx publishes all five datasets plus computed metrics and page.tsx publishes the current time; anything not passed here is invisible to the agent, and the tests pin where the time context lives
links: [chat-turn-flow, dashboard-render-flow, static-dashboard-data-convention, add-dashboard-dataset, source-text-tests-contract, security-posture]
failure: silent
anchor: useAgentContext
cites:
  - app/page.tsx:3 :: useAgentContext
  - app/page.tsx:24 :: useAgentContext({
  - app/page.tsx:25 :: description: "Current time"
  - components/Dashboard.tsx:10 :: useAgentContext
  - components/Dashboard.tsx:40 :: useAgentContext({
  - components/Dashboard.tsx:43 :: value: {
  - components/Dashboard.tsx:49 :: metrics: {
  - current-time.test.mjs:47 :: useAgentContext
  - current-time.test.mjs:56 :: useAgentContext
---
# Agent context contract

The server agent has no data access of its own — no DB, no data tool. **Everything it knows
about "your data" is what the browser publishes through `useAgentContext`** and sends with
each run. Two registrations exist (all code hits of the token cited):

1. `components/Dashboard.tsx:40` — description + `value` object holding `salesData`,
   `productData`, `categoryData`, `regionalData`, `demographicsData` and a `metrics` block
   of the six computed KPIs.
2. `app/page.tsx:24` — `"Current time"`, a `toLocaleTimeString()` string refreshed every
   second by `CurrentTimeContext`.

**What must stay in sync**
- Add a dataset to the dashboard but not to this `value` → the agent answers "I don't have
  that data" (silent). Follow [add-dashboard-dataset](/brain/playbooks/add-dashboard-dataset.md).
- The object keys and the `description` string are effectively prompt text; renaming
  `salesData` → `sales` changes what the model sees without any type error.
- Metric values are **pre-formatted strings** for rate/AOV/margin (`"12.3%"`, `"123.45"`)
  but numbers for totals — see [static-dashboard-data-convention](/brain/rules/static-dashboard-data-convention.md).

**Placement is test-pinned.** `current-time.test.mjs:34-57` regex-reads `app/page.tsx` and
asserts that `useAgentContext` for time lives in the null-rendering `CurrentTimeContext`
and NOT in `HomeContent` (so the 1 s tick doesn't re-render the dashboard). See
[source-text-tests-contract](/brain/rules/source-text-tests-contract.md).

Context values are client-controlled input to the model — see
[security-posture](/brain/playbooks/security-posture.md).
