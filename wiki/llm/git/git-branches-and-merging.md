---
type: Concept
title: Branches, HEAD, and merging
description: A branch is a movable name for a commit; merging connects lines of work.
tags: [git, branches, merging, history]
sources:
  - id: progit-branching
    resource: https://git-scm.com/book/en/v2/Git-Branching-Branches-in-a-Nutshell
    title: "Pro Git: Branches in a Nutshell"
  - id: progit-merging
    resource: https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging
    title: "Pro Git: Basic Branching and Merging"
  - id: progit-refs
    resource: https://git-scm.com/book/en/v2/Git-Internals-Git-References
    title: "Pro Git: Git References"
generated: { by: hermes-agent/gpt-6-luna, at: 2026-09-28T18:40:28Z }
status: draft
---

# Branches

A branch is a lightweight, movable reference to a commit. It does not mean Git copied the whole project into a separate directory. `HEAD` identifies the currently checked-out commit or, commonly, the branch reference that will move when a new commit is made.[^progit-branching][^progit-refs]

Create and inspect a branch with `git switch -c <name>`, `git branch --list`, and `git log --oneline --graph --decorate --all`. `git switch` is the modern command for switching branches; `git checkout` remains documented and has broader historical behavior.

# Merging

A merge incorporates work from another line of development. When one history is already an ancestor of the other, Git can move the current branch forward (fast-forward). When the lines have diverged, Git may create a merge commit; conflicting edits require a person to choose the intended result and complete the merge.[^progit-merging]

For learning, predict the commit graph before and after a merge, and inspect it afterward. Practice conflicts only in a disposable repository.

# Related concepts

See [Objects, commits, and references](/git-objects-and-history.md), [Working tree, index, and repository](/git-working-tree-index-repository.md), and the [learning path](/git-learning-path.md).

# Sources

[^progit-branching]: Scott Chacon and Ben Straub, [Pro Git: Branches in a Nutshell](https://git-scm.com/book/en/v2/Git-Branching-Branches-in-a-Nutshell).
[^progit-merging]: Scott Chacon and Ben Straub, [Pro Git: Basic Branching and Merging](https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging).
[^progit-refs]: Scott Chacon and Ben Straub, [Pro Git: Git References](https://git-scm.com/book/en/v2/Git-Internals-Git-References).