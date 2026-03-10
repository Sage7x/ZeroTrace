# Contributing to ZeroTrace

ZeroTrace is an open-source security project. Contributions should improve detection quality, runtime safety, operator clarity, or maintainability without turning the agent into a heavyweight generic EDR.

## Before You Start

1. Read [README.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/README.md) and [docs/README.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/README.md).
2. Read [docs/PRD.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/PRD.md) and [docs/TDD.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/TDD.md) before proposing architectural changes.
3. Read [SECURITY.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/SECURITY.md) before reporting or discussing security-sensitive issues publicly.

## Good Contribution Areas

1. Linux collectors
2. detection rules and test fixtures
3. CLI and output ergonomics
4. docs and threat modeling
5. packaging and reproducible builds
6. performance, profiling, and false-positive reduction

## Contribution Workflow

1. Open an issue for significant features, collector backends, schema changes, or rule-pack changes.
2. Keep pull requests focused. Separate large architecture changes from mechanical cleanup.
3. Update docs when behavior, config, schemas, or operator workflows change.
4. Add or update tests for rule changes, config changes, parser changes, and collector behavior.
5. Use an ADR when a change materially alters architecture, privilege model, telemetry scope, or release process.

## Engineering Expectations

1. Prefer small, reviewable changes.
2. Preserve telemetry minimization. Do not add content capture casually.
3. Prefer explicit, testable detection logic over opaque scoring.
4. Keep the local-only experience first-class even if optional control-plane features are added later.
5. Avoid unnecessary dependencies, especially in low-level collector code.

## Pull Request Checklist

1. The change is scoped and explained clearly.
2. Relevant docs are updated.
3. New telemetry fields are justified and documented.
4. Security-sensitive changes call out privilege, secrecy, or data-handling impact.
5. Rule changes include positive and negative validation cases.
6. Breaking changes are called out explicitly.

## Review Standards

Maintainers will prioritize:

1. correctness
2. operator impact
3. detection quality
4. performance
5. security and privacy boundaries
6. long-term maintainability

## Early-Stage Project Note

ZeroTrace is pre-implementation today. In the early phase, design and documentation contributions are as valuable as code contributions because they help lock the right architecture before the collector and rule engine harden.
