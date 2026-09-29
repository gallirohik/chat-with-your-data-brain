---
schemaVersion: 1
id: change-agent-model-or-prompt
type: how-to
domain: agent-runtime
title: How to change the agent's model, system prompt or step budget
summary: Model via COPILOTKIT_MODEL env (provider/model string, default openai/gpt-5-mini) — no code change; prompt text in lib/prompt.ts; step cap maxSteps in the runtime route; a new provider prefix needs that provider's key
links: [builtin-agent-runtime-convention, env-and-integrations, chat-turn-flow]
cites:
  - app/api/copilotkit/[[...slug]]/route.ts:25 :: COPILOTKIT_MODEL ?? "openai/gpt-5-mini"
  - app/api/copilotkit/[[...slug]]/route.ts:28 :: maxSteps: 5
  - lib/prompt.ts:1 :: export const prompt
---
# Change model / prompt / step budget

- **Model** — set `COPILOTKIT_MODEL` in the server env (`provider/model`, e.g. the default
  `openai/gpt-5-mini`). No code change. A different provider prefix requires that
  provider's API key in the env; which env name the runtime reads is library behaviour,
  not repo code — check the CopilotKit docs and add the name to
  [env-and-integrations](/brain/rules/env-and-integrations.md) and the README.
- **Prompt** — edit the template string in `lib/prompt.ts`. It is server-only and imported
  by the route; the data itself is NOT in the prompt — it arrives via agent context.
- **Step budget** — `maxSteps` on the `BuiltInAgent` bounds model↔tool iterations per turn.

Runtime shape and constraints: [builtin-agent-runtime-convention](/brain/rules/builtin-agent-runtime-convention.md).
