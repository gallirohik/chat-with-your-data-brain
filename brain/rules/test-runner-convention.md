---
schemaVersion: 1
id: test-runner-convention
type: convention
domain: testing
title: Tests run on node:test (+ tsx for TSX) from an explicit file list in package.json — new test files must be added to the script
summary: pnpm test = node --test on three root .mjs files then tsx --test on SearchResults.test.tsx; no jest/vitest/e2e; components are tested with react-dom/server renderToStaticMarkup
links: [source-text-tests-contract, tool-result-rendering-convention, build-and-deps-convention]
absent: jest
cites:
  - package.json:8 :: node --test current-time.test.mjs page-accessibility.test.mjs readme-runtime.test.mjs && tsx --test components/generative-ui/SearchResults.test.tsx
  - package.json:44 :: "tsx"
  - components/generative-ui/SearchResults.test.tsx:1 :: node:assert/strict
  - components/generative-ui/SearchResults.test.tsx:3 :: renderToStaticMarkup
  - current-time.test.mjs:12 :: mock.timers.enable
  - lib/current-time.mjs:7 :: export function startCurrentTimeUpdates
---
# Test runner convention

- **Runner:** Node's built-in `node:test` + `node:assert/strict`. `.mjs` tests run with
  `node --test`; the one TSX test runs with `tsx --test`. No jest (declared `absent`), no
  vitest in the project's own deps, no Playwright/e2e.
- **The file list is explicit** in `package.json` `scripts.test` — there is no glob.
  **A new test file that is not added to that script never runs** (and CI/`pnpm test`
  stays green). Add it to the `node --test …` list (`.mjs`) or the `tsx --test …` list
  (`.ts/.tsx`).
- **Placement:** logic/source tests sit at the repo root (`*.test.mjs`); component tests
  are colocated (`components/generative-ui/SearchResults.test.tsx`).
- **Component tests** render with `react-dom/server`'s `renderToStaticMarkup` and regex
  the markup — so the component under test must be renderable without a browser (no
  hooks needing a provider). Keep renderers pure like `SearchResults`
  ([tool-result-rendering-convention](/brain/rules/tool-result-rendering-convention.md)).
- **Time/logic seams are extracted to plain `.mjs`** (`lib/current-time.mjs`) so they can
  be tested with `mock.timers` without React. Follow that pattern for new hook logic.
- Several tests assert on source text, not behaviour:
  [source-text-tests-contract](/brain/rules/source-text-tests-contract.md).

Other gates: `pnpm check-types` (`tsc --noEmit`) and `pnpm lint` — see
[build-and-deps-convention](/brain/rules/build-and-deps-convention.md).
