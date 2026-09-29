---
schemaVersion: 1
id: copilotkit-provider-nesting-contract
type: contract
domain: routing-app-shell
title: Every CopilotKit hook and component must render beneath the root CopilotKit provider
summary: app/layout.tsx wraps all children in <CopilotKit runtimeUrl=...>; useAgentContext, useRenderTool and CopilotSidebar only work inside that subtree
links: [runtime-endpoint-path-contract, agent-context-contract, search-internet-tool-contract, dashboard-render-flow, client-component-boundary-convention]
failure: silent
anchor: none  # composition/ordering — provider nesting does not grep as one token
cites:
  - app/layout.tsx:3 :: CopilotKit
  - app/layout.tsx:33 :: <CopilotKit
  - app/layout.tsx:34 :: runtimeUrl
  - app/layout.tsx:36 :: useSingleEndpoint={false}
  - app/layout.tsx:38 :: {children}
  - app/page.tsx:41 :: <CopilotSidebar
  - components/Dashboard.tsx:40 :: useAgentContext({
  - components/Dashboard.tsx:61 :: useRenderTool({
---
# CopilotKit provider nesting

The single `<CopilotKit>` provider lives in the **root layout** (`app/layout.tsx`), wrapping
`{children}` inside `<body>`. It owns the connection to the runtime
([runtime-endpoint-path-contract](/brain/rules/runtime-endpoint-path-contract.md)) and the
registries that `useAgentContext`, `useRenderTool` and `<CopilotSidebar>` write into.

**Invariant:** any component that calls a CopilotKit v2 hook, or renders a CopilotKit UI
component, must be a descendant of that provider. Today that is `app/page.tsx`
(`CopilotSidebar`, the current-time `useAgentContext`) and `components/Dashboard.tsx`
(`useAgentContext` for dashboard data, `useRenderTool` for `searchInternet`).

**What breaks, and how:** a new route group with its own layout that does not inherit
the root layout, a portal rendered outside `<body>`, or moving the provider down into a
page, detaches those hooks from the registry. Depending on the CopilotKit version this
either throws a missing-context error or — worse — the agent simply never sees the
context / the tool renderer never fires. Treat it as silent.

The provider props are also load-bearing: `useSingleEndpoint={false}` selects the
multi-route transport that the optional catch-all route folder serves; see
[runtime-endpoint-path-contract](/brain/rules/runtime-endpoint-path-contract.md).
The stylesheet import order in the same file is its own contract:
[copilotkit-css-override-contract](/brain/rules/copilotkit-css-override-contract.md).
