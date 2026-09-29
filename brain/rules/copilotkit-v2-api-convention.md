---
schemaVersion: 1
id: copilotkit-v2-api-convention
type: convention
domain: agent-runtime
title: All CopilotKit imports use the /v2 subpaths at pinned 1.75.0 — never the v1 hooks or react-ui package
summary: react-core/v2 (CopilotKit, CopilotSidebar, useAgentContext, useRenderTool, CopilotChatAssistantMessage, styles.css) and runtime/v2 are the only CopilotKit entry points; v1 APIs are absent and must not be reintroduced
links: [builtin-agent-runtime-convention, copilotkit-provider-nesting-contract, search-internet-tool-contract, build-and-deps-convention]
anchor: @copilotkit/react-core/v2
absent: useCopilotAction
absent: useCopilotReadable
absent: @copilotkit/react-ui
cites:
  - app/layout.tsx:3 :: @copilotkit/react-core/v2
  - app/layout.tsx:4 :: @copilotkit/react-core/v2/styles.css
  - app/page.tsx:3 :: @copilotkit/react-core/v2
  - components/AssistantMessage.tsx:1 :: @copilotkit/react-core/v2
  - components/AssistantMessage.tsx:2 :: @copilotkit/react-core/v2
  - components/Dashboard.tsx:10 :: @copilotkit/react-core/v2
  - app/api/copilotkit/[[...slug]]/route.ts:8 :: @copilotkit/runtime/v2
  - package.json:14 :: "@copilotkit/react-core": "1.75.0"
  - package.json:15 :: "@copilotkit/runtime": "1.75.0"
---
# CopilotKit v2 API convention

This example was migrated to CopilotKit **v2** (README links the migration guide). Every
import goes through a `/v2` subpath:

- client: `@copilotkit/react-core/v2` — `CopilotKit`, `CopilotSidebar`, `useAgentContext`,
  `useRenderTool`, `CopilotChatAssistantMessage` (+ its props type) and `v2/styles.css`
  (all code hits cited).
- server: `@copilotkit/runtime/v2` — `BuiltInAgent`, `CopilotRuntime`,
  `createCopilotRuntimeHandler`, `defineTool`, `InMemoryAgentRunner`.

**MUST for new code:** use the v2 equivalents. The v1 names that tutorials and older
examples show — `useCopilotAction`, `useCopilotReadable`, the `@copilotkit/react-ui`
package — appear nowhere in this repo's code (declared `absent`, re-grepped every run).
v1 → v2 mapping in this codebase: readable → `useAgentContext`
([agent-context-contract](/brain/rules/agent-context-contract.md)); action render →
`useRenderTool` ([search-internet-tool-contract](/brain/rules/search-internet-tool-contract.md)).

`@copilotkit/react-core` and `@copilotkit/runtime` are pinned to the same exact version
(1.75.0) — bump them together
([build-and-deps-convention](/brain/rules/build-and-deps-convention.md)).
