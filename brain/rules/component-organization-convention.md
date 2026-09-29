---
schemaVersion: 1
id: component-organization-convention
type: convention
domain: components
title: components/ layout — PascalCase app components, kebab-case ui primitives, generative-ui renderers; named exports; relative imports
summary: App-level pieces (Dashboard, Header, Footer, AssistantMessage) are PascalCase files with named exports at components/; shadcn-style primitives live in components/ui (kebab-case); chat tool renderers in components/generative-ui
links: [client-component-boundary-convention, chart-primitive-convention, design-tokens-convention, tool-result-rendering-convention, dashboard-render-flow]
cites:
  - components/Dashboard.tsx:30 :: export function Dashboard
  - components/Header.tsx:3 :: export function Header
  - components/Footer.tsx:3 :: export function Footer
  - components/AssistantMessage.tsx:16 :: CustomAssistantMessageComponent as typeof CopilotChatAssistantMessage
  - components/generative-ui/SearchResults.tsx:46 :: export function SearchResults
  - components/ui/card.tsx:8 :: data-slot="card"
  - components/ui/card.tsx:3 :: import { cn } from "@/lib/utils"
  - components.json:16 :: "ui": "@/components/ui"
  - tsconfig.json:22 :: "@/*": ["./*"]
  - app/page.tsx:4 :: import { Dashboard } from "../components/Dashboard"
---
# Component organization

| folder | naming | contents | exemplar |
|---|---|---|---|
| `components/` | `PascalCase.tsx`, named export | app-level composition: `Dashboard`, `Header`, `Footer`, `CustomAssistantMessage` | `Dashboard.tsx` |
| `components/ui/` | `kebab-case.tsx` | primitives: shadcn `card` (data-slot, `cn`), Recharts wrappers | `card.tsx`, [chart-primitive-convention](/brain/rules/chart-primitive-convention.md) |
| `components/generative-ui/` | `PascalCase.tsx` + colocated `*.test.tsx` | chat renderers for agent tools | `SearchResults.tsx`, [tool-result-rendering-convention](/brain/rules/tool-result-rendering-convention.md) |

- **Exports:** named function exports everywhere (no default exports outside `app/`).
- **Imports:** mostly **relative** (`../components/Dashboard`, `./ui/card`,
  `../data/dashboard-data`). The `@/*` alias (tsconfig) is used only by shadcn-generated
  `card.tsx` (`@/lib/utils`). Either resolves; match the file you're editing.
- **Adding shadcn primitives:** `components.json` targets `@/components/ui` with
  `new-york` style — `shadcn add` output lands there and uses `cn`.
- **Overriding CopilotKit slots** — wrap the v2 component, spread props, add `className`,
  then cast back with `as typeof <LibComponent>` so the slot prop type-checks
  (AssistantMessage.tsx:16). Reuse this cast pattern for other `messageView` slots.

Client/server placement rules: [client-component-boundary-convention](/brain/rules/client-component-boundary-convention.md).
