---
type: Concept
title: Working tree, index, and repository
description: The three areas to reason about when preparing a Git commit.
tags: [git, beginner, staging, commits]
sources:
  - id: progit-recording
    resource: https://git-scm.com/book/en/v2/Git-Basics-Recording-Changes-to-the-Repository
    title: "Pro Git: Recording Changes to the Repository"
  - id: git-glossary
    resource: https://git-scm.com/docs/gitglossary
    title: Git glossary
generated: { by: hermes-agent/gpt-6-luna, at: 2026-09-28T18:40:28Z }
status: draft
---

# The model

Think in terms of three places:

1. **Working tree**: the checked-out files you inspect and edit.
2. **Index**: the proposed contents for the next commit. `git add` copies selected file content into this proposed snapshot; later edits can make the working-tree version differ from the staged version.
3. **Repository history**: committed snapshots and the references that name them.

The common cycle is edit → inspect → stage chosen changes → inspect the staged version → commit. Git distinguishes tracked and untracked files, and tracked files can be modified or staged; `git status` reports these states.[^progit-recording]

# Useful checks

- `git status --short` — compact state of the working tree and index.
- `git diff` — unstaged changes relative to the index.
- `git diff --staged` — staged changes relative to the current commit.
- `git add <path>` — stage the current content of a path.
- `git commit` — record the staged snapshot as a commit.

Before committing, ask: “What exact content is staged?” and inspect `git diff --staged` rather than assuming `git add` staged only what you intended.

# Mental picture

The index is not a separate history or a backup copy. It is the next-commit proposal. The working tree can continue changing after a file is staged, so the staged and unstaged diffs can describe different edits to the same file.[^progit-recording]

Related: [Learning path](/git-learning-path.md), [Objects, commits, and references](/git-objects-and-history.md), and [Practice guide](/git-practice-guide.md).

# Sources

[^progit-recording]: Scott Chacon and Ben Straub, [Pro Git: Recording Changes to the Repository](https://git-scm.com/book/en/v2/Git-Basics-Recording-Changes-to-the-Repository); see also the [Git glossary](https://git-scm.com/docs/gitglossary).