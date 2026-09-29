---
schemaVersion: 1
id: tool-result-rendering-convention
type: convention
domain: generative-ui
title: Tool renderers receive a status union and a string result — parse defensively, honour the "Error:" prefix, allow-list link schemes
summary: SearchResults is the model renderer — status executing|inProgress|complete, result string|undefined parsed via zod safeParse with plain-text fallback, "Error:"-prefixed results render as errors, only http/https URLs become links
links: [search-internet-tool-contract, add-server-tool-with-renderer, security-posture, test-runner-convention]
anchor: isSafeSearchResultUrl
cites:
  - components/generative-ui/SearchResults.tsx:5 :: searchResponseSchema
  - components/generative-ui/SearchResults.tsx:20 :: "executing" | "inProgress" | "complete"
  - components/generative-ui/SearchResults.tsx:21 :: result: string | undefined
  - components/generative-ui/SearchResults.tsx:30 :: safeParse(JSON.parse(result))
  - components/generative-ui/SearchResults.tsx:37 :: isSafeSearchResultUrl
  - components/generative-ui/SearchResults.tsx:49 :: startsWith("Error:")
  - components/generative-ui/SearchResults.tsx:97 :: isSafeSearchResultUrl
  - components/generative-ui/SearchResults.tsx:101 :: rel="noreferrer"
  - components/generative-ui/SearchResults.tsx:115 :: !searchResponse && result
  - components/generative-ui/SearchResults.test.tsx:99 :: renders a failed v2 tool result as an error
---
# Tool-result rendering convention

`components/generative-ui/` holds the chat-embedded renderers for agent tools.
`SearchResults.tsx` is the one exemplar; copy its shape for new renderers.

- **Props contract.** `status` is the v2 union `"executing" | "inProgress" | "complete"`;
  `result` is `string | undefined` — the tool's return value arrives **serialized**, not as
  the object `execute` returned.
- **Parse defensively.** `JSON.parse` inside `try`, then `zod.safeParse` against a local
  schema; on any failure return `null` and fall back to printing the raw string
  (`!searchResponse && result`). Never assume the shape.
- **Errors are a string prefix.** A failed tool call (e.g. Tavily rejects the request)
  arrives as `status === "complete"` with `result` starting `"Error:"`; the renderer
  shows the error branch instead of "Complete". This is pinned by the test at
  `SearchResults.test.tsx:99`.
- **Links are untrusted.** Search-result URLs come from the internet via the model. Only
  `http:`/`https:` URLs become `<a>` (`isSafeSearchResultUrl`), with
  `target="_blank" rel="noreferrer"`; anything else renders as plain text. New renderers
  that output links MUST reuse this allow-list — see
  [security-posture](/brain/playbooks/security-posture.md).
- **Directive-free and SSR-testable.** The file has no `"use client"` and no hooks, which
  is why it can be unit-tested with `renderToStaticMarkup`
  ([test-runner-convention](/brain/rules/test-runner-convention.md)).
