---
id: dark-mode-inert
type: Improvement
schemaVersion: 1
priority: P3
category: product
status: open
title: "Dark-mode tokens and dark: classes ship but nothing ever enables dark mode"
summary: "globals.css defines a .dark token block and a dark custom variant, and components carry dark: classes, but no code adds the dark class and no theme library is installed"
fix: "Decide: wire a toggle (or prefers-color-scheme) that sets the dark class, or delete the .dark block and dark: classes"
leverage: { impact: low, effort: medium }
blast_radius: [design-system]
cites:
  - app/globals.css:48 :: .dark {
  - app/globals.css:5 :: @custom-variant dark
  - components/generative-ui/SearchResults.tsx:55 :: dark:bg-gray-800
found: 2026-09-30
description: "globals.css defines a .dark token block and a dark custom variant, and components carry dark: classes, but no code adds the dark class and no theme library is installed"
tags: [product, P3]
timestamp: 2026-09-30
---
# Inert dark mode

Verified: `@custom-variant dark (&:is(.dark *))` plus a full `.dark` token block exist, and
`SearchResults.tsx` / `AssistantMessage.tsx` use `dark:` utilities, but no code adds a `dark`
class and `next-themes` is absent
([design-tokens-convention](/brain/rules/design-tokens-convention.md)). Meanwhile the app
chrome hard-codes `bg-white` / `gray-*`, so even a toggle would produce a half-dark page.
Dead styling that invites the wrong assumption; low urgency. If a toggle is wanted, the
first slice is migrating the KPI tiles to token utilities (`bg-card`,
`text-muted-foreground`).

<!-- okf:citations:start (generated — the frontmatter `cites:` DSL is the source of truth; do not hand-edit) -->

# Citations

[1] [app/globals.css:48](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/app/globals.css#L48) — `.dark {`
[2] [app/globals.css:5](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/app/globals.css#L5) — `@custom-variant dark`
[3] [components/generative-ui/SearchResults.tsx:55](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/components/generative-ui/SearchResults.tsx#L55) — `dark:bg-gray-800`

<!-- okf:citations:end -->
