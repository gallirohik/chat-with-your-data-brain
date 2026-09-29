---
id: sharp-libvips-libheif-cves
type: Improvement
schemaVersion: 1
priority: P1
category: security
status: open
title: "sharp 0.34.5 (via next) has 2 high advisories in libvips and libheif"
summary: "Transitive sharp@0.34.5 under next carries GHSA-f88m-g3jw-g9cj and GHSA-rgj7-g3m4-5g8c; fixed in 0.35.4"
fix: "After the next bump, re-run rafa audit; if sharp is still below 0.35.4 add a pnpm override sharp >=0.35.4 and re-run pnpm build"
leverage: { impact: medium, effort: low }
blast_radius: [build-tooling, routing-app-shell]
cites:
  - pnpm-lock.yaml:7599 :: sharp: 0.34.5
  - pnpm-lock.yaml:3577 :: sharp@0.34.5
found: 2026-09-30
description: "Transitive sharp@0.34.5 under next carries GHSA-f88m-g3jw-g9cj and GHSA-rgj7-g3m4-5g8c; fixed in 0.35.4"
tags: [security, P1]
timestamp: 2026-09-30
---
# sharp 0.34.5 high advisories

Source: `rafa audit --json` dependency tier (osv-api). Mechanical map: high -> P1.

- **Package:** `sharp@0.34.5` — **transitive**, runtime (dev:false). Chain: `next@16.1.7 > sharp@0.34.5`.
- GHSA-f88m-g3jw-g9cj — inherited libvips vulnerabilities — fixedIn `0.35.0`.
- GHSA-rgj7-g3m4-5g8c — libheif vulnerabilities (GHSA-g89c-p67h-r497, GHSA-2jg2-4ch7-h545) — fixedIn `0.35.4`.

Reachability: server-side, only through Next's image optimizer (`/_next/image`); the app
uses no `next/image` and ships no `public/` images — annotation only, P1 stands. Shares the
root cause of the critical AVIF advisory in
[next-16-1-7-critical-cves](next-16-1-7-critical-cves.md): do that bump first, then
re-audit — this row may close with it.

<!-- okf:citations:start (generated — the frontmatter `cites:` DSL is the source of truth; do not hand-edit) -->

# Citations

[1] [pnpm-lock.yaml:7599](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/pnpm-lock.yaml#L7599) — `sharp: 0.34.5`
[2] [pnpm-lock.yaml:3577](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/pnpm-lock.yaml#L3577) — `sharp@0.34.5`

<!-- okf:citations:end -->
