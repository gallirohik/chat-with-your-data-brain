---
schemaVersion: 1
id: add-server-tool-with-renderer
type: how-to
domain: generative-ui
title: How to add a server-side agent tool with a custom chat renderer
summary: defineTool in the runtime route, add it to the BuiltInAgent tools array, mirror name + zod schema in a useRenderTool call under the provider, build a pure renderer in components/generative-ui, and add its test to the package.json test script
links: [search-internet-tool-contract, tool-result-rendering-convention, builtin-agent-runtime-convention, env-and-integrations, test-runner-convention, security-posture, chat-turn-flow]
absent: useFrontendTool
cites:
  - app/api/copilotkit/[[...slug]]/route.ts:12 :: defineTool
  - app/api/copilotkit/[[...slug]]/route.ts:27 :: tools: [searchInternet]
  - app/api/copilotkit/[[...slug]]/route.ts:28 :: maxSteps: 5
  - components/Dashboard.tsx:61 :: useRenderTool({
  - components/generative-ui/SearchResults.tsx:18 :: type SearchResultsProps
  - components/generative-ui/SearchResults.test.tsx:7 :: renders an in-progress search state
  - package.json:8 :: tsx --test components/generative-ui/SearchResults.test.tsx
---
# Add a server tool with a renderer

Model: the `searchInternet` pair ([search-internet-tool-contract](/brain/rules/search-internet-tool-contract.md)).

1. **Define the tool** in `app/api/copilotkit/[[...slug]]/route.ts` with `defineTool({ name,
   description, parameters: z.object({...}), execute })`. `execute` runs on the server —
   read secrets here via `process.env.<NAME>` and document the name in
   [env-and-integrations](/brain/rules/env-and-integrations.md). The description is what
   the model uses to decide when to call it — write it for the model.
2. **Register** it in the `BuiltInAgent`'s `tools: [...]`. If it tends to chain with
   search, consider whether `maxSteps: 5` is still enough.
3. **Renderer component** in `components/generative-ui/<Name>.tsx`: props
   `{ ...params, status: "executing" | "inProgress" | "complete", result: string | undefined }`;
   no `"use client"`, no hooks; parse `result` with `JSON.parse` + `zod.safeParse` and
   handle the `"Error:"` prefix; allow-list URL schemes for any link
   ([tool-result-rendering-convention](/brain/rules/tool-result-rendering-convention.md)).
4. **Wire the renderer** with `useRenderTool({ name: "<same name>", parameters: <same zod
   schema>, render })` in a client component under `<CopilotKit>` — today all live in
   `Dashboard.tsx`. Name and schema are copied, not shared: keep them identical (or
   extract a shared schema module both files import).
5. **Test** — colocated `<Name>.test.tsx` using `renderToStaticMarkup` for each status and
   the error/plain-text fallbacks, **and append it to the `tsx --test` list in
   `package.json`** or it never runs ([test-runner-convention](/brain/rules/test-runner-convention.md)).
6. **Security** — the tool endpoint is public; a tool that writes or spends money needs a
   gate first ([security-posture](/brain/playbooks/security-posture.md)).

Frontend-only tools (executed in the browser) are a different v2 API and are not used in
this repo yet.
