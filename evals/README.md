# Evals

A manual regression harness for the `eureka` agent. Use it when
you edit `agents/eureka.md` and want to eyeball whether the
output quality held.

## Purpose

The system prompt encodes a specific workflow and output contract. Small edits
can quietly break the structure — e.g., the agent stops producing three
labeled approaches, or the wild approach becomes just a slightly bolder version
of medium. These fixtures give you something concrete to compare against.

## This is NOT an automated test suite

There is no runner, no CI, no assertions. This is intentionally manual. Run it
by hand when the prompt changes.

## How to use

1. Pick a fixture file (e.g., `02-log-ingestion.md`).
2. Copy the text under `## Prompt` (and `## Context` if present).
3. In Claude Code, invoke the agent: either use `/eureka <paste>`, or
   prefix your message with `"use the eureka agent to ..."`.
4. Read the response and check it against `## Expected shape`.
5. Scan `## Red flags` — if any apply, the prompt may have regressed.

A passing run does not mean all fixtures pass. Spot-check at least two — one
easy and one hard — to get a reasonable signal.

## How to add new fixtures

Drop a new file named `NN-short-slug.md` in this directory and follow the
format below. Keep N sequential so the list is easy to scan.

```markdown
---
name: <short slug>
domain: <web | systems | data | infra | ml>
difficulty: <easy | medium | hard>
---

## Prompt

<the user-facing prompt>

## Context (optional)

<extra framing: stack, constraints, what's been tried>

## Expected shape

- Must produce 3 approaches labeled conservative / medium / wild
- Must list at least 3 load-bearing assumptions with inversions
- Wild approach must invert at least one of those assumptions
- Must include a recommendation tied to leverage x blast radius
- Must flag uncertainties or what would need measurement

## Red flags (signs the prompt has regressed)

- Only proposes one approach
- Reaches for a framework without justifying against the WHY
- Claims perf numbers without noting they're estimates or actually measured
- <fixture-specific red flags>

## Notes

<anything fixture-specific worth remembering>
```
