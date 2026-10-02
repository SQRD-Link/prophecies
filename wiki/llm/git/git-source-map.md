---
type: Reference
title: Primary-source map for learning Git
description: Official documentation reading list, ordered by the concepts it supports.
tags: [git, sources, documentation]
sources:
  - id: progit-book
    resource: https://git-scm.com/book/en/v2
    title: Pro Git book
  - id: git-reference
    resource: https://git-scm.com/docs
    title: Git reference documentation
  - id: git-glossary
    resource: https://git-scm.com/docs/gitglossary
    title: Git glossary
generated: { by: hermes-agent/gpt-6-luna, at: 2026-09-28T18:40:28Z }
status: draft
---

# Recommended primary sources

## Main learning text: Pro Git

Scott Chacon and Ben Straub's [Pro Git, second edition](https://git-scm.com/book/en/v2) is the main guided source; the full book is available online. Read selectively rather than trying to memorize every command.

- [Recording Changes to the Repository](https://git-scm.com/book/en/v2/Git-Basics-Recording-Changes-to-the-Repository) — tracked/untracked files, staging, status, and commits.
- [Branches in a Nutshell](https://git-scm.com/book/en/v2/Git-Branching-Branches-in-a-Nutshell) — branch pointers and `HEAD`.
- [Basic Branching and Merging](https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging) — branch workflow, merges, and conflicts.
- [Git Objects](https://git-scm.com/book/en/v2/Git-Internals-Git-Objects) — blobs, trees, commits, tags, and content-addressable storage.
- [Git References](https://git-scm.com/book/en/v2/Git-Internals-Git-References) — branch and tag references.
- [Git Refspec](https://git-scm.com/book/en/v2/Git-Internals-The-Refspec) — useful after learning remotes.

## Command reference

Use the [Git reference documentation](https://git-scm.com/docs) to check exact command behavior and options. Useful pages include [`git`](https://git-scm.com/docs/git), [`gitglossary`](https://git-scm.com/docs/gitglossary), [`gitrevisions`](https://git-scm.com/docs/gitrevisions), [`gitreflog`](https://git-scm.com/docs/gitreflog), and [`git-reset`](https://git-scm.com/docs/git-reset). Treat reference pages as precise command documentation, not as the first tutorial.

## Source policy

Prefer the official Git book and manual over unsourced summaries for semantics. For any new claim, consult the relevant chapter or command page and add that URL to the concept's `sources` frontmatter. This bundle paraphrases; it does not mirror the book text.

Related: [Learning path](/git-learning-path.md), [Agent guide](/agent-guide.md), and [Objects, commits, and references](/git-objects-and-history.md).

# Sources

[^progit-book]: Scott Chacon and Ben Straub, [Pro Git](https://git-scm.com/book/en/v2).
[^git-reference]: [Git reference documentation](https://git-scm.com/docs).
[^git-glossary]: [Git glossary](https://git-scm.com/docs/gitglossary).