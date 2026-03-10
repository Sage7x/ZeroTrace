# ZeroTrace Security and Compliance Policy

## Purpose

ZeroTrace is an open-source security project that runs on monitored Linux hosts. Its own security posture must be at least as disciplined as the detections it provides. This policy defines practical expectations for coding, deployment, telemetry handling, and vulnerability response.

## Security Principles

1. least privilege over convenience
2. telemetry minimization over broad collection
3. secure defaults over opt-in hardening
4. explainable data handling over opaque capture
5. versioned, reviewable configuration over hidden runtime behavior

## Secure Coding Expectations

### Go Implementation Expectations

1. prefer standard library packages unless a third-party dependency is clearly justified
2. avoid `unsafe` unless there is a documented collector-specific need
3. use explicit context timeouts and cancellation for I/O and long-running operations
4. wrap errors with subsystem context
5. validate all config and remote inputs before use
6. avoid panic-driven control flow in long-running agent code

### Code Review Expectations

Changes should be reviewed for:

1. privilege increase
2. new telemetry fields
3. data retention changes
4. schema changes
5. third-party dependency additions
6. redaction failures in logs or alerts

## Secrets Handling

### Allowed Secret Types

1. future enrollment tokens
2. future managed-mode API tokens or certificates
3. operator-provided destination credentials only if remote export becomes supported

### Handling Rules

1. never log secrets or tokens
2. store secrets in files with restrictive permissions such as `0600`
3. prefer short-lived tokens in optional control-plane mode
4. avoid embedding credentials in command-line flags when a config file or environment variable can be used safely

`TBD:` Whether the optional control plane will use bearer tokens, mTLS, or both.

## Least Privilege Guidance

### Runtime Model

1. run with only the capabilities required by the active collectors
2. prefer Linux capabilities or narrowly scoped privileges over unrestricted root
3. separate writable state directories from system binaries and configs
4. reject configs that attempt to write alerts or state to unsafe locations

### Collector-Specific Guidance

1. file monitoring should watch configured paths only
2. network monitoring should capture metadata only
3. process monitoring should avoid collecting full command lines unless explicitly allowed

## Logging and Data Handling Policy

### Product Logs

Product logs are operational records about ZeroTrace itself.

Allowed examples:

1. collector health changes
2. startup and shutdown events
3. config validation errors
4. remote export failures in a future optional control plane

Product logs must not include:

1. full file contents
2. packet payloads
3. raw secrets from config or command lines
4. unbounded dumps of host telemetry events

### Monitored Host Telemetry

Monitored host telemetry is security data generated from the monitored host.

Collection boundary:

1. file paths or path hashes
2. process metadata
3. outbound destination metadata
4. archive metadata
5. timestamps and host identity

Explicit exclusions by default:

1. file contents
2. clipboard contents
3. keystrokes
4. screenshots
5. packet payloads
6. memory capture

## Privacy Considerations

### Telemetry Minimization

ZeroTrace should collect the minimum metadata required to support exfiltration detection.

Required controls:

1. remote export disabled by default
2. path redaction modes available for optional control-plane deployments
3. command line capture set to `redacted` by default
4. sensitive path patterns documented and operator-configurable

### Privacy Boundaries

1. ZeroTrace is not a user surveillance product.
2. ZeroTrace should not collect content merely because it is technically accessible.
3. Operator-facing docs must state clearly what metadata is collected and what is not.

## Secure Defaults

1. local-only mode enabled by default
2. remote export disabled by default
3. JSON alert files written to explicit directories with controlled permissions
4. human CLI output should not reveal more data than JSON output
5. unsupported collectors should fail clearly rather than silently downgrade without notice

## Dependency Management

### Expectations

1. use Go modules with pinned versions
2. review new dependencies for maintenance, license, and security posture
3. remove unused dependencies promptly
4. prefer mature libraries for Linux-specific low-level integration when necessary

### Required Controls

1. run `govulncheck` in CI
2. generate an SBOM for release artifacts
3. track dependency updates regularly, not only during releases

## Vulnerability Management

### Intake Sources

1. automated dependency scans
2. internal testing
3. external bug reports
4. user-reported issues

### Suggested Remediation Targets

| Severity | Target Response |
| --- | --- |
| Critical | same day triage, emergency patch path |
| High | triage within 1 business day, fix in next urgent release |
| Medium | triage within 5 business days |
| Low | backlog with documented risk acceptance if deferred |

### Disclosure Expectations

1. maintain a security contact path as documented in [SECURITY.md](/Users/pramodhkumars/GolandProjects/ZeroTrace/SECURITY.md)
2. document affected versions and mitigations clearly
3. rotate signing keys or enrollment credentials if compromise is suspected

## Compliance Mapping Ideas

ZeroTrace should map project controls to common compliance frameworks without overstating compliance.

### Relevant Control Families

1. NIST CSF `DE.CM` and `RS.AN` for monitoring and response
2. NIST 800-61 for incident handling alignment
3. SOC 2 CC6 and CC7 for access control, change management, and monitoring
4. CIS Controls 8 and 13 for audit log management and network monitoring
5. MITRE ATT&CK coverage references for `T1005`, `T1020`, `T1041`, `T1560`

### Practical Positioning

1. ZeroTrace can support compliance programs by generating evidence and alerts.
2. ZeroTrace alone should not be marketed as satisfying a framework requirement end-to-end.

## Security Review Gates

Before release:

1. least privilege review completed
2. telemetry field inventory reviewed
3. log redaction tests pass
4. dependency and SBOM review completed
5. upgrade and rollback flows reviewed for tamper and safety concerns
