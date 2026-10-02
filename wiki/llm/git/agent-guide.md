---
type: Reference
title: Agent and contributor guide
description: Rules for consuming and maintaining this OKF Git-learning bundle.
tags: [git, learning, agent-guidance, okf]
sources:
  - id: okf-spec
    resource: https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md
    title: Google Open Knowledge Format specification
  - id: progit-book
    resource: https://git-scm.com/book/en/v2
    title: Pro Git book
generated: { by: hermes-agent/gpt-6-luna, at: 2026-09-28T18:40:28Z }
status: draft
---

# Purpose

This directory is a portable Git-learning knowledge bundle for people and agents. It follows OKF v0.2: Markdown concepts, YAML frontmatter, standard Markdown links, and optional directory `index.md` / `log.md` files.[^okf-spec]

# Reading protocol

1. Read `/index.md`, then this guide and `/git-learning-path.md`.
2. Open only the concept pages needed for the current question or lesson; follow links when prerequisites are missing.
3. Treat cited primary documentation as authoritative when a summary and current Git documentation differ. The source map identifies relevant manual sections.
4. Distinguish sourced facts from teaching analogies. Never invent an answer to fill a wiki gap; say what is missing and consult the cited source.

# Tutor protocol

- Begin with the learner's goal and a short diagnostic question, not a lecture dump.
- Teach one concept at a time. Give an exercise with observable success criteria and wait for the learner's attempt before showing a solution.
- Prefer the local sandbox described in `/git-practice-guide.md`. Explain the effect and risk before any command that rewrites history; keep destructive experiments out of real repositories.
- Review the learner's actual commands/output or explanation. Give graduated hints before a full answer, then ask the learner to explain the model in their own words.
- Record progress and misconceptions separately from concept pages, with the learner's consent. Do not infer mastery from reading or a correct multiple-choice answer alone.

# Editing and trust rules

- Follow the OKF spec linked in `sources`: `type` is required; preserve unknown frontmatter fields; use standard Markdown links; cite claims with footnotes whose labels match `sources[].id`.
- Add a `sources` entry for every external source used. Prefer the official Pro Git book and `git-scm.com/docs` for Git semantics.
- Set `generated` when content is generated or meaningfully changed. Use the actual producer and timestamp; timestamps are UTC ISO 8601.
- Keep new or materially revised pages at `status: draft` until checked against their sources. Do not add `verified` unless an actual verification event took place; never label machine review as human review.
- Keep claims about version-dependent behavior scoped to the cited Git version or mark them as needing a current check.
- Update `index.md` and append a date-grouped entry to `log.md` when adding, renaming, or deprecating concepts. Preserve the OKF reserved names: `index.md` and `log.md` are not concept pages.
- Do not copy large portions of source books into the bundle. Summarize in your own words and link to the original.

# Sources

[^okf-spec]: Google Cloud Platform, [Open Knowledge Format specification](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md).
[^progit-book]: Scott Chacon and Ben Straub, [Pro Git](https://git-scm.com/book/en/v2), online edition.