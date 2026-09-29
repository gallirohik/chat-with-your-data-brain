---
schemaVersion: 1
id: search-internet-tool-contract
type: contract
domain: generative-ui
title: searchInternet — server tool name and parameter schema are mirrored in the client renderer
summary: defineTool in the runtime route and useRenderTool in Dashboard.tsx must share the name "searchInternet" and the { query: string } schema; a mismatch silently drops the custom UI
links: [tool-result-rendering-convention, add-server-tool-with-renderer, chat-turn-flow, env-and-integrations, agent-context-contract]
failure: silent
anchor: searchInternet
cites:
  - app/api/copilotkit/[[...slug]]/route.ts:12 :: const searchInternet = defineTool
  - app/api/copilotkit/[[...slug]]/route.ts:13 :: name: "searchInternet"
  - app/api/copilotkit/[[...slug]]/route.ts:16 :: query: z.string()
  - app/api/copilotkit/[[...slug]]/route.ts:20 :: maxResults: 5
  - app/api/copilotkit/[[...slug]]/route.ts:27 :: tools: [searchInternet]
  - components/Dashboard.tsx:62 :: name: "searchInternet"
  - components/Dashboard.tsx:64 :: query: z.string()
  - components/Dashboard.tsx:66 :: render: ({ parameters, status, result })
  - components/Dashboard.tsx:68 :: <SearchResults
---
# searchInternet tool contract

The one agent tool is **defined and executed on the server** and **rendered on the client**:

| side | site | what it declares |
|---|---|---|
| server | route file `defineTool({ name: "searchInternet", parameters: z.object({ query }) , execute })` | name, schema, Tavily call |
| server | `tools: [searchInternet]` on the BuiltInAgent | registration |
| client | `useRenderTool({ name: "searchInternet", parameters: z.object({ query }), render })` in `Dashboard.tsx` | how the call appears in chat |

All code hits of `searchInternet` are cited (markdown excluded).

**Must stay in sync:**
- **Name.** The client renderer is matched to tool calls by name. Rename one side only
  and the chat falls back to the default tool-call display — no error anywhere.
- **Parameter schema.** The `z.object({ query: z.string() ... })` is written twice
  (server lines 15-17, client lines 63-65). There is no shared module; adding a parameter
  server-side without mirroring it leaves `parameters.<new>` untyped/undefined in the
  renderer.
- **Result shape.** `execute` returns Tavily's `client.search(...)` object; the renderer
  receives it as a **string** (`result`) and `SearchResults` parses it as JSON with a
  schema of `{ answer?, results[{ title, url, content }] }` — see
  [tool-result-rendering-convention](/brain/rules/tool-result-rendering-convention.md).
  Changing what `execute` returns (e.g. `maxResults`, mapping fields) must be checked
  against that schema.

To add another tool with a custom renderer follow
[add-server-tool-with-renderer](/brain/playbooks/add-server-tool-with-renderer.md).
