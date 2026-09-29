---
id: test-script-explicit-file-list
type: Improvement
schemaVersion: 1
priority: P2
category: ops
status: open
title: "The test script names each test file - a new test file silently never runs"
summary: "pnpm test lists four files explicitly; any new *.test.* file is skipped with no error, so coverage can erode unnoticed"
fix: "Switch to discovery - node --test with a glob for *.test.mjs and tsx --test for *.test.tsx - so new files are picked up automatically"
leverage: { impact: medium, effort: low }
blast_radius: [testing]
cites:
  - package.json:8 :: node --test current-time.test.mjs page-accessibility.test.mjs readme-runtime.test.mjs
found: 2026-09-30
description: "pnpm test lists four files explicitly; any new *.test.* file is skipped with no error, so coverage can erode unnoticed"
tags: [ops, P2]
timestamp: 2026-09-30
---
# Explicit test file list

Verified: the `test` script enumerates `current-time.test.mjs`,
`page-accessibility.test.mjs`, `readme-runtime.test.mjs` and
`components/generative-ui/SearchResults.test.tsx`. The brain documents this as a
convention to remember ([test-runner-convention](/brain/rules/test-runner-convention.md)) —
which is exactly the problem: correctness depends on memory. A forgotten entry produces a
green run that never executed the new test, the textbook silent rot. Discovery via glob
(`node --test "**/*.test.mjs"`, `tsx --test "**/*.test.tsx"`) removes the trap; update the
test-runner rule when it lands.

<!-- okf:citations:start (generated — the frontmatter `cites:` DSL is the source of truth; do not hand-edit) -->

# Citations

[1] [package.json:8](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/package.json#L8) — `node --test current-time.test.mjs page-accessibility.test.mjs readme-runtime.test.mjs`

<!-- okf:citations:end -->
