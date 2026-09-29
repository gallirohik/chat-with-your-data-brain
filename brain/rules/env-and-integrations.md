---
schemaVersion: 1
id: env-and-integrations
type: convention
domain: external-integrations
title: Env vars and external services — TAVILY_API_KEY and COPILOTKIT_MODEL are read in the runtime route; the LLM key is consumed inside the runtime library
summary: Repo code reads exactly two env names, both server-side in the runtime route; OPENAI_API_KEY (README) is never read by repo code — the provider in @copilotkit/runtime picks it up; no NEXT_PUBLIC_ vars
links: [builtin-agent-runtime-convention, search-internet-tool-contract, security-posture, change-agent-model-or-prompt]
anchor: process.env
absent: OPENAI_API_KEY
absent: NEXT_PUBLIC_
cites:
  - app/api/copilotkit/[[...slug]]/route.ts:1 :: import { tavily } from "@tavily/core"
  - app/api/copilotkit/[[...slug]]/route.ts:19 :: process.env.TAVILY_API_KEY
  - app/api/copilotkit/[[...slug]]/route.ts:25 :: process.env.COPILOTKIT_MODEL
  - package.json:21 :: "@tavily/core"
  - README.md:48 :: OPENAI_API_KEY
  - README.md:49 :: TAVILY_API_KEY
---
# Env vars and integrations

Names only — values live in an untracked `.env` (gitignored via `.env*`), never read here.

| env name | read at | purpose | if missing |
|---|---|---|---|
| `TAVILY_API_KEY` | route.ts:19, inside `searchInternet.execute` | Tavily web search (`@tavily/core`) | only fails when the agent calls the tool; the failure reaches the chat as an `"Error:"` tool result ([tool-result-rendering-convention](/brain/rules/tool-result-rendering-convention.md)) |
| `COPILOTKIT_MODEL` | route.ts:25 | `provider/model` id for the BuiltInAgent | optional — falls back to `openai/gpt-5-mini` |
| `OPENAI_API_KEY` | **not read by repo code** (declared `absent`) | documented in README:48 | consumed implicitly by the runtime's OpenAI provider (inferred from the `openai/` model prefix — library internals not verified by this scan); a missing key fails every chat turn |

All `process.env` code sites are cited (the anchor). The client bundle reads no env at all
(`NEXT_PUBLIC_` declared `absent`) — keys are server-only by construction; see
[security-posture](/brain/playbooks/security-posture.md).

**Gotchas**
- `TAVILY_API_KEY` is read **per call**, inside `execute` — the client is constructed on
  every search, so a missing key doesn't fail at boot.
- Switching `COPILOTKIT_MODEL` to another provider prefix will need that provider's key,
  which the README does not document. See
  [change-agent-model-or-prompt](/brain/playbooks/change-agent-model-or-prompt.md).
- `COPILOTKIT_MODEL` is not in the README's `.env` example.

External services: the LLM provider (via `@copilotkit/runtime`) and Tavily. No database,
no auth provider, no analytics.
