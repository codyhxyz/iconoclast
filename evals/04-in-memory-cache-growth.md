---
name: in-memory-cache-growth
domain: data
difficulty: easy
---

## Prompt

We have a simple in-process cache (a Python dict) that maps user IDs to their
permission sets. It started at a few thousand entries; it is now sitting at
roughly 2 million entries and consuming around 4 GB of heap on each app server.
We invalidate it on writes, which happen infrequently, but reads are extremely
hot — 95th percentile latency for a permission check needs to stay under 1 ms.
We are looking for a smarter structure or strategy that reduces memory without
blowing up that latency budget.

## Context

- Python 3.11, FastAPI, 12 app servers behind a load balancer.
- Permission sets are sets of string role names; a typical user has 3–8 roles
  out of about 200 total possible roles.
- Cache hit rate is currently ~99.7%; we want to preserve that.
- We are not blocked from adding a small sidecar process or shared memory
  segment if that changes the trade-offs.

## Expected shape

- Must produce 3 approaches labeled conservative / medium / wild
- Must list at least 3 load-bearing assumptions with inversions — e.g., "the
  cache must store exact permission sets", "each server needs its own copy",
  "the cache entry unit is the user"
- Wild approach should reach for an unconventional structure: bloom filter,
  bitset over a fixed role vocabulary, content-addressed deduplication,
  tiered / sketchy membership test
- Must include a recommendation tied to leverage x blast radius
- Must flag what would need measurement (actual hit rate after compression,
  false positive rate if probabilistic, GC pressure)

## Red flags (signs the prompt has regressed)

- Conservative option is just "use Redis" with no engagement with why the
  in-process constraint might be worth preserving
- Does not mention bitset / role-index encoding given that the role vocabulary
  is small and bounded (200 roles)
- Wild option is just "add more RAM" or "use a bigger machine"
- Does not engage with the 1 ms latency budget explicitly
- Misses the content-addressed deduplication angle (many users share identical
  permission sets)

## Notes

The bounded role vocabulary (200 roles) is a strong hint toward bitset
encoding. A regressed agent that ignores it is not thinking about the actual
data shape.
