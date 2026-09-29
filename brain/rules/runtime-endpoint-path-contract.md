---
schemaVersion: 1
id: runtime-endpoint-path-contract
type: contract
domain: api
title: The CopilotKit runtime path is declared in three places that must agree
summary: layout runtimeUrl, the handler basePath and the app/api/copilotkit/[[...slug]] folder all encode /api/copilotkit; useSingleEndpoint={false} needs the optional catch-all
links: [copilotkit-provider-nesting-contract, chat-turn-flow, builtin-agent-runtime-convention, security-posture]
failure: silent
anchor: /api/copilotkit
cites:
  - app/layout.tsx:34 :: runtimeUrl="/api/copilotkit"
  - app/layout.tsx:36 :: useSingleEndpoint={false}
  - app/api/copilotkit/[[...slug]]/route.ts:36 :: createCopilotRuntimeHandler
  - app/api/copilotkit/[[...slug]]/route.ts:38 :: basePath: "/api/copilotkit"
  - app/api/copilotkit/[[...slug]]/route.ts:41 :: export const GET
  - app/api/copilotkit/[[...slug]]/route.ts:42 :: export const POST
---
# Runtime endpoint path

The browser ↔ runtime path is encoded three times:

1. **Client** — `runtimeUrl="/api/copilotkit"` on `<CopilotKit>` in `app/layout.tsx`.
2. **Server handler** — `basePath: "/api/copilotkit"` passed to
   `createCopilotRuntimeHandler` in the route file.
3. **Filesystem** — the route lives at `app/api/copilotkit/[[...slug]]/route.ts`
   (optional catch-all).

Code occurrences of the literal `/api/copilotkit` (markdown excluded): exactly the two
cited lines; the third copy is the folder name, which grep cannot see — check it by hand.

**Why the catch-all matters:** the provider sets `useSingleEndpoint={false}`, i.e. the
client calls several sub-paths under the base (per-agent / info style REST routes rather
than one POST endpoint). The `[[...slug]]` segment is what lets one `route.ts` receive
all of them, and `basePath` is how the handler strips the prefix to dispatch. Renaming the
folder, changing either string, or flipping `useSingleEndpoint` without moving to a
non-catch-all route produces 404s from the chat — the dashboard itself still renders,
so the break is easy to miss (silent).

Both `GET` and `POST` are exported from the same handler; keep both when editing.
The endpoint is unauthenticated — see [security-posture](/brain/playbooks/security-posture.md).
