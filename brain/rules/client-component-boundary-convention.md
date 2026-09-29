---
schemaVersion: 1
id: client-component-boundary-convention
type: convention
domain: routing-app-shell
title: "use client" boundary — the only page is a client page; charts and CopilotKit consumers are client components
summary: app/page.tsx is a page-level client component; every file with hooks or Recharts carries "use client"; card.tsx stays directive-free; Header/Footer carry it without needing it
links: [copilotkit-provider-nesting-contract, dashboard-render-flow, component-organization-convention]
anchor: use client
absent: use server
cites:
  - app/page.tsx:1 :: "use client"
  - components/Dashboard.tsx:1 :: "use client"
  - components/Footer.tsx:1 :: "use client"
  - components/Header.tsx:1 :: "use client"
  - components/ui/area-chart.tsx:1 :: "use client"
  - components/ui/bar-chart.tsx:1 :: "use client"
  - components/ui/pie-chart.tsx:1 :: "use client"
  - components.json:4 :: "rsc": true
  - app/layout.tsx:23 :: RootLayout
---
# Client-component boundary

The app has one route (`/`, `app/page.tsx`) and it is a **page-level client component**
(`"use client"` at line 1). `app/layout.tsx` is the only server component in `app/`; it
renders the `<CopilotKit>` provider (itself a client component from the library).

Where the directive sits today (all 7 code hits of `"use client"`, markdown excluded):
`app/page.tsx`, `components/Dashboard.tsx`, `components/Header.tsx`,
`components/Footer.tsx`, and the three Recharts wrappers in `components/ui/`.

Rules for new code:
- A component that calls a CopilotKit hook (`useAgentContext`, `useRenderTool`, …),
  React state/effects, or renders Recharts MUST be a client component.
- Pure presentational primitives SHOULD stay directive-free so they remain usable from
  server components — `components/ui/card.tsx` is the model (no directive; shadcn
  `components.json` declares `"rsc": true`). `components/generative-ui/SearchResults.tsx`
  and `components/AssistantMessage.tsx` are also directive-free; they are only ever
  rendered from client parents.
- **Exception, not the model:** `Header.tsx` and `Footer.tsx` carry `"use client"` but use
  no hooks or browser APIs. Don't pattern-match them for new static components.
- There are no server actions (`"use server"` appears nowhere in code — declared `absent`,
  re-grepped every run). Server-side work goes through the CopilotKit runtime route;
  see [security-posture](/brain/playbooks/security-posture.md).
