---
schemaVersion: 1
id: repo-toolbox-inventory
type: convention
domain: toolbox
title: Repo agent toolbox — rafa SOP skills, harness-neutral .agents skills, the rafinery MCP server, hooks and granted permissions
summary: .claude/ carries 13 rafa-* skills, 5 agent cards, the /rafa command, lifecycle hooks and three allow permissions; .agents/skills carries 7 consent-installed skills (tdd, frontend-design, vercel-composition-patterns, ...); .mcp.json registers the rafinery HTTP MCP server keyed by RAFA_MCP_KEY
links: [build-and-deps-convention, test-runner-convention, security-posture]
cites:
  - .claude/settings.json:4 :: Read(.rafa/**)
  - .claude/settings.json:5 :: Bash(npx @rafinery/cli:*)
  - .claude/settings.json:6 :: Bash(rafa:*)
  - .claude/settings.json:10 :: SessionStart
  - .claude/settings.json:16 :: session-start.mjs
  - .claude/settings.json:23 :: Edit|Write|MultiEdit|NotebookEdit
  - .claude/settings.json:41 :: UserPromptSubmit
  - .claude/commands/rafa.md:3 :: description: rafa
  - .mcp.json:3 :: "rafinery"
  - .mcp.json:5 :: https://dev.rafinery.ai/api/mcp
  - .mcp.json:7 :: RAFA_MCP_KEY
  - .agents/skills/tdd/SKILL.md:2 :: name: tdd
  - .agents/skills/frontend-design/SKILL.md:2 :: name: frontend-design
  - .agents/skills/vercel-composition-patterns/SKILL.md:2 :: name: vercel-composition-patterns
  - .agents/skills/requesting-code-review/SKILL.md:2 :: name: requesting-code-review
  - .agents/skills/improve-codebase-architecture/SKILL.md:2 :: name: improve-codebase-architecture
  - .agents/skills/grill-me/SKILL.md:2 :: name: grill-me
  - .agents/skills/grilling/SKILL.md:2 :: name: grilling
  - .claude/skills/rafa-scan/SKILL.md:2 :: name: rafa-scan
  - rafa.json:5 :: "tdd": "1.0.0"
---
# Repo toolbox

Source: `rafa leverage --json` (deterministic extractor) + the config files cited. The
committed toolbox — personal `~/.claude/` is out of scope. Note: at scan time `.claude/`,
`.agents/` and `.mcp.json` are **untracked** on branch `chore/onboard-rafa`.

**Harness-neutral skills — `.agents/skills/` (7, consent-installed, versions in `rafa.json`)**
| skill | use it for |
|---|---|
| `tdd` | red-green-refactor; pairs with `node:test` ([test-runner-convention](/brain/rules/test-runner-convention.md)) |
| `frontend-design` | new UI / restyling — brain design conventions still win |
| `vercel-composition-patterns` | React composition refactors (e.g. the Dashboard monolith) |
| `requesting-code-review` | before merge |
| `improve-codebase-architecture` | deepening-opportunity scan → HTML report |
| `grill-me` / `grilling` | pressure-test a plan one question at a time |

**Claude Code — `.claude/`**
- `skills/`: 13 rafa SOPs (`rafa-scan`, `rafa-plan`, `rafa-build`, `rafa-review`,
  `rafa-improve`, `rafa-security`, `rafa-validate`, `rafa-commit`, `rafa-insights`,
  `rafa-leverage`, `rafa-migrate`, `rafa-okf`, `rafa-sage`).
- `agents/`: atlas, bloom, compass, prism, sage. `commands/rafa.md`: the `/rafa` conductor.
- **Permissions (allow):** `Read(.rafa/**)`, `Bash(npx @rafinery/cli:*)`, `Bash(rafa:*)`.
- **Hooks:** SessionStart → `session-start.mjs`; PostToolUse on edits → `post-tool.mjs`;
  PostToolUse/Stop/SessionEnd → `usage.mjs`; UserPromptSubmit →
  `user-prompt-submit.mjs`; statusLine → `statusline.mjs` (all under `.claude/rafa/hooks/`,
  which also holds git hooks: pre-commit, post-commit, pre-push, post-checkout, post-rewrite).

**MCP — `.mcp.json`:** one HTTP server, `rafinery` (`https://dev.rafinery.ai/api/mcp`),
bearer auth from env name `RAFA_MCP_KEY` (value supplied by the harness env, never
committed — see [security-posture](/brain/playbooks/security-posture.md)).

No project-specific (non-rafa) Claude skills or commands exist yet; app tooling lives in
`package.json` ([build-and-deps-convention](/brain/rules/build-and-deps-convention.md)).
