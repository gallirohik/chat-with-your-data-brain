---
id: turbopack-root-outside-repo
type: Improvement
schemaVersion: 1
priority: P2
category: ops
status: open
title: "next.config turbopack.root resolves three levels above the standalone repo"
summary: "turbopack.root is path.resolve(__dirname, ../../..) - the old monorepo root - so Turbopack roots itself outside this repo (for a checkout on Desktop that is the user's home dir)"
fix: "Set turbopack.root to __dirname (or delete the turbopack block) and run pnpm dev plus pnpm build once"
leverage: { impact: medium, effort: low }
blast_radius: [build-tooling]
cites:
  - next.config.ts:7 :: path.resolve(__dirname, "../../..")
found: 2026-09-30
description: "turbopack.root is path.resolve(__dirname, ../../..) - the old monorepo root - so Turbopack roots itself outside this repo (for a checkout on Desktop that is the user's home dir)"
tags: [ops, P2]
timestamp: 2026-09-30
---
# Turbopack root points outside the repo

Verified: `next.config.ts` sets `turbopack.root` three directories up — correct inside the
CopilotKit monorepo this example came from (`examples/v1/chat-with-your-data`), wrong since
commit c51f08e made it standalone. From a checkout at `~/Desktop/chat-with-your-data` the
root becomes the parent of the home directory tree.

Silent rot: builds still pass, but the root bounds file-watching and module resolution, so
Turbopack can resolve stray `node_modules` or lockfiles from outside the project and watch a
far larger tree than needed — the class of bug that shows up only on someone else's machine.
The brain already flags it as debt
([build-and-deps-convention](/brain/rules/build-and-deps-convention.md)). One-line fix.

<!-- okf:citations:start (generated — the frontmatter `cites:` DSL is the source of truth; do not hand-edit) -->

# Citations

[1] [next.config.ts:7](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/next.config.ts#L7) — `path.resolve(__dirname, "../../..")`

<!-- okf:citations:end -->
