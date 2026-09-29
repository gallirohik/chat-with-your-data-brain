---
id: postcss-sourcemap-cves
type: Improvement
schemaVersion: 1
priority: P1
category: security
status: open
title: "postcss 8.4.31 pinned by next has 2 high path-traversal advisories"
summary: "next@16.1.7 pins postcss@8.4.31, flagged for arbitrary file read via sourceMappingURL (2 high, 2 moderate); fixed in 8.5.23"
fix: "Add a pnpm override for next's postcss to >=8.5.23 (Tailwind already resolves postcss 8.5.28), run pnpm build, and re-audit"
leverage: { impact: low, effort: low }
blast_radius: [build-tooling]
cites:
  - pnpm-lock.yaml:7585 :: postcss: 8.4.31
  - pnpm-lock.yaml:3281 :: postcss@8.4.31
found: 2026-09-30
description: "next@16.1.7 pins postcss@8.4.31, flagged for arbitrary file read via sourceMappingURL (2 high, 2 moderate); fixed in 8.5.23"
tags: [security, P1]
timestamp: 2026-09-30
---
# postcss 8.4.31 (next's pinned copy)

Source: `rafa audit --json` dependency tier (osv-api). Mechanical map: high -> P1.

- **Package:** `postcss@8.4.31` — **transitive**, dev:false. Chain: `next@16.1.7 > postcss@8.4.31`.
- high GHSA-6g55-p6wh-862q / CVE-2026-45623 — arbitrary file read via attacker-controlled sourceMappingURL — fixedIn 8.5.12
- high GHSA-r28c-9q8g-f849 / CVE-2026-73646 — path traversal in previous source-map auto-loading — fixedIn 8.5.18
- moderate GHSA-fxqj-rqcc-2cmp / CVE-2026-69153 — incomplete fix of the above — fixedIn 8.5.23
- moderate GHSA-qx2v-qp2m-jg93 / CVE-2026-41305 — XSS via unescaped `</style>` in stringify — fixedIn 8.5.10

Reachability: build-time only — Next's copy processes the repo's own CSS
(`app/globals.css`); no user-supplied CSS is ever parsed. Low real exposure, but the
mechanical P1 stands (annotate, never downgrade). The lockfile already carries
`postcss@8.5.28` for Tailwind, so the override lands on a known-good version.

<!-- okf:citations:start (generated — the frontmatter `cites:` DSL is the source of truth; do not hand-edit) -->

# Citations

[1] [pnpm-lock.yaml:7585](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/pnpm-lock.yaml#L7585) — `postcss: 8.4.31`
[2] [pnpm-lock.yaml:3281](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/pnpm-lock.yaml#L3281) — `postcss@8.4.31`

<!-- okf:citations:end -->
