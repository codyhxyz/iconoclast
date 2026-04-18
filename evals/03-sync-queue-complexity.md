---
name: sync-queue-complexity
domain: systems
difficulty: medium
---

## Prompt

Our background sync queue started as a simple job table in Postgres. Three
years later it has grown into a 4,000-line state machine with 14 job statuses,
retry logic spread across five services, a dead-letter queue, a manual replay
tool, and an incident last quarter where a cascade of retries took down the
primary DB. Every time we add a new sync type we touch all five services. Is
there a fundamentally different way to model this that does not compound in
complexity the way a job queue does?

## Context

- Python monorepo, Postgres 15, internal tooling product (B2B SaaS).
- ~50,000 sync jobs per day, peak 800 concurrent.
- The queue drives data sync from customer integrations (Salesforce, HubSpot,
  etc.) into our data model — each job is a unit of customer data.
- Correctness matters more than throughput; customers notice stale data after
  ~30 seconds.

## Expected shape

- Must produce 3 approaches labeled conservative / medium / wild
- Must list at least 3 load-bearing assumptions with inversions — the obvious
  one is "we need a job queue"; others include "sync is push not pull",
  "state lives in Postgres", "retry is the right recovery model"
- Wild approach must invert a load-bearing assumption meaningfully
- Must include a recommendation tied to leverage x blast radius
- Must flag correctness risks honestly — this is customer data

## Red flags (signs the prompt has regressed)

- Recommends a specific job queue library (Celery, Sidekiq, BullMQ) as the
  wild option — that is a conservative option at best
- Does not question whether a queue is the right model at all
- Does not mention event sourcing, CQRS, CRDTs, or append-only log as
  candidates for the wild slot
- Glosses over the correctness / stale-data constraint
- Recommendation ignores blast radius for a system touching customer data

## Notes

This fixture is deliberately close to the sync-queue example in the project
README. It is a known-good sanity check — if this one regresses, the core
prompt contract has broken.
