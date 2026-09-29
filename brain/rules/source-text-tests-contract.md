---
schemaVersion: 1
id: source-text-tests-contract
type: contract
domain: testing
title: Three tests regex-read app/page.tsx, README.md and package.json as text — formatting and structure are asserted
summary: current-time, page-accessibility and readme-runtime tests readFileSync source/docs and match regexes on function bodies, indentation, badges and code snippets; refactoring page.tsx or editing README prose can fail pnpm test
links: [test-runner-convention, agent-context-contract, dashboard-render-flow, build-and-deps-convention, search-internet-tool-contract]
failure: loud
anchor: readFileSync
cites:
  - current-time.test.mjs:2 :: readFileSync
  - current-time.test.mjs:6 :: readFileSync
  - current-time.test.mjs:36 :: function CurrentTimeContext
  - current-time.test.mjs:55 :: homeContentSource
  - page-accessibility.test.mjs:2 :: readFileSync
  - page-accessibility.test.mjs:5 :: readFileSync
  - page-accessibility.test.mjs:12 :: fallback=
  - readme-runtime.test.mjs:2 :: readFileSync
  - readme-runtime.test.mjs:8 :: readFileSync
  - readme-runtime.test.mjs:11 :: readFileSync
  - readme-runtime.test.mjs:26 :: Built%20with-Next
  - readme-runtime.test.mjs:34 :: - pnpm$
  - readme-runtime.test.mjs:45 :: useRenderTool
  - app/page.tsx:11 :: function CurrentTimeContext()
  - app/page.tsx:32 :: function HomeContent()
---
# Source-text tests

Three of the four test files do not render anything — they `readFileSync` a file and
assert **regexes over its text** (all code hits of `readFileSync` cited):

**`current-time.test.mjs` → `app/page.tsx`**
- extracts `function CurrentTimeContext() { … \n}` and `function HomeContent() { … \n}`
  by regex — the functions must keep those exact declarations and end with `}` at column 0.
- asserts `CurrentTimeContext` contains `startCurrentTimeUpdates`, `useAgentContext`,
  `return null`; `HomeContent` contains `<CurrentTimeContext />`, `<Dashboard />`,
  `<CopilotSidebar` and **no** `useState|useEffect|useAgentContext|startCurrentTimeUpdates`.
  This pins the re-render isolation ([dashboard-render-flow](/brain/playbooks/dashboard-render-flow.md)).

**`page-accessibility.test.mjs` → `app/page.tsx`**
- matches `fallback={ … \n      }\n    >` — the Suspense fallback's **indentation** is part of
  the assertion — then requires `role="status"`, `aria-live="polite"`, `sr-only` "Loading
  dashboard" and `aria-hidden="true"`.

**`readme-runtime.test.mjs` → `README.md` + `package.json` + installed `next`**
- the README Next.js badge major must equal the pinned `next` major; README must state the
  Node minimum from `next/package.json` engines; `- pnpm` must be listed; no npm/yarn
  install instructions; no `openCopilot`; the README `useRenderTool` snippet must pass
  `result={result}` to `SearchResults`.

**Consequences:** reformatting `page.tsx` (prettier width change, renaming the components,
converting to arrow functions) or editing README examples fails `pnpm test` (loud). Bumping
`next` a major requires editing the README badge and Node line in the same change
([build-and-deps-convention](/brain/rules/build-and-deps-convention.md)).
