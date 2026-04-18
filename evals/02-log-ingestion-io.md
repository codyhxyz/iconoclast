---
name: log-ingestion-io
domain: systems
difficulty: hard
---

## Prompt

Our log ingestion service processes about 400 GB of structured JSON logs per
hour from 600 microservices. It parses each line, enriches it with service
metadata, and writes to an append-only columnar store. We are hitting IO
saturation on the ingestion nodes at around 320 GB/hr — before we even get to
the columnar write. Profiling shows 60% of wall time is in JSON
deserialization and re-serialization for enrichment. We have already tried
faster JSON libraries (simdjson) and batching writes. What is a
fundamentally different way to structure this pipeline?

## Context

- Go service, runs on bare metal, 32-core machines with NVMe SSDs.
- The columnar store is ClickHouse; the schema is relatively stable but
  occasionally gains new top-level fields.
- Downstream consumers need the enriched data within 5 seconds of ingestion.
- The microservices currently emit JSON because that was the path of least
  resistance; changing the wire format is politically feasible if we can
  provide a drop-in client library.

## Expected shape

- Must produce 3 approaches labeled conservative / medium / wild
- Must list at least 3 load-bearing assumptions with inversions; JSON-on-the-
  wire is the obvious one but not the only one
- Wild approach must invert a fundamental assumption (e.g., that enrichment
  happens at ingest time, or that the ingestion tier parses the payload at all)
- Must include a recommendation tied to leverage x blast radius
- Must flag what would actually need measurement (e.g., whether IO saturation
  is network, disk, or bus; whether ClickHouse is the actual bottleneck)

## Red flags (signs the prompt has regressed)

- Only proposes switching JSON libraries or adding more partitions
- Does not challenge the parse-enrich-write ordering assumption
- Recommends a managed SaaS solution (Datadog, Splunk) without acknowledging
  the prompt is asking for architectural alternatives, not vendor swaps
- Wild approach is just "use Kafka" — that's standard, not wild
- Claims throughput numbers (e.g., "this will handle 2 TB/hr") without flagging
  them as estimates pending measurement

## Notes

The hard part here is that multiple assumptions are load-bearing simultaneously
(wire format, enrichment timing, parsing at ingestion, the columnar-write path).
A regressed agent tends to only invert one and present the others as fixed.
