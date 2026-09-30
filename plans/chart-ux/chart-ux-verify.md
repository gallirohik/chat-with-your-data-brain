---
schemaVersion: 1
id: chart-ux-verify
plan: chart-ux
parent: chart-ux
kind: task
type: Plan Task
timestamp: 2026-09-30T00:00:00Z
title: Full gate + live visual and accessibility pass of the dashboard
description: >-
  SSR markup tests cannot prove what a browser paints (CSS vars inside SVG attributes,
  gradients, hover states, reduced motion). One live pass closes that gap before merge.
approach: "how: run skill to launch the dev server and drive a browser; screenshots into the Log"
status: todo
track: Verify
validation_tier: standard
blocked_by: [chart-ux-dashboard-cards]
---
# Verify

## Done-check

- **Seam (whole suite):** `pnpm test` exits 0 and its output includes the tests from
  `chart-theme.test.ts`, `chart-chrome.test.tsx`, `area-chart.test.tsx`,
  `bar-chart.test.tsx`, `pie-chart.test.tsx` (proves each is registered, not orphaned —
  [test-runner-convention](/brain/rules/test-runner-convention.md));
  `pnpm check-types` and `pnpm build` exit 0.
- `git diff main --stat` touches no `app/page.tsx`, `README.md`, or `data/`.
- `package.json` diff vs `main` is limited to: `scripts.test` (the five appended test files)
  and the `@rafinery/cli` devDependency `0.21.0 → 0.21.1` — an UNCOMMITTED working-tree
  change that predates this plan (HEAD and `rafa.json` still say 0.21.0); it rides this
  branch's commits and is the only allowed dependency delta. `pnpm-lock.yaml` changes only
  for that same bump. No other dependency added, bumped or removed.
- Live pass (dev server), screenshots in the Log:
  - 1280px and 375px widths: five charts render with visible, distinct token colours (proves
    `var(--chart-N)` resolves inside SVG attributes), gradients on the area chart, solid
    bars, gapped donut with centre label;
  - hovering each chart shows the themed tooltip with swatch + name + value;
  - DevTools "emulate prefers-reduced-motion: reduce" + reload: charts appear without
    animation;
  - accessibility tree shows each chart as `img` whose name carries the card title and the
    data (per-row for the bar and donut cards); Tab order through the page shows a visible
    focus ring on every focusable element and no chart becomes a silent tab stop;
  - browser console free of hydration-mismatch warnings.

## Log

## Decisions
