---
schemaVersion: 1
id: copilotkit-css-override-contract
type: contract
domain: design-system
title: CopilotKit theming works only because globals.css is imported after the v2 stylesheet and scopes vars to [data-copilotkit]
summary: layout.tsx imports @copilotkit/react-core/v2/styles.css before ./globals.css; globals.css redefines --background/--primary/... under [data-copilotkit] (and its .dark forms) — reorder the imports and the overrides lose the cascade
links: [copilotkit-provider-nesting-contract, design-tokens-convention, copilotkit-v2-api-convention]
failure: silent
anchor: data-copilotkit
cites:
  - app/layout.tsx:4 :: @copilotkit/react-core/v2/styles.css
  - app/layout.tsx:5 :: import "./globals.css"
  - app/globals.css:83 :: [data-copilotkit] {
  - app/globals.css:96 :: .dark [data-copilotkit]
  - app/globals.css:97 :: [data-copilotkit].dark
  - app/globals.css:158 :: .copilotKitSidebar .copilotKitWindow
  - app/globals.css:162 :: .copilotKitButton
  - app/globals.css:168 :: .copilotKitMessage.copilotKitUserMessage
  - components/AssistantMessage.tsx:10 :: className="rounded-lg border border-gray-200 bg-white
---
# CopilotKit CSS override contract

Two mechanisms theme the chat sidebar:

1. **CSS variables scoped to `[data-copilotkit]`** (globals.css:83, and the `.dark` forms
   at 96-97) — same variable names as the app tokens (`--background`, `--primary`,
   `--border`, `--radius` …) but blue/slate values, so the chat can differ from the
   stone app theme. All code hits of `data-copilotkit` are cited.
2. **Per-component className** — `CustomAssistantMessage` passes Tailwind classes to
   `CopilotChatAssistantMessage` (AssistantMessage.tsx:10), wired via
   `messageView={{ assistantMessage: ... }}` on the sidebar.

**Ordering invariant:** `app/layout.tsx` imports `@copilotkit/react-core/v2/styles.css`
(line 4) **before** `./globals.css` (line 5). The overrides win only because they come
later with equal specificity. Swapping the two imports, or importing the library CSS
from a component, silently reverts the sidebar to library defaults.

**Unverified legacy selectors:** `.copilotKitSidebar .copilotKitWindow`,
`.copilotKitButton`, `.copilotKitMessage.copilotKitUserMessage` (globals.css:158-170) are
v1-era class names. This scan did not verify that v2 components still emit them; treat
them as possibly dead before relying on them, and prefer the `[data-copilotkit]` vars.
