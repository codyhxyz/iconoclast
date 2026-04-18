# iconoclast

> A Claude Code subagent for when Claude is **trying all the wrong things** — drops the safe answer and reaches for the edge of its training data.

<p align="center">
  <img src="./assets/steamed-hams.gif" alt="Steamed hams" width="480">
</p>

When standard optimizations aren't cutting it, when the architecture feels fundamentally wrong, or when a feature is "working" but the code is a mess — this subagent is given explicit permission to question every assumption and propose radical rewrites, novel data structures, and approaches most engineers forget exist.

## What it does

Most coding agents converge on the safe, obvious fix. When you can tell Claude is trying all the wrong things — or when "working" code is clearly the wrong shape — `iconoclast` is the override. It surfaces the assumptions baked into your current approach, inverts them, and builds from there — conservative to wild, reaching for techniques at the **edge of its training data**.

It will:

- **Surface load-bearing assumptions** before proposing anything — so you see why the wild option is wild, not just that it is
- **Generate three approaches** (conservative → medium → wild), each with working code and honest trade-offs
- **Reach for unusual tools** — SIMD, arena allocators, mmap, lock-free structures, coroutines, zero-copy IPC, WASM, Web Workers, GPU compute, CRDTs, content-addressed storage, columnar layouts, compile-time codegen, spatial indexes, persistent data structures
- **Give a direct recommendation** — which to pick, and why, by leverage × blast radius
- **Be honest about risk** — it knows when *not* to use clever solutions, and marks unverified performance claims as estimates

## When to invoke it

- **Claude is stuck on a bug** — looping on the same wrong hypotheses, patching symptoms, or proposing fixes that obviously won't work
- A performance bottleneck where standard optimizations have failed
- An architecture that feels fundamentally wrong but you can't articulate why
- A subsystem that's accreted complexity and needs a rethink, not a refactor
- You've shipped something that works but the approach feels clunky and you want to explore alternatives

Claude Code's main agent will automatically delegate to it when these patterns appear in your conversation.

## Installation

### Claude Code (recommended)

Add this repository as a plugin marketplace, then install:

```bash
/plugin marketplace add codyhxyz/iconoclast
/plugin install iconoclast@iconoclast
```

### Manual install

Drop the agent file into your user agents directory:

```bash
mkdir -p ~/.claude/agents
curl -fsSL https://raw.githubusercontent.com/codyhxyz/iconoclast/main/agents/iconoclast.md \
  -o ~/.claude/agents/iconoclast.md
```

Restart Claude Code and the agent will appear in the `Task` tool's `subagent_type` options.

## Usage

Once installed, Claude Code will recommend `iconoclast` automatically when your request matches its triggers. You can also invoke it explicitly:

> "Use the iconoclast agent to rethink how our sync queue works."

> "Spawn iconoclast to find a wilder approach to this rendering bottleneck."

Or, if the plugin-scoped slash command is installed:

```
/iconoclast our tree view is choking at 10k nodes — standard virtualization isn't cutting it
```

## Examples

> **Example 1:** "Our tree view is lagging with 10k+ nodes. Standard virtualization isn't cutting it." — The agent surfaces three assumptions: that layout happens on the main thread, that the tree is stored hierarchically, that the renderer is the bottleneck. Inversion of assumption 2 drives approach B: replace the recursive tree with a flat indexed array + parent pointers for cache-friendly traversal (est. ~10× faster on random-access patterns). Approach C inverts assumption 1: render to a GPU-accelerated `<canvas>` off-thread via OffscreenCanvas. Recommendation: ship B — highest leverage, surgical blast radius.

> **Example 2:** "Our sync queue keeps growing more complex. Maybe there's a completely different way?" — The agent challenges whether a queue needs to exist at all. Assumption inversion: "what if operations were commutative and order didn't matter?" Approach A: event sourcing — the "queue" becomes an append-only log, server replays on reconnect. Approach C: CRDT-based state — conflicts resolve mathematically, you delete ~80% of reconciliation code. Honest about the rewrite cost; recommendation turns on whether offline-first or real-time latency is the actual constraint.

> **Example 3:** "Our log ingestion pipeline can't keep up with burst traffic — it's choking at ~200k events/sec and we're dropping data." — The agent identifies three assumptions: that logs are parsed at ingest time, that each event is individually acknowledged, that storage is row-oriented. Approach A: move parsing to query time, ingest raw bytes — removes the CPU bottleneck immediately. Approach B: batch acknowledgment with a ring buffer between intake and the writer, amortizing syscall overhead. Approach C (inverts assumption 3): switch to a columnar append-only format (Parquet-style) — compression ratios improve 5–10×, scan queries become trivially parallelizable with SIMD. Recommendation: ship A now, plan C for the next quarter.

> **Example 4:** "This test passes locally but fails in CI 30% of the time — we've tried everything." — The main agent keeps patching symptoms (longer timeouts, retries, more mocks). `iconoclast` names the hidden assumption instead: that the test's clock and the framework's fake-timer clock are the same clock. Inverting it — instrument wall-clock and monotonic time at every await point — exposes two timer systems advancing in different domains, a race invisible to every "obvious" fix. Root cause found in one pass because it stopped asking *"what fix do I apply?"* and started asking *"what am I assuming is true?"*

## Design philosophy

This agent exists because most LLM-driven engineering converges on the same safe, mediocre answer. The fix isn't more enthusiasm — it's procedure: force the model to name what it's assuming, invert those assumptions, and build from there. Even if you don't ship the wild option, it recalibrates your sense of what's possible.

**Why it works:** LLMs have a strong prior toward the most common solution for any given problem shape. That prior is useful 90% of the time and catastrophic the other 10%. Explicitly licensing the weird answer doesn't make the model smarter — it gives it permission to surface options it already knows but wouldn't otherwise volunteer.

It's not for every task. Use the standard agents for bug fixes and small features. Use this one when you've already tried the obvious thing and it didn't work — and you need an iconoclast on the other side of the terminal.

## Contributing

Issues and pull requests welcome. If you have a great example of `iconoclast` solving a real problem, open an issue and I'll add it to this README.

## License

[MIT](LICENSE) © 2026 Cody Hergenroeder
