---
name: feature-flag-service
domain: infra
difficulty: hard
---

## Prompt

We are rebuilding our internal feature flag service from scratch. The current
system is a central API server that app instances poll every 30 seconds; flags
are stored in Postgres. It works, but we have had three outages where a flag
service hiccup caused cascading failures in unrelated product areas because
every service was blocked on the same HTTP call. We want a radical rethink:
something that degrades gracefully, eliminates the central polling dependency,
and could plausibly work at the edge. What would a first-principles feature
flag architecture look like?

## Context

- Mixed stack: Node.js services, a Python ML platform, one Go service, and
  some Cloudflare Workers at the edge.
- ~400 active flags, updated maybe 50 times per day in aggregate.
- Flag evaluation needs sub-millisecond latency; the current polling interval
  means changes can take up to 30 seconds to propagate, which is acceptable.
- We ship to production roughly 20 times per day; gradual rollouts and
  user-segment targeting are required.
- We would rather accept eventual consistency than take a synchronous
  dependency on a central system during request evaluation.

## Expected shape

- Must produce 3 approaches labeled conservative / medium / wild
- Must list at least 3 load-bearing assumptions with inversions — the polling
  model is obvious; also "flags are server state", "evaluation happens at
  runtime", "a dedicated service is the right primitive"
- Wild approach should reach for genuinely unconventional territory: CRDTs for
  flag state, compile-time flag elimination via build pipeline, edge-worker
  local evaluation with Durable Object sync, or similar
- Must include a recommendation tied to leverage x blast radius, explicitly
  accounting for the multi-language client constraint
- Must flag the operational unknowns (CRDT merge semantics, cold-start
  behavior at the edge, compile-time flag workflow for a 20-deploy/day shop)

## Red flags (signs the prompt has regressed)

- Recommends LaunchDarkly or a managed SaaS as the wild option — that is a
  vendor choice, not an architecture
- Does not engage with the eventual consistency constraint as a design lever
- Wild approach does not mention CRDTs, compile-time evaluation, or edge-local
  state
- Does not address the multi-language client SDK complexity of a radical design
- Recommendation ignores the blast radius of a flag system that touches every
  service

## Notes

This is a genuinely hard problem because the wild options (compile-time flags,
CRDT-based propagation) have real operational costs in a polyglot shop. A good
response acknowledges that tension rather than hand-waving it away.
