# ADR 0001: Local-First Agent With In-Memory Correlation

## Status

Accepted

## Context

ZeroTrace is starting from an empty repository and needs the fastest path to a useful Linux exfiltration detector. Requiring backend infrastructure before the product can detect anything would slow validation, complicate adoption, and distort early feedback.

## Decision

The MVP will:

1. run as a local host agent without requiring a control plane
2. perform behavioral correlation in memory on the endpoint
3. persist alerts locally, but not depend on a local database

## Rationale

1. The core product value is on-host sequence detection, not backend management.
2. Exfiltration sequences are usually time-bounded enough for short in-memory windows.
3. Local-first mode reduces deployment friction for pilots, labs, and incident response usage.
4. It keeps the implementation small enough to harden before expanding into an optional control plane.

## Consequences

Positive:

1. Faster path to a working product
2. Lower operational complexity
3. Easier offline or isolated-environment usage

Negative:

1. Correlation state is lost on restart
2. No cross-host correlation in the MVP
3. Central rule management and multi-host visibility are postponed

## Follow-Up

1. Add a managed-mode API after local detection quality is validated.
2. Revisit durable local queues before remote ingestion becomes a production dependency.
