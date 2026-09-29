---
id: next-16-1-7-critical-cves
type: Improvement
schemaVersion: 1
priority: P0
category: security
status: open
title: "next 16.1.7 carries 2 critical and 12 high advisories - bump to 16.3.3"
summary: "rafa audit (osv-api + pnpm audit) flags 25 advisories on the direct dep next@16.1.7, incl. 2 critical unauthenticated RCEs; all fixed by 16.3.3"
fix: "Bump next to 16.3.3 (exact pin, same major so the README badge test still holds); run pnpm install, pnpm test, pnpm build"
leverage: { impact: high, effort: low }
blast_radius: [build-tooling, routing-app-shell, api]
cites:
  - package.json:27 :: "next": "16.1.7"
  - pnpm-lock.yaml:3127 :: next@16.1.7
found: 2026-09-30
description: "rafa audit (osv-api + pnpm audit) flags 25 advisories on the direct dep next@16.1.7, incl. 2 critical unauthenticated RCEs; all fixed by 16.3.3"
tags: [security, P0]
timestamp: 2026-09-30
---
# next 16.1.7 carries critical advisories

Source: `rafa audit --json` (rafa.audit/v1, dependency tier ran: osv-api+pnpm-audit over
pnpm-lock.yaml, 2026-09-30). Priority is the mechanical map (critical -> P0); not downgraded.

**Package:** `next@16.1.7` — **direct**, runtime (dev:false). Chain: `next@16.1.7`.
**Fixed in:** `16.3.3` covers every advisory below (highest fixedIn among them).

| severity | advisory | fixedIn |
|---|---|---|
| critical | GHSA-2xp9-vwfh-vxw4 — unauthenticated RCE in Image Optimization API (AVIF via sharp/libheif) | 16.3.3 |
| critical | GHSA-p293-qw3h-jr36 / CVE-2026-75604 — unauthenticated RCE on Windows-hosted servers | 16.3.3 |
| high | 12 advisories — middleware/proxy bypasses (GHSA-267c-6grr-h53f, -26hh-7cqf-hhc6, -36qx-fr4f-26g5, -492v-c6pp-mqqv, -6gpp-xcg3-4w24), SSRF (GHSA-89xv-2m56-2m9x, -c4j6-fc7j-m34r, -p9j2-gv94-2wf4), DoS (GHSA-8h8q-6873-q5fj, -m99w-x7hq-7vfj, -mg66-mrh9-m8jx, -q4gf-8mx6-v5v3) | 16.2.3–16.2.11 |
| moderate | 9 advisories — cache confusion/poisoning, XSS with CSP nonces / beforeInteractive, image-optimizer DoS, server-function endpoint disclosure | 16.2.5–16.2.11 |
| low | 2 advisories — GHSA-3g8h-86w9-wvmq, GHSA-vfv6-92ff-j949 (cache poisoning) | 16.2.5 |

Full refs, aliases and details live in `.rafa/improve/security-audit.json`.

Reachability: server-exposed — `next` is the whole server (routing-app-shell + the
`app/api/copilotkit` route, see [security-posture](/brain/playbooks/security-posture.md)).
Observational notes (priority-neutral): the app uses no `next/image` and has no `public/`
dir, and nothing here uses middleware, i18n, rewrites or Server Actions, so several highs
are likely not reachable in this app's shape; the default `/_next/image` endpoint still
ships with `next start`. Annotation only — the P0 stands.

**Fix path:** same major, so the README badge/Node test
([source-text-tests-contract](/brain/rules/source-text-tests-contract.md)) keeps passing;
the build convention says Next moves as an exact pin
([build-and-deps-convention](/brain/rules/build-and-deps-convention.md)). A 10-minute fix:
edit the pin, `pnpm install`, `pnpm test && pnpm build`, smoke the chat turn.

<!-- okf:citations:start (generated — the frontmatter `cites:` DSL is the source of truth; do not hand-edit) -->

# Citations

[1] [package.json:27](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/package.json#L27) — `"next": "16.1.7"`
[2] [pnpm-lock.yaml:3127](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/pnpm-lock.yaml#L3127) — `next@16.1.7`

<!-- okf:citations:end -->
