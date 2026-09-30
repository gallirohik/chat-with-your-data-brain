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
status: done
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

- 2026-09-30 — done, prism PASS (standard tier, round 2). Gate: `pnpm test` exits 0 (7 + 50) and lists all five chart test files; `pnpm check-types`, `pnpm build` exit 0; `git diff main` touches no `app/page.tsx`, `README.md`, `data/`; `package.json` = `scripts.test` + the `@rafinery/cli` 0.21.0→0.21.1 bump only, lockfile only that bump. Live pass (production build, Chrome 154 headless via CDP, 1280px with the CopilotKit sidebar open and 375px): all mark fills resolve from `var(--chart-N)`, area gradients 0.35→0.02, bars solid, donut gapped with centre label, no horizontal overflow; hover on all five charts shows the themed tooltip (swatch + name + value), bars dim the others to 0.55, the donut slice grows, mouse-leave restores; reduced-motion emulation gives marks at final size from the first frame while the default animates; the five figures are `role=img` named title + data (per-row for bar/donut/area); no hydration warnings. FOUND AT VERIFY (three real defects the SSR suite could not see, each fixed red-first or with a guard): (1) bar charts dropped x-axis category labels in narrow cards (3 of 5, 2 of 5 — Recharts' default `interval="preserveEnd"`; pre-existing) → `55c88df`/`aab36d9`: `interval={0}` + wrapped/truncated custom tick, full name kept in `<title>`, tooltip and aria-label (long single words truncate to ~6 letters only when the sidebar is open; at normal width all fit); (2) the donut was a nameless keyboard tab stop (Recharts `Pie` `rootTabIndex` defaults to 0; pre-existing) → `85afc62`/`eab7ff7` `rootTabIndex={-1}` plus tab-order assertions on area/bar/pie markup; (3) Customer Demographics numbers had no thousands separator (`$6000`) → `5ac951a`. Surprise: all three predate the plan and only showed up in a real browser — Recharts measures text as 0px under SSR, so default tick hiding never fires there (banked as a session fact). Residual / not met literally: the Done-check's "visible focus ring on every focusable element" is unmet by CopilotKit's own controls (sidebar toggle hidden under the open sidebar at 1280, off-screen Close/textarea stops at 375, textarea `outline:none` — `app/page.tsx` + third-party, unchanged vs main, outside this plan's scope) — follow-up. Cosmetic leftovers: bar tooltips show raw keys "sales"/"spending" (renaming keys would touch the chart-data-key contract), donut tooltip covers the centre label on hover, donut has no entry animation (same as main), Chrome only / no real screen reader.

## Decisions
