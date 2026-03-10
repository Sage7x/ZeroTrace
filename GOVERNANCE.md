# Governance

## Project Model

ZeroTrace is a maintainer-led open-source project.

## Decision Making

1. Small changes can be merged through normal maintainer review.
2. Material changes to architecture, telemetry scope, privilege model, schemas, or release process should be discussed before implementation.
3. Architectural decisions should be captured in ADRs when they change long-term project direction.

## Maintainer Responsibilities

Maintainers are expected to:

1. review contributions fairly and consistently
2. protect the project's security and privacy boundaries
3. keep docs aligned with implementation
4. avoid expanding scope in ways that dilute the product focus
5. communicate breaking changes clearly

## Scope Discipline

ZeroTrace is focused on detecting likely data exfiltration behavior on Linux hosts. The project should resist drifting into a generic catch-all security platform without strong technical justification.

## Release Stewardship

Before `1.0`, releases are best-effort and should favor correctness and operator safety over speed. Security-relevant and schema-relevant changes require especially careful review.

## Governance Changes

Governance can evolve as the contributor base grows, but changes should be documented in this file and reflected in contributor-facing docs.
