# ZeroTrace Infrastructure and Deployment Guide

## Scope

This guide covers local development, packaging, CI/CD, release management, and deployment patterns for ZeroTrace. The current repository has no implementation yet, so environment and pipeline details are prescriptive rather than descriptive.

- `Assumption:` ZeroTrace will be built as a Go service and CLI binary.
- `Assumption:` The first deployment target is a Linux host running `systemd`.
- `Future Design:` Optional self-hosted control-plane services may be added after the local agent MVP.

## Open Source Delivery Model

1. The source repository is the canonical project artifact.
2. Signed release binaries and packages are convenience artifacts for operators.
3. The project should remain buildable from source without requiring private infrastructure.
4. No vendor-operated hosted service is assumed by these docs.

## Local Development Setup

### Recommended Toolchain

1. Go `1.23.x`
2. Git
3. Make or Mage for repeatable local workflows
4. Container runtime for integration test harnesses
5. Privileged Linux VM for collector development

### Recommended Local Workflow

1. edit code on macOS or Linux
2. run unit tests locally where possible
3. run privileged collector tests inside a Linux VM
4. run end-to-end adversary simulations against that VM

### Local Directory Assumptions

| Path | Purpose |
| --- | --- |
| `/etc/zerotrace/config.yaml` | Main config file |
| `/var/lib/zerotrace` | Runtime state and future spool directory |
| `/var/log/zerotrace/alerts.jsonl` | Local alert output |

## Build and Packaging

### Build Strategy

1. Produce a single `zerotrace` binary for the CLI and service runtime.
2. Prefer static linking where collector dependencies allow it.
3. Generate build metadata:
   - version
   - git commit
   - build time
   - target platform

### Packaging Targets

1. raw tarball with binary, sample config, and systemd unit
2. `.deb` package for Debian/Ubuntu families
3. `.rpm` package for RHEL-compatible systems

### Packaging Requirements

1. create service user and directories with sane permissions
2. install sample config and default rules
3. include systemd unit with hardening defaults where compatible
4. preserve local config on upgrade

## Service Management

### Recommended `systemd` Expectations

1. service starts after network target but does not require network in local-only mode
2. restart on failure with bounded backoff
3. use explicit writable paths instead of broad filesystem access
4. grant only required Linux capabilities

### Service Hardening Ideas

1. `NoNewPrivileges=true`
2. `PrivateTmp=true`
3. `ProtectSystem=strict`
4. `ProtectHome=true` where compatible with monitored scope
5. narrow capability set instead of unrestricted root when feasible

`TBD:` Exact capabilities depend on the final collector backends.

## CI/CD Pipeline Recommendation

### CI Stages

1. formatting and linting
2. unit tests
3. schema and config validation tests
4. integration tests
5. packaging build
6. security scanning
7. release artifact signing

### Recommended Checks

1. `go test ./...`
2. static analysis such as `govulncheck` and `staticcheck`
3. SBOM generation
4. privileged integration job on Linux runner
5. end-to-end scenario smoke tests on nightly or release branches

### Pipeline Separation

| Pipeline | Purpose |
| --- | --- |
| Pull request CI | Fast feedback: lint, unit, light integration |
| Main branch CI | Full build, packaging, schema checks |
| Nightly | Privileged collector matrix and adversary simulations |
| Release pipeline | Signed artifacts, changelog, checksums, provenance |

## Release Strategy

### Versioning

1. Use SemVer with `0.x` while the product remains pre-GA.
2. Treat alert schema changes as versioned contract changes.
3. Track rule bundle version separately from binary version.

### Rollout Approach

1. internal validation on lab hosts
2. canary deployment to a small set of real Linux hosts
3. staged expansion once performance and false-positive rates are acceptable

### Upgrade Guidance

1. binary upgrades must be restart-safe
2. config format changes must include validation and migration notes
3. rule bundle updates should be independently versioned and reversible

## Environment Separation

### MVP Agent

The local-only agent does not require `dev`, `staging`, and `prod` services to function. Environment separation mainly applies to packaging and test hosts.

### Future Backend

For an optional control plane, maintain separate:

1. development environment for API iteration
2. staging environment with synthetic agents and telemetry replay
3. production environment with strict access control and change control

## Artifact Signing and Supply Chain Integrity

### Recommended Controls

1. publish SHA-256 checksums
2. sign release artifacts with Sigstore or equivalent
3. generate provenance attestations in CI
4. generate an SBOM for each release
5. pin Go module dependencies in source control

### Why It Matters

ZeroTrace is a security agent. Users and contributors will reasonably expect verifiable releases and a clear software supply chain story.

## Containerization Notes

### Agent

The endpoint agent should not rely on container-only deployment for production host monitoring.

Reasons:

1. host visibility may be incomplete from inside a container
2. required privileges become broad and operationally awkward
3. local file and process correlation is clearer from a native service

Container use is acceptable for:

1. build tooling
2. non-privileged development
3. synthetic event pipeline testing

### Future Backend

Optional self-hosted control-plane services are good candidates for containerized deployment in Kubernetes or on VMs.

## Deployment Options

### Agent Deployment

1. package install plus `systemd`
2. SSH-driven rollout for small environments
3. configuration management via Ansible, Salt, or Puppet
4. `Future Design:` fleet enrollment via central API

### Future Control-Plane Deployment

1. small-scale single-region VM deployment for an early optional control plane
2. Kubernetes deployment for multi-service backend as scale grows
3. regionalized ingestion services if telemetry volume justifies it

## Operational Notes

1. Local-only mode should remain functional even when remote connectivity is unavailable.
2. Config should default to no remote export.
3. Any optional control-plane dependency must fail closed for transport security and fail open for local detection continuity.

## Initial Implementation Checklist

1. define build target names and version injection strategy
2. create sample config and systemd unit
3. create package metadata for `.deb` and `.rpm`
4. add CI jobs for unit, integration, packaging, and SBOM generation
5. define release signing and checksum publication workflow
