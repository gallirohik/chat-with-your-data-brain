---
schemaVersion: 1
id: chart-ux-dashboard-cards
plan: chart-ux
parent: chart-ux
kind: task
type: Plan Task
timestamp: 2026-09-30T00:00:00Z
title: Chart cards — token palette, header polish, per-chart aria labels; data keys untouched
description: >-
  Dashboard.tsx:78 hard-codes five hex arrays (and single-series bars only ever use the
  first colour). Swapping to the token palette and labelling each chart touches the lines
  right next to the chart-data-key contract, hence full tier. The only leaf that edits
  Dashboard.tsx.
approach: "how: frontend-design skill for header rhythm; chart-data-key guard via git diff; brain tokens win"
status: done
track: Composition
validation_tier: full
blocked_by: [chart-ux-area, chart-ux-bar, chart-ux-donut]
---
# Chart cards

In `components/Dashboard.tsx` (this is the ONLY leaf that edits it):

- Replace the hex `colors` map (line ~78) with literal token strings from `CHART_PALETTE`:
  Sales Overview uses palette order; the three single-series bar cards (Product Performance,
  Regional Sales, Customer Demographics) each get a DIFFERENT token so they are
  distinguishable; donut uses the palette. No hex literals remain.
- Card header polish: consistent title/description rhythm (`CardTitle` `text-sm
  font-medium`, `CardDescription` `text-xs text-muted-foreground`), tighter content padding.
- Pass `ariaLabel` = the card title on all five charts (e.g. `ariaLabel="Sales Overview"`);
  the wrapper prefixes it to the generated per-row summary, so each label names its chart
  AND carries its data.
- Donut: pass `centerValue` (e.g. number of categories) with the existing `centerText`.
- KPI tiles are NOT touched (dropped from scope — see epic non-goals).
- **Do not touch** `data=`, `index=`, `categories=`, `category=` props, the
  `useAgentContext` value ([agent-context-contract](/brain/rules/agent-context-contract.md)),
  or the `useRenderTool` block. Keep every `h-60` chart parent.

## Done-check

- **Seam (contract guard, Dashboard is not SSR-testable — CopilotKit hooks need a provider):**
  `git diff -U0 main -- components/Dashboard.tsx | grep -E '^[-+].*[[:space:]](data|index|categories|category)='`
  prints NOTHING (exit 1 from grep) — the five call sites' keys are byte-identical to `main`
  ([chart-data-key-contract](/brain/rules/chart-data-key-contract.md)). Positive control
  verified at plan time: the grep matches a changed `index="date"` line.
- `grep -c 'className="h-60"' components/Dashboard.tsx` prints `5`.
- `grep -c 'ariaLabel=' components/Dashboard.tsx` prints `5`.
- The three BarChart call sites pass three pairwise-different palette tokens (each resolves
  to a distinct `var(--chart-N)`) — shown in the Log by quoting the three `colors=` values.
- `grep -nE "#[0-9a-fA-F]{6}" components/Dashboard.tsx` returns nothing.
- `git diff main -- components/Dashboard.tsx` shows no change inside the `useAgentContext({ … })`
  or `useRenderTool({ … })` blocks, nor in the KPI tile markup.
- `pnpm check-types`, `pnpm test`, `pnpm build` exit 0.
- Live check (run skill / dev server): all five cards render non-empty charts with
  distinct colours — screenshot attached to the Log.

## Log

- 2026-09-30 — done, prism PASS (full tier). `c68c643` edits ONLY `components/Dashboard.tsx`: the hex `colors` map is gone (Area/Donut use `CHART_PALETTE` order by default; the three single-series bars each get a different token — `barColors.productPerformance = CHART_PALETTE[2]` (`var(--chart-3)`, violet 6.66:1), `regional = [1]` (`var(--chart-2)`, teal 4.07:1), `demographics = [3]` (`var(--chart-4)`, amber 3.83:1)); `ariaLabel` = the card title on all five charts; donut `centerValue={String(categoryData.length)}` ("5 / Categories"); card headers `text-sm font-medium` / `text-xs text-muted-foreground`, Card `gap-3 py-4` (tailwind-merge replaces the primitive's `gap-6 py-6`). No data prop changed: prism read the whole diff — `data=`/`index=`/`categories=`/`category=` are byte-identical and each card still renders the same chart type on the same dataset (the regex guard alone cannot see a card↔chart swap, so the diff was read by eye). `h-60` ×5, `ariaLabel=` ×5, no hex, `useAgentContext`/`useRenderTool`/KPI markup untouched. Live check (the Done-check's screenshot line): prism rendered the production build in the already-installed Chrome at 1280px — five non-empty charts, fills `var(--chart-N)` resolve, aria-labels = title + data rows. Observed by eye in that screenshot (with the CopilotKit sidebar open): the bar charts drop x-axis category labels (Product Performance shows 3 of 5, Regional Sales 2 of 5) because Recharts' default tick interval hides overlapping labels — pre-existing behaviour, routed to chart-ux-verify. Residual: dark mode not rendered (`.dark` inert); dark `--chart-1` is 2.89:1 vs dark `--card` by calculation and predates this plan (dark-mode-inert, P3).

## Decisions
