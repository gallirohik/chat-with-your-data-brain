---
schemaVersion: 1
open: 15
debt_score: 39
by_priority: { P0: 1, P1: 4, P2: 5, P3: 5 }
type: "Improvement Ledger"
title: "Improvement ledger"
description: "15 open · debt score 39 · P0 1 · P1 4 · P2 5 · P3 5"
timestamp: 2026-09-29T23:18:32.012Z
---
# Improvement ledger

First improve pass, 2026-09-30, on branch `chore/improve-ledger` against brain sha `31a001b`
(19 rules, 6 playbooks). No prior rows, so there is no trend yet: this is the baseline.

**Debt score** = weighted open rows (P0 x8 · P1 x4 · P2 x2 · P3 x1) = 8 + 16 + 10 + 5 = **39**.

## Trend

| pass | date | open | debt score | change |
|---|---|---|---|---|
| 1 (baseline) | 2026-09-30 | 15 | 39 | — |

## Start here (leverage-ranked)

| # | id | P | category | impact / effort | why now |
|---|---|---|---|---|---|
| 1 | [next-16-1-7-critical-cves](improvements/next-16-1-7-critical-cves.md) | P0 | security | high / low | 2 critical RCEs + 12 highs; one exact-pin bump in the same major |
| 2 | [copilotkit-endpoint-unauthenticated](improvements/copilotkit-endpoint-unauthenticated.md) | P1 | security | high / medium | anyone can spend your LLM + Tavily credit; start with a spend cap today |
| 3 | [no-ci-gate](improvements/no-ci-gate.md) | P2 | ops | high / low | makes every dependency bump here safe to land |
| 4 | [sharp-libvips-libheif-cves](improvements/sharp-libvips-libheif-cves.md) | P1 | security | medium / low | re-audit after the next bump; may close with it |
| 5 | [postcss-sourcemap-cves](improvements/postcss-sourcemap-cves.md) | P1 | security | low / low | build-time only; one pnpm override |
| 6 | [undici-transitive-cves](improvements/undici-transitive-cves.md) | P1 | security | medium / medium | prefer a CopilotKit bump over a major-version override |
| 7 | [turbopack-root-outside-repo](improvements/turbopack-root-outside-repo.md) | P2 | ops | medium / low | one-line fix |
| 8 | [lint-script-oxlint-missing](improvements/lint-script-oxlint-missing.md) | P2 | ops | medium / low | `pnpm lint` fails on a clean clone |
| 9 | [test-script-explicit-file-list](improvements/test-script-explicit-file-list.md) | P2 | ops | medium / low | new tests silently never run |
| 10 | [hardcoded-conversion-rate-kpi](improvements/hardcoded-conversion-rate-kpi.md) | P2 | product | medium / low | the agent analyses a made-up number |
| 11 | [unused-template-dependencies](improvements/unused-template-dependencies.md) | P3 | architecture | low / low | seven template leftovers |
| 12 | [dead-user-info-module](improvements/dead-user-info-module.md) | P3 | architecture | low / low | pure deletion |
| 13 | [readme-preview-lfs-pointer](improvements/readme-preview-lfs-pointer.md) | P3 | product | low / low | README hero image is broken |
| 14 | [source-text-tests-brittle](improvements/source-text-tests-brittle.md) | P3 | architecture | low / medium | formatter-fragile test |
| 15 | [dark-mode-inert](improvements/dark-mode-inert.md) | P3 | product | low / medium | dead styling; decide wire or delete |

## Counts

| by priority | open |
|---|---|
| P0 | 1 |
| P1 | 4 |
| P2 | 5 |
| P3 | 5 |

| by category | open |
|---|---|
| security | 5 |
| ops | 4 |
| product | 3 |
| architecture | 3 |
| correctness | 0 |
| performance | 0 |

| by status | count |
|---|---|
| open | 15 |
| backlog | 0 |
| fixed | 0 |
| wontfix | 0 |

## Security profile

`rafa audit --json` (rafa.audit/v1): dependency tier **ran** (osv-api + pnpm audit over
`pnpm-lock.yaml`), secrets tier **ran** (0 findings), SAST tier **did not run** (semgrep
not installed; optional). 44 advisories: 2 critical, 19 high, 18 moderate, 5 low, grouped
into 4 rows by shared fix (next, sharp, postcss, undici). Priorities follow the fixed
severity mapping. Reachability notes are annotations only; they never lower a priority. The unauthenticated-endpoint row
comes from an observational review and is not scanner output. Machine detail: `security-audit.json`.

## Dropped this pass

- `@rafinery/cli` version drift (package.json 0.20.0 vs rafa.json 0.21.0), a scan seed: the
  working tree already carries 0.21.0 in `package.json` and `pnpm-lock.yaml` (uncommitted),
  so the cite no longer resolves and the row was not minted.
