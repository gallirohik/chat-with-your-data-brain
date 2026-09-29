---
schemaVersion: 1
id: security-posture
type: flow
domain: api
title: Security posture — one public, unauthenticated runtime endpoint that spends LLM and Tavily credit; secrets server-only; untrusted tool output rendered defensively
summary: Trust boundary is app/api/copilotkit/[[...slug]] (GET/POST, no auth, no rate limit, no middleware); env keys are read only server-side; client-supplied agent context and web-search results are untrusted and rendered without raw HTML, with an http/https link allow-list
links: [runtime-endpoint-path-contract, env-and-integrations, agent-context-contract, tool-result-rendering-convention, builtin-agent-runtime-convention, chat-turn-flow, repo-toolbox-inventory]
absent: getServerSession
absent: export function middleware
absent: rateLimit
absent: dangerouslySetInnerHTML
cites:
  - app/api/copilotkit/[[...slug]]/route.ts:41 :: export const GET = handler
  - app/api/copilotkit/[[...slug]]/route.ts:42 :: export const POST = handler
  - app/api/copilotkit/[[...slug]]/route.ts:19 :: process.env.TAVILY_API_KEY
  - app/api/copilotkit/[[...slug]]/route.ts:25 :: process.env.COPILOTKIT_MODEL
  - app/api/copilotkit/[[...slug]]/route.ts:28 :: maxSteps: 5
  - components/Dashboard.tsx:40 :: useAgentContext({
  - components/generative-ui/SearchResults.tsx:37 :: isSafeSearchResultUrl
  - components/generative-ui/SearchResults.tsx:101 :: rel="noreferrer"
  - app/layout.tsx:35 :: showDevConsole={false}
  - .gitignore:34 :: .env*
  - .mcp.json:7 :: RAFA_MCP_KEY
---
# Security posture

**Trust boundaries**

| surface | exposure | notes |
|---|---|---|
| `/` page + static data | public, client | all dashboard data ships in the JS bundle — it is mock data, nothing confidential |
| `app/api/copilotkit/[[...slug]]` `GET`/`POST` | **public server endpoint** | the only server surface; runs the LLM and Tavily with server keys |
| LLM provider, Tavily | outbound from server | keys from env, per request |

**Auth chokepoints: none.** No auth library, session check or middleware exists
(`getServerSession`, `export function middleware` declared `absent`), and no rate limiting
(`rateLimit` absent). Anyone who can reach the deployment can drive the agent and spend
provider/Tavily credit; the only per-turn bound is `maxSteps: 5`. Any production use MUST
add an authz check (and a rate limit) in front of the runtime handler — the route file is
the chokepoint to wrap. New tools that mutate state or cost money inherit this exposure
([add-server-tool-with-renderer](/brain/playbooks/add-server-tool-with-renderer.md)).

**Secrets handling (names only).** Repo code reads `TAVILY_API_KEY` and `COPILOTKIT_MODEL`
only inside the server route; the LLM key is consumed inside the runtime library; no
`NEXT_PUBLIC_` vars exist, so nothing is inlined into the client
([env-and-integrations](/brain/rules/env-and-integrations.md)). `.env*` is gitignored.
Tooling: `.mcp.json` references `RAFA_MCP_KEY` by name; the value is supplied from a
local, uncommitted Claude settings file — keep it out of git (it is ignored today via the
developer's global gitignore, not this repo's `.gitignore`).

**Untrusted inputs to the model.** The agent context (`useAgentContext` payloads) is
constructed in the browser and sent with each request — a caller can send arbitrary
"data" and instructions. Treat model output as untrusted; never let it trigger privileged
server actions without server-side validation
([agent-context-contract](/brain/rules/agent-context-contract.md)).

**Untrusted output rendering.** Web-search results and model text are rendered as React
text (no `dangerouslySetInnerHTML` anywhere — declared `absent`). Links from search results
pass the `http:`/`https:` allow-list and open with `rel="noreferrer"`
([tool-result-rendering-convention](/brain/rules/tool-result-rendering-convention.md)).
The CopilotKit dev console is disabled (`showDevConsole={false}`).

**Where bloom's reachability annotations should look:** server-reachable deps are
`@copilotkit/runtime`, `@tavily/core`, `zod`, `next`; everything else is client-bundle
or unused (`@tremor/react`, `react-hook-form`, `@radix-ui/*`, `date-fns`,
`react-day-picker` are declared but imported nowhere).
