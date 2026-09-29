---
schemaVersion: 1
id: dashboard-render-flow
type: flow
domain: components
title: Page render — root layout, Suspense, the 1 s current-time child isolated from the Dashboard, KPIs and five chart cards
summary: RootLayout (fonts, CopilotKit) → Home (Suspense with accessible fallback) → HomeContent renders CurrentTimeContext (null, ticks every second), Header, Dashboard, Footer, CopilotSidebar; Dashboard computes metrics, publishes context, registers the tool renderer and lays out cards
links: [copilotkit-provider-nesting-contract, client-component-boundary-convention, agent-context-contract, static-dashboard-data-convention, chart-data-key-contract, chart-primitive-convention, source-text-tests-contract, component-organization-convention]
cites:
  - app/layout.tsx:7 :: Geist
  - app/layout.tsx:33 :: <CopilotKit
  - app/page.tsx:57 :: <Suspense
  - app/page.tsx:61 :: role="status"
  - app/page.tsx:11 :: function CurrentTimeContext()
  - lib/current-time.mjs:8 :: setInterval(onUpdate, 1_000)
  - app/page.tsx:29 :: return null
  - app/page.tsx:35 :: <CurrentTimeContext />
  - app/page.tsx:38 :: <Dashboard />
  - components/Dashboard.tsx:32 :: calculateTotalRevenue()
  - components/Dashboard.tsx:61 :: useRenderTool({
  - components/Dashboard.tsx:87 :: grid gap-4 grid-cols-1 md:grid-cols-2 lg:grid-cols-4
  - components/Dashboard.tsx:131 :: <Card
---
# Dashboard render flow

1. **`app/layout.tsx`** (server) — loads Geist/Geist Mono as CSS vars, imports CopilotKit v2
   CSS then `globals.css`, wraps the tree in `<CopilotKit>`.
2. **`app/page.tsx` `Home`** (client) — `<Suspense>` with a fallback that is
   screen-reader-announced (`role="status"`, `aria-live`, `sr-only` text) — pinned by
   `page-accessibility.test.mjs` ([source-text-tests-contract](/brain/rules/source-text-tests-contract.md)).
3. **`HomeContent`** renders, in order: `CurrentTimeContext`, `Header`, `<main>` →
   `Dashboard`, `Footer`, `CopilotSidebar`.
4. **`CurrentTimeContext`** holds the clock state, ticks via `startCurrentTimeUpdates`
   (`setInterval` 1000 ms, cleanup returned to `useEffect`), publishes it with
   `useAgentContext`, and **returns `null`**. This isolation is deliberate: a state
   update every second in `HomeContent` would re-render the whole Dashboard and all
   charts. The test forbids moving that state back up.
5. **`Dashboard`** — calls the six `calculate*` helpers each render, publishes datasets +
   metrics via `useAgentContext`, registers the `searchInternet` renderer via
   `useRenderTool`, then lays out a responsive grid (1 / 2 / 4 cols): six KPI tiles, then
   five `Card`s each holding a `h-60` chart (Area, Bar ×3, Donut)
   ([chart-data-key-contract](/brain/rules/chart-data-key-contract.md),
   [chart-primitive-convention](/brain/rules/chart-primitive-convention.md)).

**Where to change what:** layout/chrome → `page.tsx`/`Header`/`Footer`; a new panel →
`Dashboard.tsx` ([add-dashboard-dataset](/brain/playbooks/add-dashboard-dataset.md));
anything ticking frequently → its own null-rendering child like `CurrentTimeContext`.
`Dashboard.tsx` is a 260-line monolith; splitting it is safe as long as the hooks stay
under the provider.
