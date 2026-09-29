---
id: dead-user-info-module
type: Improvement
schemaVersion: 1
priority: P3
category: architecture
status: open
title: "lib/user-info.ts is imported by nothing"
summary: "getUser and getUsers are leftover demo mock code with no importer; they suggest a user model the app does not have"
fix: "Delete lib/user-info.ts"
leverage: { impact: low, effort: low }
blast_radius: [data-ops]
cites:
  - lib/user-info.ts:1 :: export const getUser
found: 2026-09-30
description: "getUser and getUsers are leftover demo mock code with no importer; they suggest a user model the app does not have"
tags: [architecture, P3]
timestamp: 2026-09-30
---
# Dead module lib/user-info.ts

Verified: no tracked file references `user-info`. The module fabricates a user record
(`role: "admin"`, fixed email/phone) — harmless today, but it reads like a user/auth layer
that does not exist, which is the wrong signal next to an unauthenticated endpoint
([copilotkit-endpoint-unauthenticated](copilotkit-endpoint-unauthenticated.md)). The brain
already marks it as not-a-pattern
([static-dashboard-data-convention](/brain/rules/static-dashboard-data-convention.md)).
Deletion test: removing it concentrates nothing and smears nothing — pure deletion.

<!-- okf:citations:start (generated — the frontmatter `cites:` DSL is the source of truth; do not hand-edit) -->

# Citations

[1] [lib/user-info.ts:1](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/lib/user-info.ts#L1) — `export const getUser`

<!-- okf:citations:end -->
