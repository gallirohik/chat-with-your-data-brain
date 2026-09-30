---
schemaVersion: 1
id: chart-ux
plan: chart-ux
parent: null
kind: epic
type: Plan Epic
timestamp: 2026-09-30T00:00:00Z
title: Modernise the dashboard chart UX
description: >-
  Owner: "the dashboard charts seem outdated — improve the UX of these charts".
  The wrappers still carry the template look: 10%-opacity outline fills, hard
  strokes, white bordered tooltips, fixed 30px outlined bars, outlined donut
  slices, and hex palettes that ignore the --chart-* tokens (whose current values
  also fail contrast on the card background).
approach: extend the existing Recharts wrappers through one shared token-driven theme module (never Tremor), keep the prop API backward compatible, test at the wrapper seams via SSR markup
branch: feat/chart-ux
baseSha: 59c84f7c94713a5745d9e64bd57601fd5f777005
domains: [design-system, components, testing, data-ops]
priority: 3
---
# Modernise the dashboard chart UX

## Briefing (what the brain already says)

- Charts are hand-rolled Recharts wrappers with a Tremor-style API; `@tremor/react` is
  declared but never imported — extend the wrappers
  ([chart-primitive-convention](/brain/rules/chart-primitive-convention.md)).
- `index` / `categories` / `category` are plain string keys into `data/dashboard-data.ts`
  rows; `ChartDataItem` is an index signature, so a renamed key type-checks and renders an
  empty chart ([chart-data-key-contract](/brain/rules/chart-data-key-contract.md), anchor none).
- Tokens live in `app/globals.css` (`--chart-1..5` oklch in `:root`, mapped by `@theme inline`);
  new UI uses token utilities; `.dark` is inert
  ([design-tokens-convention](/brain/rules/design-tokens-convention.md)).
- Tests: `node:test` + `tsx --test`, explicit file list in `package.json` — a new test file
  not added to the script never runs ([test-runner-convention](/brain/rules/test-runner-convention.md)).
  `app/page.tsx` and README are regex-asserted as text — this plan does NOT touch them
  ([source-text-tests-contract](/brain/rules/source-text-tests-contract.md)).
- Recent deltas: none in this blast radius since the founding scan (brain at 59c84f7).

## Verified at plan time (code, not memory)

- `components/ui/area-chart.tsx:115`, `bar-chart.tsx:119`, `pie-chart.tsx:138` — `fillOpacity={0.1}`
  outline look; `bar-chart.tsx:123` fixed `barSize` 30/20; `pie-chart.tsx:180`
  `props.innerRadius || 40`; `ChartDataItem` redeclared at area:15, bar:15, pie:14.
- Pie API is `category` (singular) + `index`, not `categories` (`pie-chart.tsx:52`).
- `Dashboard.tsx:78` hex `colors` map; five `<div className="h-60">` chart parents
  (lines 141, 167, 192, 220, 245).
- **Palette contrast** (`:root` `--chart-1..5` vs white `--card`, WCAG, oklch→sRGB):
  3.59 / 3.66 / 9.12 / **1.72 / 2.15**, min pairwise OKLab distance 0.075 — `--chart-4`/`--chart-5`
  fail ≥3:1 and are near-identical ambers. A retuned candidate (see chart-ux-theme-core)
  measures 5.02 / 4.07 / 6.66 / 3.83 / 4.96, min distance 0.138, all in sRGB gamut.
- **SF-1 (session fact, re-verified round 2):** Recharts 2.15.4 `ResponsiveContainer`
  renders an EMPTY div under `renderToStaticMarkup` — no SVG — unless
  `initialDimension={{width,height}}` is passed. With it, Area (defs + `fill="url(#id)"`)
  renders server-side whether `isAnimationActive` is default, `true` or `false`; Bar renders
  its `recharts-bar-rectangle"` paths (with fill and radius arcs) with the DEFAULT or `false`
  setting — with explicit `true` the 3 groups render but are empty `<g>`s with no `<path>`;
  **Pie renders its `recharts-sector`s only with the DEFAULT or `false`
  setting — an explicit `isAnimationActive={true}` renders 0 sectors** (probe: 5-row pie →
  default 5 · true 0 · false 5). Recharts' default is `!Global.isSsr`, so wrappers pass
  `isAnimationActive={prefersReducedMotion ? false : undefined}` and never an explicit
  `true`. Legend `content` also renders under SSR inside `recharts-legend-wrapper`. Hence
  every wrapper gains an optional, additive `initialDimension` pass-through prop — the test seam.

## Seams (TDD — the approval of this plan confirms them)

| leaf | runner · test file | seam (public interface) |
|---|---|---|
| chart-ux-theme-core | `tsx --test components/ui/chart-theme.test.ts` | `chart-theme` exports (`CHART_PALETTE`, `seriesColor`, `chartGradientId`, `buildChartSummary`, `AXIS_TICK`, `GRID_STROKE`, `usePrefersReducedMotion`) + `:root --chart-*` tokens parsed from `app/globals.css` |
| chart-ux-theme-chrome | `tsx --test components/ui/chart-chrome.test.tsx` | `<ChartTooltip>`, `<ChartLegend>`, `<ChartFrame>` rendered markup |
| chart-ux-area | `tsx --test components/ui/area-chart.test.tsx` | `<AreaChart>` props → SSR markup (with `initialDimension`) |
| chart-ux-bar | `tsx --test components/ui/bar-chart.test.tsx` | `<BarChart>` props → SSR markup (with `initialDimension`) |
| chart-ux-donut | `tsx --test components/ui/pie-chart.test.tsx` | `<PieChart>` / `<DonutChart>` props → SSR markup (with `initialDimension`) |
| chart-ux-dashboard-cards | `git diff` key-guard + greps + `pnpm check-types` + `pnpm build` | the five `Dashboard.tsx` call sites (chart-data-key-contract) — Dashboard is not SSR-testable (CopilotKit hooks need a provider) |
| chart-ux-verify | `pnpm test` + live browser pass | whole dashboard at 1280px / 375px, reduced-motion emulated |

Every leaf also runs `pnpm check-types` and `pnpm build` (exit 0); each code leaf appends its
test file to `package.json` `scripts.test` (the `tsx --test …` list).

## Tree and execution order — the build runs SERIALLY

Every leaf appends to the same `scripts.test` line, and only chart-ux-dashboard-cards edits
`Dashboard.tsx`, so the leaves form one `blocked_by` chain — no two leaves run in parallel:

theme-core → theme-chrome → area → bar → donut → dashboard-cards → verify

1. **chart-ux-theme** — shared theme
   - [chart-ux-theme-core](chart-ux-theme-core.md) (full) — palette retune + contrast gate, shared type, ids, per-row summary, reduced motion
   - [chart-ux-theme-chrome](chart-ux-theme-chrome.md) (standard) — tooltip, legend, a11y frame
2. [chart-ux-area](chart-ux-area.md) (standard) — gradient areas, crosshair, themed tooltip/legend
3. [chart-ux-bar](chart-ux-bar.md) (standard) — solid rounded bars, hover emphasis, responsive width
4. [chart-ux-donut](chart-ux-donut.md) (standard) — solid slices with gaps, centre label, hover, legend values
5. [chart-ux-dashboard-cards](chart-ux-dashboard-cards.md) (full) — token palette, card headers, aria labels, keys untouched
6. [chart-ux-verify](chart-ux-verify.md) (standard) — full gate + live visual / a11y pass

## ADR — extend the wrappers vs adopt a chart kit

**Context.** Five charts, three wrapper files, one call site file; the prop API is the
chart-data-key contract's surface.

**Options.**
1. **Extend the local Recharts wrappers** through a shared `chart-theme` module (chosen).
2. Adopt `@tremor/react` (already declared) — rejected: the brain declares it `absent`
   (never imported), it pins its own Recharts/Tailwind assumptions (Tailwind v3 config
   era) against our v4 CSS-first tokens, and it would replace every call site at once.
3. Adopt shadcn `chart` (ChartContainer/ChartTooltip) — rejected for now: it is a
   generated copy of the same Recharts idiom with a different config API
   (`ChartConfig`), so it would rewrite all five call sites and the key contract's
   surface for no capability we cannot add locally. Its good ideas (CSS-var colours,
   token tooltip) are borrowed into option 1.

**Consequences.** Zero new dependencies; call sites keep compiling unchanged; the theme
module becomes the one place colours/axes/tooltips are defined, so dark mode works the day
`.dark` is wired.

## Risks

- **Silent key drift** (chart-data-key-contract): any edit near `index=`/`categories=`/
  `category=`/`data=` can empty a chart with no type error. Guard: chart-ux-dashboard-cards
  Done-check diffs those lines against `main` and must show zero changes.
- **0px charts**: `ResponsiveContainer` needs a sized parent; the five `h-60` wrappers must
  survive the card refactor (Done-check counts them).
- **Hydration of gradient ids**: ids come from `React.useId()` (stable across server and
  client), encoded (not stripped) for `url(#…)`; never `Math.random()`/counters.
- **Reduced-motion hydration**: `matchMedia` is read in an effect, never during render, so
  server and first client render agree (asserted in theme-core). Wrappers map the hook to
  `isAnimationActive={prefersReducedMotion ? false : undefined}`: an explicit `true` would
  override Recharts' SSR-aware default, empty the donut under SSR and risk a hydration
  mismatch.
- **Residual — reduced-motion subscription is only proven live**: a `() => false` stub
  passes theme-core's SSR probe (server output is `false` either way). The `matchMedia`
  read and change subscription are proven ONLY by chart-ux-verify's live pass (DevTools
  reduced-motion emulation + reload). Accepted: node:test has no `matchMedia`, and faking
  one would test the fake.
- **Token names hidden from Tailwind**: palette strings are literal `var(--chart-N)` (the
  always-defined `:root` vars) — never template-built.
- **CSS vars inside SVG attributes**: `fill="var(--chart-1)"` resolves in current browsers
  (the shadcn chart precedent); confirmed live in chart-ux-verify, not assumed.
- **Accessibility**: `role="img"` on the frame makes the legend, tooltip and ticks
  presentational, so the `aria-label` must carry the data itself — per-row `label: value`
  for categorical / short charts, range + min/max for long series. Recharts'
  `accessibilityLayer` stays OFF (an interactive region inside `role="img"` is invalid).

## Non-goals

Data changes (`data/dashboard-data.ts` untouched) · dark-mode toggle (dark-mode-inert P3 not
adopted; `.dark` block byte-identical) · new chart types · dependency adds/bumps/removals ·
`app/page.tsx`, README, Header/Footer · agent context shape (`useAgentContext` value
unchanged) · **KPI tiles and KPI trends** (dropped at plan review as beyond "the charts" —
offer later) · value-label (`showValues`) and gradient-toggle (`showGradient`) props (no call
site needs them).

## Security

`rafa audit` (0.21.1 `--report`) at plan time: **44 advisories — 2 critical, 19 high,
18 moderate, 5 low**; dependency + secrets tiers ran, SAST not run (semgrep missing).
**None sit in chart or design-system files.** The P0 `next@16.1.7` CVEs live in the
build-tooling / routing region and are NOT in this plan's scope — tracked as improvement
`next-16-1-7-critical-cves`. Surfaced, not adopted.

## While-you're-here (offered, not adopted)

- `hardcoded-conversion-rate-kpi` (P2, data-ops) — the Conversion Rate tile shows a constant
  as derived data. Untouched by this plan (KPI tiles are out of scope).
- `test-script-explicit-file-list` (P2, testing) — every leaf here appends to the explicit
  `tsx --test` list (and that shared line is why the build is serialized); switching to
  discovery would remove that chore. Not adopted.

## Decisions

- (atlas, pending owner approval) Extend wrappers over Tremor/shadcn — see ADR.
- (atlas, pending owner approval) `role="img"` + a data-bearing `aria-label` over Recharts
  `accessibilityLayer` keyboard navigation; `ariaLabel` props are title prefixes, never
  replacements of the generated summary.
- (atlas, pending owner approval) Optional `initialDimension` pass-through on the wrappers as
  the test seam (SF-1) rather than testing Recharts internals or exporting private sub-components.
- (prism round 2, applied) `isAnimationActive` never explicit `true` (SSR-aware default
  kept); legend assertions scoped to `data-slot="chart-legend"`; `ariaLabel`-as-title-prefix
  tested in theme-core, area, bar and donut; palette parse asserts exactly 5 `:root` values +
  `--card` and in-gamut; concrete arc observable for the bar radius.
- (prism round 1, applied) Palette retune is required and gated by an in-repo contrast +
  OKLab-distance test; KPI trend task, `showValues`, `showGradient` dropped as YAGNI; leaves
  serialized through one `blocked_by` chain.
