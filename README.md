# eureka

> A subagent for when Claude is **trying all the wrong things**. Use it when you need Claude to think outside the box. This is my "eureka" plugin, my #1 most helpful plugin in daily use.

<p align="center">
  <img src="./assets/steamed-hams.gif" alt="Steamed hams" width="480">
</p>

Most coding agents loop on the same three hypotheses until you give up. When standard optimizations aren't working, when the architecture feels wrong but you can't say why, or when the code "works" but the shape is off, `eureka` is the override. It has explicit permission to question the assumptions you didn't know you were making and propose rewrites the main agent would never volunteer.

## What it does

When you can tell Claude is flailing, `eureka` stops and asks a different question: *what am I assuming is true?* Then it inverts those assumptions and builds from there, from conservative to wild.

It will:

- Surface the load-bearing assumptions first, so you see why the weird option is weird instead of being handed it out of nowhere
- Generate three approaches (conservative, medium, wild) with working code and honest trade-offs for each
- Reach for tools the default agent forgets exist: SIMD, arena allocators, mmap, lock-free structures, coroutines, zero-copy IPC, WASM, Web Workers, GPU compute, CRDTs, content-addressed storage, columnar layouts, compile-time codegen, spatial indexes, persistent data structures
- Give you a direct recommendation based on leverage vs. blast radius, not a neutral menu
- Be honest about when *not* to do the clever thing, and flag any performance number it didn't measure as an estimate

## When to invoke it

- Claude is stuck on a bug, looping on the same wrong hypotheses, or patching symptoms
- A performance bottleneck where the obvious optimizations already failed
- An architecture that feels wrong but you can't articulate why
- A subsystem that's accreted complexity and needs a rethink, not another refactor
- Something ships, it works, but the approach feels clunky and you want to know what else was on the table

Claude Code's main agent will usually delegate to it on its own when these patterns show up in your conversation.

## Installation

### Claude Code (recommended)

Add this repo as a plugin marketplace, then install:

```bash
/plugin marketplace add codyhxyz/eureka
/plugin install eureka@eureka
```

### Manual install

Drop the agent file into your user agents directory:

```bash
mkdir -p ~/.claude/agents
curl -fsSL https://raw.githubusercontent.com/codyhxyz/eureka/main/agents/eureka.md \
  -o ~/.claude/agents/eureka.md
```

Restart Claude Code and the agent will show up in the `Task` tool's `subagent_type` options.

## Usage

Once installed, Claude Code will recommend `eureka` when your request matches its triggers. You can also call it explicitly:

> "Use the eureka agent to rethink how our sync queue works."

> "Spawn eureka to find a wilder approach to this rendering bottleneck."

Or, if the plugin-scoped slash command is installed:

```
/eureka our tree view is choking at 10k nodes — standard virtualization isn't cutting it
```

## Examples

> **Example 1:** "Our tree view is lagging at 10k+ nodes. Standard virtualization isn't cutting it." The agent names three assumptions: layout happens on the main thread, the tree is stored hierarchically, the renderer is the bottleneck. Inverting #2 gives you approach B — replace the recursive tree with a flat indexed array plus parent pointers for cache-friendly traversal (estimated ~10x on random-access patterns). Approach C inverts #1 and renders to a GPU-accelerated `<canvas>` off-thread via OffscreenCanvas. Recommendation: ship B. Highest leverage, smallest blast radius.

> **Example 2:** "Our sync queue keeps getting more complex. Maybe there's a different way?" The agent asks whether a queue needs to exist at all. Inversion: what if operations were commutative and order didn't matter? Approach A: event sourcing, where the "queue" becomes an append-only log and the server replays on reconnect. Approach C: CRDT-based state, where conflicts resolve mathematically and you delete ~80% of the reconciliation code. Honest about the rewrite cost. The recommendation depends on whether your actual constraint is offline-first or real-time latency.

> **Example 3:** "Our log pipeline can't keep up with burst traffic. Choking at ~200k events/sec and dropping data." Three assumptions: logs are parsed at ingest time, each event is individually acked, storage is row-oriented. Approach A: parse at query time, ingest raw bytes, CPU bottleneck gone. Approach B: batch acks with a ring buffer between intake and writer, amortizing syscalls. Approach C (inverts #3): switch to a columnar append-only format like Parquet. Compression gets 5–10x better and scan queries parallelize trivially with SIMD. Recommendation: ship A now, plan C for next quarter.

> **Example 4:** "This test passes locally but fails in CI 30% of the time. We've tried everything." The main agent keeps patching symptoms: longer timeouts, retries, more mocks. `eureka` stops and names the hidden assumption instead: that the test's clock and the framework's fake-timer clock are the same clock. Instrumenting wall-clock and monotonic time at every await point exposed two timer systems advancing in different domains — a race invisible to every obvious fix. Root cause found in one pass because it stopped asking "what fix do I apply?" and started asking "what am I assuming is true?"

## Why it works

Most LLM-driven engineering converges on the same safe, mediocre answer. The fix isn't more enthusiasm, it's procedure: force the model to name what it's assuming, invert those assumptions, and build from there. Even if you don't ship the wild option, you end up recalibrated about what's possible.

LLMs have a strong prior toward the most common solution for any given problem shape. That prior is useful 90% of the time and catastrophic the other 10%. Explicitly licensing the weird answer doesn't make the model smarter. It gives it permission to surface options it already knows but wouldn't otherwise volunteer.

It's not for every task. Use the standard agents for bug fixes and small features. Use this one when you've already tried the obvious thing and you need someone on the other side of the terminal willing to throw the whole approach out.

## Contributing

Issues and PRs welcome. If you've used `eureka` on a real problem and it worked, open an issue with the story and I'll add it here.

## License

[MIT](LICENSE) © 2026 Cody Hergenroeder
