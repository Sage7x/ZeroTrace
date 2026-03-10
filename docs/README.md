# ZeroTrace Documentation

ZeroTrace is an open-source, Linux-first endpoint agent focused on detecting suspicious data collection, archiving, and outbound transfer behavior from a single host. This documentation set is written for the current repository state, which is still pre-implementation.

## Repository State

- `Assumption:` The repository is in an early planning phase and is being shaped as an open-source maintainer-led project.
- `Assumption:` The MVP will be a local CLI-first Go agent with optional future self-hosted central management.
- `TBD:` Exact collector implementation details will be finalized after Linux prototype validation across target distributions and kernels.

## Recommended Reading Order

1. [README.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/README.md)
2. [CONTRIBUTING.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/CONTRIBUTING.md)
3. [PRD.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/PRD.md)
4. [TDD.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/TDD.md)
5. [Supported-Platforms.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/Supported-Platforms.md)
6. [Threat-Model.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/Threat-Model.md)
7. [UI-UX-Spec.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/UI-UX-Spec.md)
8. [Security-Compliance.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/Security-Compliance.md)
9. [QA-Test-Plan.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/QA-Test-Plan.md)
10. [Infrastructure-Deployment.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/Infrastructure-Deployment.md)
11. [Observability-Monitoring.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/Observability-Monitoring.md)
12. [DR-IR-Runbooks.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/DR-IR-Runbooks.md)
13. [SECURITY.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/SECURITY.md)
14. [SUPPORT.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/SUPPORT.md)
15. [GOVERNANCE.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/GOVERNANCE.md)
16. [openapi.yaml](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/openapi.yaml)
17. [adr/0001-mvp-local-correlation.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/adr/0001-mvp-local-correlation.md)
18. [adr/0002-telemetry-minimization.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/adr/0002-telemetry-minimization.md)

## Document Index

| File | Purpose |
| --- | --- |
| [PRD.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/PRD.md) | Defines the product problem, target users, MVP scope, roadmap, and release gates. |
| [TDD.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/TDD.md) | Describes the Linux-first agent architecture, collectors, event model, rule engine, and technical tradeoffs. |
| [Supported-Platforms.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/Supported-Platforms.md) | Defines supported Linux targets, privileges, deployment boundaries, and compatibility expectations for this OSS project. |
| [Threat-Model.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/Threat-Model.md) | Captures attacker goals, trust boundaries, attack surfaces, and mitigation priorities for ZeroTrace. |
| [UI-UX-Spec.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/UI-UX-Spec.md) | Specifies the CLI-first experience, terminal output, alert presentation, configuration UX, and optional future self-hosted web-console concepts. |
| [openapi.yaml](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/openapi.yaml) | Future-facing OpenAPI 3.1 contract for an optional self-hosted ZeroTrace control plane. |
| [QA-Test-Plan.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/QA-Test-Plan.md) | Quality strategy for unit, integration, end-to-end, performance, adversary simulation, and release validation. |
| [Infrastructure-Deployment.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/Infrastructure-Deployment.md) | Development setup, packaging, CI/CD, release process, and deployment options for the agent and any future self-hosted control-plane components. |
| [Security-Compliance.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/Security-Compliance.md) | Practical security expectations for telemetry handling, least privilege, dependency hygiene, disclosure, and common compliance alignment. |
| [Observability-Monitoring.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/Observability-Monitoring.md) | Monitoring model for product health, pipeline quality, SLOs, and separation of internal metrics from monitored-host telemetry. |
| [DR-IR-Runbooks.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/DR-IR-Runbooks.md) | Incident severity model, operational recovery procedures, rollback guidance, and postmortem structure. |
| [adr/0001-mvp-local-correlation.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/adr/0001-mvp-local-correlation.md) | Records the decision to start with a local-only agent and in-memory correlation engine for the MVP. |
| [adr/0002-telemetry-minimization.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/docs/adr/0002-telemetry-minimization.md) | Records the decision to minimize telemetry collection and avoid content capture by default. |

## Project Docs Outside `/docs`

| File | Purpose |
| --- | --- |
| [CONTRIBUTING.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/CONTRIBUTING.md) | Contributor workflow, review expectations, and change guidelines. |
| [SECURITY.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/SECURITY.md) | Vulnerability disclosure process and security issue handling for the OSS project. |
| [SUPPORT.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/SUPPORT.md) | Best-effort support expectations, issue routing, and project support boundaries. |
| [GOVERNANCE.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/GOVERNANCE.md) | Maintainer roles, decision-making model, and project governance. |
| [CODE_OF_CONDUCT.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/CODE_OF_CONDUCT.md) | Community participation standards for contributors and maintainers. |

## Status Markers

- `Assumption:` A design choice made because the codebase has not yet fixed the implementation.
- `TBD:` A decision or value that must be resolved during implementation or validation.
- `Future Design:` A feature or interface intentionally left outside the MVP but reserved in the design.
