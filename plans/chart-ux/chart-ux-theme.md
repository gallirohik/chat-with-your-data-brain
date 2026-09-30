---
schemaVersion: 1
id: chart-ux-theme
plan: chart-ux
parent: chart-ux
kind: task
type: Plan Task
timestamp: 2026-09-30T00:00:00Z
title: Shared chart theme — tokens, types, tooltip, legend, a11y frame
description: >-
  Each wrapper redeclares ChartDataItem and hard-codes hex colours, grid/tick greys and a
  white tooltip. One module becomes the single source for the look so the three wrappers
  stop drifting and the --chart-* tokens finally drive colour.
approach: new components/ui/chart-theme.ts (pure) + components/ui/chart-chrome.tsx (render-only components), both SSR-testable
status: todo
track: Theme
---
# Shared chart theme

Parent of [chart-ux-theme-core](chart-ux-theme-core.md) and
[chart-ux-theme-chrome](chart-ux-theme-chrome.md). Done when both subtasks are done.
