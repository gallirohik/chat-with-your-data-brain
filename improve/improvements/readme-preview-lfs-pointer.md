---
id: readme-preview-lfs-pointer
type: Improvement
schemaVersion: 1
priority: P3
category: product
status: open
title: "README preview.gif is a Git LFS pointer, so the README image is broken"
summary: "preview.gif is a 133-byte LFS pointer text file with no .gitattributes or LFS object in this repo - the README hero image renders broken"
fix: "Re-add the real GIF (fetch the LFS object from the CopilotKit source repo) as a normal file or with LFS tracked, or point the README at a hosted image"
leverage: { impact: low, effort: low }
blast_radius: [build-tooling]
cites:
  - README.md:10 :: ./preview.gif
  - preview.gif:1 :: git-lfs
found: 2026-09-30
description: "preview.gif is a 133-byte LFS pointer text file with no .gitattributes or LFS object in this repo - the README hero image renders broken"
tags: [product, P3]
timestamp: 2026-09-30
---
# Broken README preview

Verified: `preview.gif` contains the text `version https://git-lfs.github.com/spec/v1` with
`size 21412280` — a pointer, not an image — and the repo has no `.gitattributes`. The
standalone import copied the pointer from the monorepo without the LFS object, so the first
thing a visitor sees on the README is a broken image. Low impact, five-minute fix.

<!-- okf:citations:start (generated — the frontmatter `cites:` DSL is the source of truth; do not hand-edit) -->

# Citations

[1] [README.md:10](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/README.md#L10) — `./preview.gif`
[2] [preview.gif:1](https://github.com/gallirohik/chat-with-your-data/blob/31a001b8ffeeefed212be67855c60ffe88ac0d29/preview.gif#L1) — `git-lfs`

<!-- okf:citations:end -->
