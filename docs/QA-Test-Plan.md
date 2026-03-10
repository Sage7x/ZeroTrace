# ZeroTrace Quality Assurance and Test Plan

## Purpose

This plan defines how ZeroTrace will be validated as a Linux-first behavioral detector for data exfiltration. The focus is not just software correctness, but also detection quality, performance, and operational safety.

- `Assumption:` The first release targets a local agent with no backend dependency.
- `Assumption:` QA must validate both product behavior and detection outcomes.
- `TBD:` Final supported distro and kernel matrix.

## Testing Strategy

ZeroTrace testing is split into five layers:

1. unit tests for parsers, normalization, config, state, and rule logic
2. integration tests for collectors and event pipeline behavior
3. end-to-end tests using realistic adversary simulations
4. performance and stability tests on representative Linux hosts
5. detection-quality validation for false positives and false negatives

## Test Environments

### Minimum Environment Matrix

1. Ubuntu LTS server image
2. Debian stable server image
3. RHEL-compatible image
4. Containerized test harness for non-privileged components
5. Privileged host or VM for collector integration tests

### Environment Characteristics

1. Bare VM with direct host visibility
2. Host with common server workloads
3. Host with container runtime present
4. Offline host where remote services are unavailable

## Unit Testing

### Areas to Cover

1. config parsing and validation
2. path sensitivity matching
3. IP and hostname internal vs external classification
4. archive tool identification
5. event normalization from raw collector input
6. correlation window updates and eviction
7. rule evaluation logic
8. alert formatting and schema serialization

### Unit Test Standards

1. Rules must be tested with positive and negative fixtures.
2. Config validation must include malformed and risky-but-valid cases.
3. Time-window logic must use deterministic clocks.
4. Event and alert schema tests must assert required field presence.

## Integration Testing

Integration tests validate component contracts, not just individual functions.

### Integration Test Areas

1. process collector -> normalizer
2. file collector -> sensitivity tagging
3. archive detection from process plus file events
4. network collector -> external destination classification
5. event pipeline -> correlation state -> rule engine
6. local alert store write/read behavior
7. CLI commands against live agent runtime

### Required Integration Scenarios

1. collector starts healthy and emits valid normalized events
2. collector fails or degrades and status reflects the exact coverage loss
3. alert emission continues when one non-critical collector degrades
4. config reload or restart preserves safe startup behavior

## End-to-End Testing

End-to-end tests must use realistic process trees and Linux tools rather than synthetic rule invocations only.

### Adversary Simulation Scenarios

| Scenario ID | Description | Expected Result |
| --- | --- | --- |
| `E2E-EXFIL-001` | `find` sensitive files, `tar -czf /tmp/stage.tgz`, then `scp` to external host | High alert for collection -> archive -> outbound transfer |
| `E2E-EXFIL-002` | Read `.env`, `.pem`, and backup files, then `zip` and upload with `curl -T` | High alert with evidence for sensitive hits and external upload |
| `E2E-EXFIL-003` | Use `rclone copy` to public cloud object storage after staging files | High alert for bulk collect then cloud upload |
| `E2E-EXFIL-004` | Use `aws s3 cp` with credentials already on host | High alert if destination not allowlisted |
| `E2E-EXFIL-005` | Access SSH keys and `kubeconfig`, then exfiltrate using `nc` or `socat` | Medium or high alert depending rule confidence |
| `E2E-EXFIL-006` | Create archive under `/tmp` and open external TLS session via `curl` | High alert |

### Benign Comparison Scenarios

| Scenario ID | Description | Expected Result |
| --- | --- | --- |
| `E2E-BENIGN-001` | Nightly backup process reads large files and writes a local archive only | No high-severity alert |
| `E2E-BENIGN-002` | Package manager reads many files and opens internal network connections | No exfiltration alert |
| `E2E-BENIGN-003` | Administrator uses `tar` for local troubleshooting without outbound transfer | At most low-severity or no alert |
| `E2E-BENIGN-004` | CI runner uploads build artifacts to approved internal endpoint | No alert if destination allowlisted |

## Rule Validation Testing

### Objectives

1. Ensure each rule fires when its documented sequence occurs.
2. Ensure each rule does not fire on closely related benign workflows.
3. Validate evidence ordering, summary text, severity, and confidence.

### Rule Validation Method

1. Maintain fixture sets of normalized events per rule.
2. Replay fixtures directly into the rule engine for deterministic validation.
3. Compare produced alerts against golden outputs.
4. Re-run fixture suites whenever rule thresholds or schemas change.

### Required Assertions

1. matched rule ID is correct
2. alert severity and confidence are expected
3. evidence contains the specific sequence stages required
4. deduplication prevents repeated alert storms from the same window

## Telemetry Simulation Tests

Telemetry simulation should cover both single-event correctness and realistic burst behavior.

### Simulation Inputs

1. recorded normalized event streams from lab scenarios
2. synthetic bursts of file access events to stress window aggregation
3. mixed benign and suspicious traffic
4. reordered or delayed events to test clock skew and buffering behavior

### Simulation Goals

1. prove that event ordering assumptions are robust enough for short-lived bursts
2. validate that event drops are surfaced in health metrics
3. ensure correlation windows do not grow without bound

## Performance and Load Testing

### Performance Targets

1. average CPU overhead under target baseline: `< 2%`
2. steady-state memory under target baseline: `< 100 MB`
3. p95 alert generation latency after triggering event: `< 5s`

### Load Scenarios

1. burst read of thousands of files under a watched directory
2. high-rate short-lived outbound connection attempts
3. concurrent suspicious activity by multiple users or process trees
4. noisy host with heavy file churn but no exfiltration

### What to Measure

1. event ingest rate
2. event drop count
3. active correlation window count
4. collector lag
5. alert latency
6. CPU and memory consumption

## False Positive and False Negative Validation

Detection quality must be measured explicitly, not inferred from software tests.

### False Positive Validation

1. Run ZeroTrace on clean baseline server workloads for at least `24-72` hours.
2. Include common administrative workflows:
   - backups
   - package updates
   - CI artifact packaging
   - log shipping
   - internal file sync
3. Review every generated alert and classify:
   - true positive
   - tolerable signal
   - false positive

### False Negative Validation

1. Run the adversary simulations with variations in timing and tooling.
2. Test both obvious and quieter sequences:
   - archive then upload
   - direct upload without archive
   - staged upload after delay
3. Track which steps were seen and which rule should have fired.

## Security Testing

### Product Security Tests

1. config file permission checks
2. secret redaction in logs and alerts
3. TLS validation for a future optional control plane
4. local file permission validation for alert store and state directory
5. malformed event or config input fuzzing

### Adversarial Robustness Tests

1. attempt to overwhelm collectors with event bursts
2. provide malformed paths or command lines intended to break formatting
3. simulate clock skew and delayed event delivery
4. verify behavior if one collector silently stops producing data

## Regression Testing

Every release candidate should rerun:

1. config validation suite
2. rule fixture replay suite
3. core end-to-end exfiltration scenarios
4. benign baseline scenarios
5. packaging and service startup tests

## Release Test Checklist

1. All unit and integration tests pass on the supported matrix.
2. End-to-end exfiltration scenarios meet documented detection expectations.
3. False-positive review is completed on representative clean workloads.
4. Performance targets are met or variances are documented and approved.
5. Alert JSON validates against the documented schema.
6. CLI status and config validation commands behave as documented.
7. Degraded collector scenarios are observable and actionable.
8. Security checks for file permissions, redaction, and dependency scanning pass.
9. Upgrade, restart, and rollback flows are tested on packaged artifacts.

## Test Artifacts and Evidence

QA should preserve:

1. test host image details
2. scenario execution scripts
3. recorded normalized event streams
4. generated alerts
5. performance measurements
6. false-positive review notes

`Future Design:` Once an optional control plane exists, this plan should add API contract tests, ingestion durability tests, and multi-agent scale validation.
