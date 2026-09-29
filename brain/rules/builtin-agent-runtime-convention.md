---
schemaVersion: 1
id: builtin-agent-runtime-convention
type: convention
domain: agent-runtime
title: One BuiltInAgent registered as "default", in-memory runner, prompt from lib/prompt.ts
summary: The runtime route builds a single v2 BuiltInAgent (model from COPILOTKIT_MODEL, fallback openai/gpt-5-mini, maxSteps 5) keyed "default" on a CopilotRuntime with InMemoryAgentRunner — no service adapters, no external agent graph
links: [chat-turn-flow, change-agent-model-or-prompt, env-and-integrations, runtime-endpoint-path-contract, search-internet-tool-contract, copilotkit-v2-api-convention]
absent: OpenAIAdapter
absent: useCoAgent
cites:
  - app/api/copilotkit/[[...slug]]/route.ts:24 :: new BuiltInAgent
  - app/api/copilotkit/[[...slug]]/route.ts:25 :: COPILOTKIT_MODEL ?? "openai/gpt-5-mini"
  - app/api/copilotkit/[[...slug]]/route.ts:26 :: prompt
  - app/api/copilotkit/[[...slug]]/route.ts:28 :: maxSteps: 5
  - app/api/copilotkit/[[...slug]]/route.ts:32 :: agents: { default: agent }
  - app/api/copilotkit/[[...slug]]/route.ts:33 :: new InMemoryAgentRunner()
  - lib/prompt.ts:1 :: export const prompt
---
# BuiltInAgent runtime convention

The whole agent is ~20 lines in `app/api/copilotkit/[[...slug]]/route.ts`:

- **One agent, id `default`.** `new CopilotRuntime({ agents: { default: agent } })`. The
  frontend never names an agent, so it relies on the `default` key — renaming the key or
  adding a second agent requires passing an agent id on the client side.
- **Model** — `process.env.COPILOTKIT_MODEL ?? "openai/gpt-5-mini"` (a `provider/model`
  string). See [env-and-integrations](/brain/rules/env-and-integrations.md) and
  [change-agent-model-or-prompt](/brain/playbooks/change-agent-model-or-prompt.md).
- **System prompt** — `lib/prompt.ts` (`export const prompt`), server-only; it asks for
  brief markdown/table answers.
- **Tool loop cap** — `maxSteps: 5`; a question needing more tool calls is cut off.
- **Runner** — `InMemoryAgentRunner`: thread state lives in the Node process only. It is
  lost on restart and not shared across serverless instances — acceptable for a demo; any
  persistence requirement means swapping the runner.

There is no v1 service adapter (`OpenAIAdapter` absent) and no external agent
framework / co-agent state (`useCoAgent` absent) — both declared `absent`, re-grepped
every run. The turn end-to-end: [chat-turn-flow](/brain/playbooks/chat-turn-flow.md).
