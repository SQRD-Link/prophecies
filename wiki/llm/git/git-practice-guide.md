---
type: Playbook
title: Git practice repository
description: Safe hands-on exercises for the local disposable repository at ~/git-practice.
tags: [git, practice, exercises]
sources:
  - id: progit-book
    resource: https://git-scm.com/book/en/v2
    title: Pro Git book
  - id: git-reference
    resource: https://git-scm.com/docs
    title: Git command reference
generated: { by: hermes-agent/gpt-6-luna, at: 2026-09-28T18:40:28Z }
status: draft
---

# Sandbox

The local repository at `~/git-practice` is a disposable learning area with an initial commit on `main`. It has no GitHub remote. Keep experiments here, not in a work or homelab repository.

# First exercise: understand the three areas

1. Run `git status` and `git log --oneline --decorate`.
2. Edit `field-notes.md`; inspect `git diff`.
3. Stage it with `git add field-notes.md`; compare `git diff` with `git diff --staged`.
4. Make a second edit without staging it. Identify which version is staged and which remains only in the working tree.
5. Commit only after you can explain what will be recorded. Verify using `git show --stat --oneline HEAD`.

# Next exercises

- Create a branch, make a commit, switch back, and compare the branch graph.
- Make different commits on two branches and merge them.
- Create a deliberate same-line conflict in a scratch file, resolve it, and inspect the resulting history.
- Inspect an object read-only with `git cat-file -t HEAD` and `git cat-file -p HEAD`.
- Inspect `git reflog` after branch switching and commits. Before trying a history-rewriting command, create a backup branch and ask the tutor to explain the exact effect.

# Tutor contract

Ask an agent to give one task at a time and wait for your attempt. It should review your terminal output, explain mistakes, offer hints before a solution, and ask you to predict the effect of the next command. Use `/agent-guide.md` for the reusable learning protocol.

# Safety

Do not add credentials, private files, or real project data. Avoid `git reset --hard`, force-push, and deletion commands until you understand them; this repo has no remote, but careless commands can still remove uncommitted work. Check `git status` before and after each experiment.

# Sources

[^progit-book]: Scott Chacon and Ben Straub, [Pro Git](https://git-scm.com/book/en/v2).
[^git-reference]: [Git reference documentation](https://git-scm.com/docs).