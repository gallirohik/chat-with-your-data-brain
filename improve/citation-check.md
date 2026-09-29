---
type: "Citation Check"
title: "Citation check report"
description: "checker v2 — all gates pass · 20 warn(s)"
timestamp: 2026-09-29T23:18:47.456Z
---
# Citation check (generated — do not hand-edit) · checker v2

## Resolution (B1): 36/36 ✓
✓ app/api/copilotkit/[[...slug]]/route.ts:42 :: export const POST = handler
✓ app/api/copilotkit/[[...slug]]/route.ts:19 :: process.env.TAVILY_API_KEY
✓ app/api/copilotkit/[[...slug]]/route.ts:28 :: maxSteps: 5
✓ wfcms-data.json:4 :: live_demo
✓ app/globals.css:48 :: .dark {
✓ app/globals.css:5 :: @custom-variant dark
✓ components/generative-ui/SearchResults.tsx:55 :: dark:bg-gray-800
✓ lib/user-info.ts:1 :: export const getUser
✓ data/dashboard-data.ts:225 :: 8.13
✓ components/Dashboard.tsx:53 :: conversionRate
✓ components/Dashboard.tsx:110 :: Conversion Rate
✓ package.json:11 :: oxlint .
✓ package.json:27 :: "next": "16.1.7"
✓ pnpm-lock.yaml:3127 :: next@16.1.7
✓ package.json:8 :: "test"
✓ package.json:10 :: tsc --noEmit
✓ pnpm-lock.yaml:7585 :: postcss: 8.4.31
✓ pnpm-lock.yaml:3281 :: postcss@8.4.31
✓ README.md:10 :: ./preview.gif
✓ preview.gif:1 :: git-lfs
✓ pnpm-lock.yaml:7599 :: sharp: 0.34.5
✓ pnpm-lock.yaml:3577 :: sharp@0.34.5
✓ page-accessibility.test.mjs:12 :: fallback=
✓ readme-runtime.test.mjs:45 :: useRenderTool
✓ package.json:8 :: node --test current-time.test.mjs page-accessibility.test.mjs readme-runtime.test.mjs
✓ next.config.ts:7 :: path.resolve(__dirname, "../../..")
✓ pnpm-lock.yaml:4105 :: undici: 5.29.0
✓ pnpm-lock.yaml:3760 :: undici@5.29.0
✓ package.json:15 :: "@copilotkit/runtime": "1.75.0"
✓ package.json:22 :: "@tremor/react"
✓ package.json:31 :: "react-hook-form"
✓ package.json:16 :: "@hookform/resolvers"
✓ package.json:17 :: "@radix-ui/react-label"
✓ package.json:25 :: "date-fns"
✓ package.json:29 :: "react-day-picker"
✓ package.json:23 :: "class-variance-authority"

## Completeness (B2): 0/0 ✓  (0 anchors)

## Policy (contract → anchor declared): 0/0 ✓

## Absence (B3, declared `absent:` re-grepped): 0/0 ✓

## Inventory (coverage declared vs `git ls-files`): 0/0 ✓

## Warns (heuristic, non-failing — existence-shaped title/summary with no `absent:` declared): 1
⚠ improvements/lint-script-oxlint-missing.md — reads as an absence claim ("absent from") — declare `absent: <token>` so the gate can re-grep it, or reword

## Links (non-failing, OKF §5.3 — dangling cross-links, bundle-wide resolution): 19
⚠ improvements/copilotkit-endpoint-unauthenticated.md — mdlink → /brain/playbooks/security-posture.md does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)
⚠ improvements/copilotkit-endpoint-unauthenticated.md — mdlink → /brain/rules/agent-context-contract.md does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)
⚠ improvements/copilotkit-endpoint-unauthenticated.md — mdlink → /brain/playbooks/add-server-tool-with-renderer.md does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)
⚠ improvements/dark-mode-inert.md — mdlink → /brain/rules/design-tokens-convention.md does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)
⚠ improvements/dead-user-info-module.md — mdlink → /brain/rules/static-dashboard-data-convention.md does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)
⚠ improvements/hardcoded-conversion-rate-kpi.md — mdlink → /brain/rules/static-dashboard-data-convention.md does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)
⚠ improvements/hardcoded-conversion-rate-kpi.md — mdlink → /brain/playbooks/add-dashboard-dataset.md does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)
⚠ improvements/lint-script-oxlint-missing.md — mdlink → /brain/rules/build-and-deps-convention.md does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)
⚠ improvements/next-16-1-7-critical-cves.md — mdlink → /brain/playbooks/security-posture.md does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)
⚠ improvements/next-16-1-7-critical-cves.md — mdlink → /brain/rules/source-text-tests-contract.md does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)
⚠ improvements/next-16-1-7-critical-cves.md — mdlink → /brain/rules/build-and-deps-convention.md does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)
⚠ improvements/no-ci-gate.md — mdlink → /brain/rules/source-text-tests-contract.md does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)
⚠ improvements/source-text-tests-brittle.md — mdlink → /brain/rules/source-text-tests-contract.md does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)
⚠ improvements/test-script-explicit-file-list.md — mdlink → /brain/rules/test-runner-convention.md does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)
⚠ improvements/turbopack-root-outside-repo.md — mdlink → /brain/rules/build-and-deps-convention.md does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)
⚠ improvements/undici-transitive-cves.md — mdlink → /brain/playbooks/change-agent-model-or-prompt.md does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)
⚠ improvements/undici-transitive-cves.md — mdlink → /brain/rules/build-and-deps-convention.md does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)
⚠ improvements/unused-template-dependencies.md — mdlink → /brain/rules/build-and-deps-convention.md does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)
⚠ improvements/unused-template-dependencies.md — mdlink → /brain/rules/chart-primitive-convention.md does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)

**All pass.**
