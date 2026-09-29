---
id: unused-template-dependencies
type: Improvement
schemaVersion: 1
priority: P3
category: architecture
status: open
title: "Seven runtime dependencies are declared but imported nowhere"
summary: "@tremor/react, react-hook-form, @hookform/resolvers, four @radix-ui packages, date-fns, react-day-picker and class-variance-authority are template leftovers that no file imports"
fix: "pnpm remove the unused packages, then pnpm check-types, pnpm test and pnpm build"
leverage: { impact: low, effort: low }
blast_radius: [build-tooling, design-system]
cites:
  - package.json:22 :: "@tremor/react"
  - package.json:31 :: "react-hook-form"
  - package.json:16 :: "@hookform/resolvers"
  - package.json:17 :: "@radix-ui/react-label"
  - package.json:25 :: "date-fns"
  - package.json:29 :: "react-day-picker"
  - package.json:23 :: "class-variance-authority"
found: 2026-09-30
description: "@tremor/react, react-hook-form, @hookform/resolvers, four @radix-ui packages, date-fns, react-day-picker and class-variance-authority are template leftovers that no file imports"
tags: [architecture, P3]
timestamp: 2026-09-30
---
# Unused template dependencies

Verified by grep over every tracked `.ts`/`.tsx`/`.mjs`/`.css` file: no imports of
`@tremor/react`, `react-hook-form`, `@hookform/resolvers`, `@radix-ui/*`
(label, popover, select, slot), `date-fns`, `react-day-picker` or
`class-variance-authority`. The brain records them as absent and warns not to read them as
house libraries ([build-and-deps-convention](/brain/rules/build-and-deps-convention.md),
[chart-primitive-convention](/brain/rules/chart-primitive-convention.md)).

Cost: slower installs, a larger audit surface (each is one more thing the security profile
must track), and misleading signals to agents and humans picking a form/date/chart library.
None currently has an advisory. Keep `clsx`, `tailwind-merge`, `lucide-react`,
`tailwindcss-animate`, `zod`, `recharts` — all imported.

<!-- okf:citations:start (generated — the frontmatter `cites:` DSL is the source of truth; do not hand-edit) -->

# Citations

[1] [package.json:22](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/package.json#L22) — `"@tremor/react"`
[2] [package.json:31](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/package.json#L31) — `"react-hook-form"`
[3] [package.json:16](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/package.json#L16) — `"@hookform/resolvers"`
[4] [package.json:17](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/package.json#L17) — `"@radix-ui/react-label"`
[5] [package.json:25](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/package.json#L25) — `"date-fns"`
[6] [package.json:29](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/package.json#L29) — `"react-day-picker"`
[7] [package.json:23](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/package.json#L23) — `"class-variance-authority"`

<!-- okf:citations:end -->
