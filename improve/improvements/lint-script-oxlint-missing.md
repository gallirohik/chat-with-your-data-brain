---
id: lint-script-oxlint-missing
type: Improvement
schemaVersion: 1
priority: P2
category: ops
status: open
title: "pnpm lint calls oxlint, which is not a dependency"
summary: "The lint script runs oxlint . but oxlint is absent from package.json and the lockfile, so pnpm lint fails on a clean clone"
fix: "Add oxlint as an exact-pinned devDependency (pnpm add -D oxlint), or change the script to a linter that is installed"
leverage: { impact: medium, effort: low }
blast_radius: [build-tooling]
cites:
  - package.json:11 :: oxlint .
found: 2026-09-30
description: "The lint script runs oxlint . but oxlint is absent from package.json and the lockfile, so pnpm lint fails on a clean clone"
tags: [ops, P2]
timestamp: 2026-09-30
---
# Lint script with no linter installed

Verified: `package.json` `lint` is `oxlint .`; no `oxlint` entry in `package.json` or
`pnpm-lock.yaml`, and no global binary on this machine — so `pnpm lint` errors with
command-not-found for anyone without a global install
([build-and-deps-convention](/brain/rules/build-and-deps-convention.md)). The loud failure
is the good news; the silent part is that there is effectively no lint gate at all, and
agents told to "run lint" will either fail or skip it. Two-minute fix.

<!-- okf:citations:start (generated — the frontmatter `cites:` DSL is the source of truth; do not hand-edit) -->

# Citations

[1] [package.json:11](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/package.json#L11) — `oxlint .`

<!-- okf:citations:end -->
