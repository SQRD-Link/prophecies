---
type: Learning Path
title: A practical path to understanding Git
description: Sequence concepts and exercises from everyday change tracking to history internals.
tags: [git, learning, curriculum]
sources:
  - id: progit-book
    resource: https://git-scm.com/book/en/v2
    title: Pro Git book
  - id: git-docs
    resource: https://git-scm.com/docs
    title: Git command reference
  - id: git-remote-branches
    resource: https://git-scm.com/book/en/v2/Git-Branching-Remote-Branches
    title: "Pro Git: Remote Branches"
  - id: git-objects
    resource: https://git-scm.com/book/en/v2/Git-Internals-Git-Objects
    title: "Pro Git: Git Objects"
  - id: git-refs
    resource: https://git-scm.com/book/en/v2/Git-Internals-Git-References
    title: "Pro Git: Git References"
generated: { by: hermes-agent/gpt-6-luna, at: 2026-09-28T18:40:28Z }
status: draft
---

# Suggested sequence

## 1. Observe repository state

Learn the working tree, index (staging area), and last commit. Practice with `git status`, `git diff`, and `git diff --staged`; predict what the next commit would contain before making it. See [Working tree, index, and repository](/git-working-tree-index-repository.md).

## 2. Record snapshots

Stage selected changes, inspect them, and commit. Learn that Git records a project snapshot in a commit and that the index lets you choose what goes into that snapshot.[^progit-book]

## 3. Read history

Use `git log --oneline --graph --decorate --all`, `git show`, and `git diff <commit>..<commit>` to connect commits and file changes. Then inspect object types and references in [Objects, commits, and references](/git-objects-and-history.md).[^git-objects][^git-refs]

## 4. Branch and merge

Create a branch, make a commit, switch back, and merge. Draw the commit graph before and after each operation. Resolve a deliberate conflict in the disposable practice repo. See [Branches and merges](/git-branches-and-merging.md).[^progit-book]

## 5. Remotes and collaboration

After the local model is clear, study remotes, remote-tracking branches, fetch, and push using the relevant Pro Git chapters and command reference. A remote-tracking branch is a local reference to the last observed state of a remote branch; it is not a live connection.[^git-remote-branches]

## 6. Recovery and internals

Use `git reflog` to inspect recent local reference movements and learn recovery concepts. Only practice history-rewriting commands in the sandbox, and create a safety branch first. Consult the command reference for exact behavior; this bundle intentionally does not present destructive commands as copy-paste recipes.[^git-docs]

# How to study

For each stage: explain the model from memory, predict command effects, perform a small task, inspect the resulting state, and explain what changed. Ask an agent for hints and review, but do not request a solution before attempting the task. Use `/git-practice-guide.md` for the starter repository.

# Sources

[^progit-book]: Scott Chacon and Ben Straub, [Pro Git](https://git-scm.com/book/en/v2).
[^git-docs]: [Git reference documentation](https://git-scm.com/docs).
[^git-remote-branches]: [Pro Git: Remote Branches](https://git-scm.com/book/en/v2/Git-Branching-Remote-Branches).
[^git-objects]: [Pro Git: Git Objects](https://git-scm.com/book/en/v2/Git-Internals-Git-Objects).
[^git-refs]: [Pro Git: Git References](https://git-scm.com/book/en/v2/Git-Internals-Git-References).