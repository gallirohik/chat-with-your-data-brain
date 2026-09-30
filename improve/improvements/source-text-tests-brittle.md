---
schemaVersion: 1
id: source-text-tests-brittle
priority: P3
category: architecture
status: open
title: "Accessibility test regex-matches exact indentation in app/page.tsx"
summary: "page-accessibility.test.mjs extracts the Suspense fallback with a regex that hard-codes six- and four-space indentation, so a formatter run breaks the test with no behaviour change"
fix: "Render the fallback (react-dom/server via tsx, like SearchResults.test.tsx) and assert on the markup instead of the source text"
leverage: { impact: low, effort: medium }
blast_radius: [testing, routing-app-shell]
cites:
  - page-accessibility.test.mjs:12 :: fallback=
  - readme-runtime.test.mjs:45 :: useRenderTool
---
# Source-text tests couple to formatting

Confidence: Worth exploring.

Verified: `page-accessibility.test.mjs` reads `app/page.tsx` as a string and captures the
fallback with `/fallback=\{([\s\S]*?)\n      \}\n    >/` — the seam is the file's
whitespace, not its behaviour. `readme-runtime.test.mjs` similarly regex-parses README code
fences ([source-text-tests-contract](/brain/rules/source-text-tests-contract.md)). The README
checks are reasonable doc-contract tests; the page test is the shallow one — its interface
(the regex) knows more about layout than about accessibility.

First slice: move only the Suspense-fallback assertion to a rendered check (extract the
fallback into a tiny component and render it in a `.test.tsx`, as
`SearchResults.test.tsx` already does). Leave the README tests alone.

<!-- okf:citations:start (generated — the frontmatter `cites:` DSL is the source of truth; do not hand-edit) -->

# Citations

[1] [page-accessibility.test.mjs:12](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/page-accessibility.test.mjs#L12) — `fallback=`
[2] [readme-runtime.test.mjs:45](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/readme-runtime.test.mjs#L45) — `useRenderTool`

<!-- okf:citations:end -->

