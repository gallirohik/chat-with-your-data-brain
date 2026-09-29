---
schemaVersion: 1
id: build-and-deps-convention
type: convention
domain: build-tooling
title: pnpm + Next 16.1.7 standalone app — exact pins that move together, a monorepo-leftover turbopack root, and a lint script whose binary is not installed
summary: pnpm is the only package manager (lockfile committed, README-test enforced); next and both @copilotkit packages are exact pins; next.config turbopack.root points three levels up; oxlint is invoked but not a dependency
links: [copilotkit-v2-api-convention, source-text-tests-contract, test-runner-convention, repo-toolbox-inventory]
absent: oxlint@
absent: from "react-hook-form
absent: from "@radix-ui
absent: from "date-fns
absent: from "react-day-picker
absent: from "class-variance-authority
absent: from "@hookform/resolvers
cites:
  - package.json:6 :: next dev --turbopack
  - package.json:7 :: next build
  - package.json:10 :: tsc --noEmit
  - package.json:11 :: oxlint .
  - package.json:14 :: "@copilotkit/react-core": "1.75.0"
  - package.json:15 :: "@copilotkit/runtime": "1.75.0"
  - package.json:27 :: "next": "16.1.7"
  - next.config.ts:5 :: allowedDevOrigins
  - next.config.ts:7 :: path.resolve(__dirname, "../../..")
  - tsconfig.json:7 :: "strict": true
  - readme-runtime.test.mjs:34 :: - pnpm$
---
# Build & dependency convention

Single Next.js app at the repo root (no workspace file). History: imported from the
CopilotKit monorepo, then made standalone (commit c51f08e pinned the CopilotKit packages
and committed `pnpm-lock.yaml`).

- **Package manager: pnpm only.** Lockfile is committed; `readme-runtime.test.mjs` fails if
  the README stops listing `- pnpm` or documents npm/yarn installs.
- **Scripts:** `dev` (`next dev --turbopack`), `build`, `start`, `test`
  ([test-runner-convention](/brain/rules/test-runner-convention.md)), `check-types`
  (`tsc --noEmit`, strict mode), `lint` (`oxlint .`).
- **Exact pins that move together:**
  - `next` `16.1.7` — bumping the major requires the README badge + Node line edits
    ([source-text-tests-contract](/brain/rules/source-text-tests-contract.md)).
  - `@copilotkit/react-core` and `@copilotkit/runtime` both `1.75.0` — client and runtime
    speak one protocol; bump both in one change
    ([copilotkit-v2-api-convention](/brain/rules/copilotkit-v2-api-convention.md)).
- **Path alias** `@/*` → repo root (tsconfig).

**Gotchas (debt, not patterns)**
- `next.config.ts` sets `turbopack.root` to `path.resolve(__dirname, "../../..")` — the
  monorepo root it came from. In the standalone repo that resolves **outside the repo**.
  The standalone commit reports `pnpm build` passing, so it is not currently fatal, but it
  is vestigial; any new Turbopack config MUST NOT build on it — set root to `__dirname`
  or remove it.
- `pnpm lint` runs `oxlint`, but oxlint is not a dependency (no `oxlint@` in the lockfile —
  declared `absent`); it only works with a global install.
- **Unused runtime dependencies:** `react-hook-form`, `@hookform/resolvers`, `@radix-ui/*`,
  `date-fns`, `react-day-picker`, `class-variance-authority` are declared but imported nowhere
  (import forms declared `absent`, re-grepped every run) — leftovers of the shadcn template.
  Don't read their presence as "the house form/date library".
- `allowedDevOrigins: ["127.0.0.1"]` exists so the dev server accepts requests via the IP.
