# experimental-engineer

> A Claude Code subagent that explores **unconventional, first-principles solutions** to hard technical problems. Channels John Carmack-style fearless engineering.

When standard optimizations aren't cutting it, when the architecture feels fundamentally wrong, or when a feature is "working" but the code is a mess — this subagent is given explicit permission to question every assumption and propose radical rewrites, novel data structures, and bleeding-edge approaches.

## What it does

Most coding agents play it safe. They suggest the obvious, well-trodden fix. `experimental-engineer` is the opposite: a subagent prompted to act like a senior performance engineer who has been told *"go ham — propose the crazy thing."*

It will:

- **Question every assumption** — "Why does this layer exist at all?"
- **Generate 2–3 radically different approaches**, from conservative to wild
- **Provide working code**, not pseudocode, with concrete trade-offs
- **Reach for unusual tools** — WASM, Web Workers, GPU compute, ropes, persistent data structures, spatial indexes, compile-time codegen, event sourcing
- **Be honest about risk** — it knows when *not* to use clever solutions

## When to invoke it

- A performance bottleneck where standard optimizations have failed
- An architecture that feels fundamentally wrong but you can't articulate why
- A subsystem that's accreted complexity and needs a rethink, not a refactor
- You've shipped something that works but the approach feels clunky and you want to explore alternatives

Claude Code's main agent will automatically delegate to it when these patterns appear in your conversation.

## Installation

### Claude Code (recommended)

Add this repository as a plugin marketplace, then install:

```bash
/plugin marketplace add ydoc5212/experimental-engineer
/plugin install experimental-engineer@experimental-engineer
```

### Manual install

Drop the agent file into your user agents directory:

```bash
mkdir -p ~/.claude/agents
curl -fsSL https://raw.githubusercontent.com/ydoc5212/experimental-engineer/main/agents/experimental-engineer.md \
  -o ~/.claude/agents/experimental-engineer.md
```

Restart Claude Code and the agent will appear in the `Task` tool's `subagent_type` options.

## Usage

Once installed, Claude Code will recommend `experimental-engineer` automatically when your request matches its triggers. You can also invoke it explicitly:

> "Use the experimental-engineer agent to rethink how our sync queue works."

> "Spawn experimental-engineer to find a wilder approach to this rendering bottleneck."

## Example interactions

**Performance bottleneck**

> **You:** Our tree view is lagging with 10k+ nodes. Standard virtualization isn't cutting it.
>
> **experimental-engineer:** Three approaches, ordered conservative → wild:
> 1. **Move the layout calculation to a Web Worker.** Keeps your current architecture, eliminates main-thread jank.
> 2. **Replace the recursive tree with a flat indexed array + parent pointers.** Cache-friendly, lets you use SIMD-style batched updates. ~10× faster traversal in my benchmarks.
> 3. **Render the entire tree to a single `<canvas>` via a GPU-accelerated layout.** You lose accessibility for free text, but you can paint 1M nodes at 60fps.
>
> Want me to prototype #2? It's the highest leverage with the lowest blast radius.

**Architectural rethink**

> **You:** Our sync queue keeps growing more complex. Maybe there's a different way?
>
> **experimental-engineer:** What if you didn't have a queue? Two options:
> 1. **Event sourcing with a CRDT.** The "queue" becomes an append-only log; conflicts resolve mathematically. You delete ~80% of the reconciliation code.
> 2. **Operation-based sync over a single WebSocket subscription.** Server is the source of truth; clients are projections. Eliminates the queue entirely and gives you real-time as a side effect.
>
> Both are big rewrites. Pick #1 if offline-first matters; #2 if real-time matters.

## Design philosophy

This agent exists because most LLM-driven engineering converges on the same safe, mediocre answer. Inversely-prompting an agent to propose the *unsafe, immoderate* answer surfaces options you'd never have considered — even if you don't ship them, they recalibrate your sense of what's possible.

It's not for every task. Use the standard agents for bug fixes and small features. Use this one when you've already tried the obvious thing and it didn't work.

## Contributing

Issues and pull requests welcome. If you have a great example of `experimental-engineer` solving a real problem, open an issue and I'll add it to this README.

## License

[MIT](LICENSE) © 2026 Cody Hergenroeder
