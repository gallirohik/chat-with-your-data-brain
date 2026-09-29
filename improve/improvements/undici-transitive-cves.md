---
id: undici-transitive-cves
type: Improvement
schemaVersion: 1
priority: P1
category: security
status: open
title: "undici 5.29.0 under @copilotkit/runtime carries 3 high advisories"
summary: "@copilotkit/runtime > @ai-sdk/google-vertex > @ai-sdk/provider-utils@3.0.40 pulls undici@5.29.0 - 13 advisories (3 high); fixes are in undici 7.24-8.10"
fix: "Check whether a newer @copilotkit/runtime (bumped together with react-core) drops the provider-utils 3.x chain; otherwise add a scoped pnpm override for undici and smoke-test a chat turn"
leverage: { impact: medium, effort: medium }
blast_radius: [agent-runtime, build-tooling]
cites:
  - pnpm-lock.yaml:4105 :: undici: 5.29.0
  - pnpm-lock.yaml:3760 :: undici@5.29.0
  - package.json:15 :: "@copilotkit/runtime": "1.75.0"
found: 2026-09-30
description: "@copilotkit/runtime > @ai-sdk/google-vertex > @ai-sdk/provider-utils@3.0.40 pulls undici@5.29.0 - 13 advisories (3 high); fixes are in undici 7.24-8.10"
tags: [security, P1]
timestamp: 2026-09-30
---
# undici 5.29.0 via the CopilotKit runtime

Source: `rafa audit --json` dependency tier (osv-api). Mechanical map: high -> P1.

- **Package:** `undici@5.29.0` — **transitive**, dev:false. Chain:
  `@copilotkit/runtime@1.75.0 > @ai-sdk/google-vertex@3.0.179 > @ai-sdk/provider-utils@3.0.40 > undici@5.29.0`.
- high: GHSA-v9p9-hfj2-hcw8 (CVE-2026-2229, WebSocket unhandled exception, fixedIn 7.24.0),
  GHSA-vrm6-8vpv-qv8q (CVE-2026-1526, permessage-deflate memory, 7.24.0),
  GHSA-vxpw-j846-p89q (CVE-2026-12151, WebSocket fragment DoS, 8.5.0).
- moderate (7): request smuggling, CRLF / header / cookie injection, decompression chain,
  retry-interceptor desync — fixedIn 7.18.2–8.9.0.
- low (3): keep-alive queue poisoning, SameSite downgrade, retry response splitting — up to 8.10.2.

Reachability: server-bundled via the agent runtime, but only on the Google Vertex provider
path; the default model is `openai/gpt-5-mini`
([change-agent-model-or-prompt](/brain/playbooks/change-agent-model-or-prompt.md)) and the
WebSocket-client highs need a WS client — likely unreached today. Annotation only; P1 stands.

Fix caution: a 5.x -> 8.x override is a major jump for a library written against 5.x — prefer
the CopilotKit bump (both `@copilotkit/*` pins move together per
[build-and-deps-convention](/brain/rules/build-and-deps-convention.md)).

<!-- okf:citations:start (generated — the frontmatter `cites:` DSL is the source of truth; do not hand-edit) -->

# Citations

[1] [pnpm-lock.yaml:4105](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/pnpm-lock.yaml#L4105) — `undici: 5.29.0`
[2] [pnpm-lock.yaml:3760](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/pnpm-lock.yaml#L3760) — `undici@5.29.0`
[3] [package.json:15](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/package.json#L15) — `"@copilotkit/runtime": "1.75.0"`

<!-- okf:citations:end -->
