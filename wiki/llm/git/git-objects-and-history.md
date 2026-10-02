---
type: Concept
title: Git objects, commits, and references
description: How Git stores snapshots and how names point into commit history.
tags: [git, internals, commits, objects, references]
sources:
  - id: progit-objects
    resource: https://git-scm.com/book/en/v2/Git-Internals-Git-Objects
    title: "Pro Git: Git Objects"
  - id: progit-refs
    resource: https://git-scm.com/book/en/v2/Git-Internals-Git-References
    title: "Pro Git: Git References"
  - id: git-glossary
    resource: https://git-scm.com/docs/gitglossary
    title: Git glossary
generated: { by: hermes-agent/gpt-6-luna, at: 2026-09-28T18:40:28Z }
status: draft
---

# Objects

Git's object database is content-addressable: object identity is derived from the object content and type. The core object types are **blob** (file content), **tree** (a directory snapshot mapping names to modes and objects), **commit** (a snapshot plus metadata and parent commit references), and **tag** (a tag object containing a name, target object, tagger metadata, and message; annotated tags use this object, while lightweight tags are references).[^progit-objects]

A commit points to a tree representing the project state and to zero or more parent commits. History is therefore a graph of commits, not a sequence of patches that Git must replay to reconstruct the current tree.[^progit-objects]

# References and HEAD

A branch name is a movable reference to a commit. Creating a branch creates a name at a commit; committing while that branch is checked out advances the branch reference. `HEAD` identifies the currently checked-out location, commonly through a symbolic reference to a branch.[^progit-refs]

A useful inspection set is:

- `git log --oneline --graph --decorate --all` — view reachable commit history and refs.
- `git show <commit>` — inspect a commit and its changes.
- `git cat-file -t <object>` — ask Git for an object's type.
- `git cat-file -p <object>` — inspect its readable representation.

Run plumbing commands as observation in the sandbox first. Object storage layout and hash algorithms are implementation details; use the official docs for version-specific details rather than relying on a simplified mental model.

# What to remember

- Commits identify snapshots and connect history through parent links.
- Branches are names pointing into the graph, not independent copies of files.
- Moving a branch reference changes what that branch name denotes; it does not mutate the commit object itself.[^progit-refs]

Related: [Working tree, index, and repository](/git-working-tree-index-repository.md), [Branches and merges](/git-branches-and-merging.md), and [Learning path](/git-learning-path.md).

# Sources

[^progit-objects]: Scott Chacon and Ben Straub, [Pro Git: Git Objects](https://git-scm.com/book/en/v2/Git-Internals-Git-Objects).
[^progit-refs]: Scott Chacon and Ben Straub, [Pro Git: Git References](https://git-scm.com/book/en/v2/Git-Internals-Git-References); see also the [Git glossary](https://git-scm.com/docs/gitglossary).