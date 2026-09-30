---
schemaVersion: 1
id: no-ci-gate
priority: P2
category: ops
status: open
title: "No CI - tests, type-check and build never run on push or PR"
summary: "The repo has no .github workflows or other CI config; pnpm test, check-types and build only run when someone remembers to run them locally"
fix: "Add a minimal GitHub Actions workflow: pnpm install --frozen-lockfile, pnpm check-types, pnpm test, pnpm build"
leverage: { impact: high, effort: low }
blast_radius: [build-tooling, testing]
cites:
  - package.json:8 :: "test"
  - package.json:10 :: tsc --noEmit
---
# No CI gate

Verified: `git ls-files` shows no `.github/` directory and no other CI config. The repo has
real gates — strict `tsc --noEmit`, four node:test suites including README/source contracts
([source-text-tests-contract](/brain/rules/source-text-tests-contract.md)), and `next build`
— but nothing runs them on push or pull request, so a dependency bump (see the security rows)
or a README edit can merge broken. A ~20-line workflow makes every other row on this ledger
cheaper to fix safely. Pair it with fixing
[lint-script-oxlint-missing](lint-script-oxlint-missing.md) so lint can join the job.

<!-- okf:citations:start (generated — the frontmatter `cites:` DSL is the source of truth; do not hand-edit) -->

# Citations

[1] [package.json:8](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/package.json#L8) — `"test"`
[2] [package.json:10](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/package.json#L10) — `tsc --noEmit`

<!-- okf:citations:end -->

