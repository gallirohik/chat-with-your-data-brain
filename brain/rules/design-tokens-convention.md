---
schemaVersion: 1
id: design-tokens-convention
type: convention
domain: design-system
title: Tailwind v4 CSS-first theme with shadcn new-york/stone tokens in globals.css — app chrome still uses raw gray utilities
summary: No tailwind.config; @import tailwindcss + @theme inline maps oklch CSS vars to utilities; cn() merges classes; card.tsx is token-based while Dashboard/Header/Footer hard-code gray-*; the .dark variant exists but nothing toggles it
links: [chart-primitive-convention, copilotkit-css-override-contract, component-organization-convention]
absent: next-themes
cites:
  - app/globals.css:1 :: @import "tailwindcss"
  - app/globals.css:3 :: @plugin "tailwindcss-animate"
  - app/globals.css:5 :: @custom-variant dark
  - app/globals.css:12 :: :root {
  - app/globals.css:32 :: --chart-1
  - app/globals.css:48 :: .dark {
  - app/globals.css:109 :: @theme inline {
  - app/globals.css:148 :: @layer base {
  - components.json:3 :: "style": "new-york"
  - components.json:7 :: "config": ""
  - components.json:9 :: "baseColor": "stone"
  - lib/utils.ts:5 :: export function cn
  - components/ui/card.tsx:10 :: bg-card text-card-foreground
  - components/Dashboard.tsx:91 :: bg-white p-3 rounded-lg border border-gray-100
  - postcss.config.mjs:2 :: @tailwindcss/postcss
---
# Design tokens convention

- **Tailwind v4, CSS-first.** `app/globals.css` starts with `@import "tailwindcss"`;
  PostCSS uses `@tailwindcss/postcss`; there is no `tailwind.config.*` (shadcn
  `components.json` has `"config": ""`). Theme extension goes in `globals.css`
  (`@theme` / `@theme inline`), never a JS config.
- **Tokens.** shadcn `new-york` style, `stone` base: `:root` and `.dark` define oklch vars
  (`--background`, `--primary`, `--muted-foreground`, `--chart-1..5`, `--sidebar-*`,
  `--radius`), mapped to utilities in `@theme inline` (`bg-card`, `text-muted-foreground`,
  `rounded-lg` …). `@layer base` applies `border-border` and `bg-background`.
- **Class merging** — `cn()` in `lib/utils.ts` (clsx + tailwind-merge); primitives accept
  `className` and merge through it (see `components/ui/card.tsx`).

**Exceptions, not the model:** `Dashboard.tsx` KPI tiles, `Header.tsx`, `Footer.tsx` and
`app/page.tsx` hard-code `bg-white`, `border-gray-*`, `text-gray-*`; chart wrappers
hard-code hex. They predate the tokens. New UI SHOULD use the token utilities
(`bg-card`, `text-muted-foreground`, `border`) so dark mode and re-theming work.

**Dark mode is inert.** `@custom-variant dark (&:is(.dark *))` and the `.dark` token block
exist, and components use `dark:` classes, but no code adds a `dark` class and no theme
library is installed (`next-themes` declared `absent`). Adding a toggle is new work.

CopilotKit's own UI is themed separately:
[copilotkit-css-override-contract](/brain/rules/copilotkit-css-override-contract.md).
