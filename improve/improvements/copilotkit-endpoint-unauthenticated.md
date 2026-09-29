---
id: copilotkit-endpoint-unauthenticated
type: Improvement
schemaVersion: 1
priority: P1
category: security
status: open
title: "Public /api/copilotkit accepts anonymous POSTs that spend LLM and Tavily credit"
summary: "The only server route runs the agent and Tavily search with server keys for any caller - no auth, rate limit or origin check; only maxSteps bounds one turn"
fix: "Before any deployment beyond a local demo, wrap the handler with an auth or signed-session check plus a per-IP rate limit (or put the deployment behind platform protection); keep maxSteps as the per-turn bound"
leverage: { impact: high, effort: medium }
blast_radius: [api, agent-runtime, external-integrations]
cites:
  - app/api/copilotkit/[[...slug]]/route.ts:42 :: export const POST = handler
  - app/api/copilotkit/[[...slug]]/route.ts:19 :: process.env.TAVILY_API_KEY
  - app/api/copilotkit/[[...slug]]/route.ts:28 :: maxSteps: 5
  - wfcms-data.json:4 :: live_demo
found: 2026-09-30
description: "The only server route runs the agent and Tavily search with server keys for any caller - no auth, rate limit or origin check; only maxSteps bounds one turn"
tags: [security, P1]
timestamp: 2026-09-30
---
# Unauthenticated runtime endpoint spends paid credit

Observational (LLM security lens, not scanner output). Verified in code: `route.ts` exports
the CopilotKit handler as both `GET` and `POST` with no auth check, no middleware file in
the repo, and no rate limiting; each request can run the `BuiltInAgent` (up to
`maxSteps: 5`) and call Tavily with the server's `TAVILY_API_KEY`. The agent context is
client-built, so a caller can also send arbitrary instructions
([security-posture](/brain/playbooks/security-posture.md),
[agent-context-contract](/brain/rules/agent-context-contract.md)).

**Why P1, not P0.** No user data sits behind the endpoint (all dashboard data is static
mock data shipped in the bundle), so this is a cost-abuse / denial-of-wallet exposure rather
than a data breach, and the repo is a public demo by design (`wfcms-data.json` lists a live
demo URL). It becomes P0 the moment real data or a paid key with no spend cap is deployed.

**Fix path (first slice):** set a provider-side spend cap on the LLM and Tavily keys today
(zero code). Then wrap `handler` in `route.ts` — the single chokepoint — with an auth check
and a per-IP rate limit. New tools inherit this exposure
([add-server-tool-with-renderer](/brain/playbooks/add-server-tool-with-renderer.md)).

<!-- okf:citations:start (generated — the frontmatter `cites:` DSL is the source of truth; do not hand-edit) -->

# Citations

[1] [app/api/copilotkit/[[...slug]]/route.ts:42](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/app/api/copilotkit/[[...slug]]/route.ts#L42) — `export const POST = handler`
[2] [app/api/copilotkit/[[...slug]]/route.ts:19](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/app/api/copilotkit/[[...slug]]/route.ts#L19) — `process.env.TAVILY_API_KEY`
[3] [app/api/copilotkit/[[...slug]]/route.ts:28](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/app/api/copilotkit/[[...slug]]/route.ts#L28) — `maxSteps: 5`
[4] [wfcms-data.json:4](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/wfcms-data.json#L4) — `live_demo`

<!-- okf:citations:end -->
