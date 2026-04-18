---
description: Invoke the experimental-engineer subagent for first-principles, Carmack-style alternatives to a hard technical problem.
argument-hint: <problem description>
---

Use the Task tool to invoke the `experimental-engineer` subagent immediately — no preamble, no clarifying questions before dispatching.

Set `subagent_type` to `experimental-engineer`.

If `$ARGUMENTS` is not empty, pass the following as the task description verbatim:

> $ARGUMENTS

If `$ARGUMENTS` is empty, ask the user one brief question: "What problem do you want the experimental engineer to explore?" Then dispatch with their response as the task description.

After the subagent returns, present its full output to the user exactly as returned. Do not summarize, sanitize, or editorialize — the user wants the raw, unfiltered analysis.
