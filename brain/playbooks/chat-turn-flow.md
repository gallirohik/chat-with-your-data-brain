---
schemaVersion: 1
id: chat-turn-flow
type: flow
domain: agent-runtime
title: One chat turn end to end — sidebar input to agent run, Tavily tool call and rendered answer
summary: CopilotSidebar sends the message plus useAgentContext payloads to /api/copilotkit; the catch-all route hands it to the default BuiltInAgent (prompt, maxSteps 5); a searchInternet call runs Tavily server-side and streams back to the useRenderTool renderer; text renders through CustomAssistantMessage
links: [copilotkit-provider-nesting-contract, runtime-endpoint-path-contract, agent-context-contract, builtin-agent-runtime-convention, search-internet-tool-contract, tool-result-rendering-convention, env-and-integrations, security-posture]
cites:
  - app/page.tsx:41 :: <CopilotSidebar
  - app/page.tsx:43 :: assistantMessage: CustomAssistantMessage
  - app/layout.tsx:34 :: runtimeUrl="/api/copilotkit"
  - components/Dashboard.tsx:40 :: useAgentContext({
  - app/page.tsx:24 :: useAgentContext({
  - app/api/copilotkit/[[...slug]]/route.ts:42 :: export const POST
  - app/api/copilotkit/[[...slug]]/route.ts:36 :: createCopilotRuntimeHandler
  - app/api/copilotkit/[[...slug]]/route.ts:32 :: agents: { default: agent }
  - app/api/copilotkit/[[...slug]]/route.ts:26 :: prompt
  - app/api/copilotkit/[[...slug]]/route.ts:18 :: execute: async ({ query })
  - app/api/copilotkit/[[...slug]]/route.ts:20 :: client.search(query, { maxResults: 5 })
  - components/Dashboard.tsx:66 :: render: ({ parameters, status, result })
  - components/generative-ui/SearchResults.tsx:46 :: export function SearchResults
  - components/AssistantMessage.tsx:8 :: <CopilotChatAssistantMessage
---
# Chat turn flow

1. **Input** — the user types in `<CopilotSidebar>` (`app/page.tsx:41`, open by default,
   labels "Data Assistant"). It is inside `<CopilotKit>`
   ([copilotkit-provider-nesting-contract](/brain/rules/copilotkit-provider-nesting-contract.md)).
2. **Context attach** — the provider bundles the registered agent context: dashboard
   datasets + metrics (`Dashboard.tsx:40`) and the current time (`page.tsx:24`)
   ([agent-context-contract](/brain/rules/agent-context-contract.md)).
3. **Transport** — the client calls sub-routes under `runtimeUrl="/api/copilotkit"`
   (multi-endpoint mode) → `app/api/copilotkit/[[...slug]]/route.ts` `GET`/`POST` →
   `createCopilotRuntimeHandler({ basePath })`
   ([runtime-endpoint-path-contract](/brain/rules/runtime-endpoint-path-contract.md)).
   No auth on this hop ([security-posture](/brain/playbooks/security-posture.md)).
4. **Agent run** — `CopilotRuntime` dispatches to agent `default`: a `BuiltInAgent` with the
   `lib/prompt.ts` system prompt, model from `COPILOTKIT_MODEL`, up to 5 steps, state in
   `InMemoryAgentRunner` ([builtin-agent-runtime-convention](/brain/rules/builtin-agent-runtime-convention.md)).
   The LLM call itself happens inside the runtime library.
5. **Tool call (optional)** — if the model calls `searchInternet`, `execute` builds a Tavily
   client from `TAVILY_API_KEY` and returns `client.search(query, { maxResults: 5 })`
   ([env-and-integrations](/brain/rules/env-and-integrations.md)).
6. **Tool render** — the call streams to the browser; `useRenderTool({ name:
   "searchInternet" })` in `Dashboard.tsx` renders `<SearchResults>` through
   `executing → inProgress → complete`, parsing the stringified result or showing the
   `"Error:"` branch ([search-internet-tool-contract](/brain/rules/search-internet-tool-contract.md),
   [tool-result-rendering-convention](/brain/rules/tool-result-rendering-convention.md)).
7. **Answer** — model text renders through `CustomAssistantMessage` (a styled
   `CopilotChatAssistantMessage`), markdown/tables per the prompt.

**Debugging map:** chat 404 → step 3; agent ignores data → step 2; "search failed" card →
step 5 env; default tool UI instead of the card → step 6 name mismatch; answer truncated
after several searches → `maxSteps` in step 4; model/auth errors on every turn → provider
key (step 4, [env-and-integrations](/brain/rules/env-and-integrations.md)).
