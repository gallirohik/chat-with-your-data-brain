---
type: "Citation Check"
title: "Citation check report"
description: "checker v2 — all gates pass · 11 warn(s)"
timestamp: 2026-09-29T23:20:33.908Z
---
# Citation check (generated — do not hand-edit) · checker v2

## Resolution (B1): 11/11 ✓
✓ app/api/copilotkit/[[...slug]]/route.ts:41 :: export const GET = handler
✓ app/api/copilotkit/[[...slug]]/route.ts:42 :: export const POST = handler
✓ app/api/copilotkit/[[...slug]]/route.ts:19 :: process.env.TAVILY_API_KEY
✓ app/api/copilotkit/[[...slug]]/route.ts:25 :: process.env.COPILOTKIT_MODEL
✓ app/api/copilotkit/[[...slug]]/route.ts:28 :: maxSteps: 5
✓ components/Dashboard.tsx:40 :: useAgentContext({
✓ components/generative-ui/SearchResults.tsx:37 :: isSafeSearchResultUrl
✓ components/generative-ui/SearchResults.tsx:101 :: rel="noreferrer"
✓ app/layout.tsx:35 :: showDevConsole={false}
✓ .gitignore:34 :: .env*
✓ .mcp.json:7 :: RAFA_MCP_KEY

## Completeness (B2): 0/0 ✓  (0 anchors)

## Policy (contract → anchor declared): 0/0 ✓

## Absence (B3, declared `absent:` re-grepped): 1/1 ✓
✓ absent 'dangerouslySetInnerHTML' → (nowhere — as claimed)

## Inventory (coverage declared vs `git ls-files`): 0/0 ✓

## Warns (heuristic, non-failing — existence-shaped title/summary with no `absent:` declared): 0

## Links (non-failing, OKF §5.3 — dangling cross-links, bundle-wide resolution): 11
⚠ playbooks/security-posture.md — frontmatter → runtime-endpoint-path-contract does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)
⚠ playbooks/security-posture.md — frontmatter → env-and-integrations does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)
⚠ playbooks/security-posture.md — frontmatter → agent-context-contract does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)
⚠ playbooks/security-posture.md — frontmatter → tool-result-rendering-convention does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)
⚠ playbooks/security-posture.md — frontmatter → builtin-agent-runtime-convention does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)
⚠ playbooks/security-posture.md — frontmatter → chat-turn-flow does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)
⚠ playbooks/security-posture.md — frontmatter → repo-toolbox-inventory does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)
⚠ playbooks/security-posture.md — mdlink → /brain/playbooks/add-server-tool-with-renderer.md does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)
⚠ playbooks/security-posture.md — mdlink → /brain/rules/env-and-integrations.md does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)
⚠ playbooks/security-posture.md — mdlink → /brain/rules/agent-context-contract.md does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)
⚠ playbooks/security-posture.md — mdlink → /brain/rules/tool-result-rendering-convention.md does not resolve in the bundle (not-yet-written knowledge, a typo, or a moved file)

**All pass.**
