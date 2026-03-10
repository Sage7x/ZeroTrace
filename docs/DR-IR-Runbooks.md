# ZeroTrace Disaster Recovery and Incident Response Plan

## Purpose

This document covers both:

1. product and platform incidents affecting ZeroTrace availability or integrity
2. detection-quality incidents where the product is running but producing unsafe or low-value results

The MVP is local-only, so several backend runbooks are marked as future-facing.

## Severity Levels

| Severity | Definition | Example |
| --- | --- | --- |
| `SEV-1` | Critical security or broad outage with user-impacting detection loss or compromise | Signed release compromise, widespread agent crash loop |
| `SEV-2` | Major functionality loss or materially unsafe detection behavior | High false-positive storm across canary hosts |
| `SEV-3` | Partial degradation with workaround available | One collector backend broken on a subset of kernels |
| `SEV-4` | Minor issue with low immediate impact | Cosmetic CLI output regression |

## Escalation Model

### Core Roles

1. incident commander
2. engineering lead
3. detections lead
4. release owner
5. security lead

### Escalation Expectations

1. `SEV-1`: page engineering lead and security lead immediately
2. `SEV-2`: page engineering lead and detections lead
3. `SEV-3`: route to owning engineer with same-day acknowledgement
4. `SEV-4`: backlog unless user impact grows

## General Incident Workflow

1. detect and classify severity
2. establish incident owner
3. contain blast radius
4. preserve evidence
5. recover service or detection quality
6. communicate status and mitigation
7. run postmortem

## Recovery Procedures

### Product or Platform Incidents

1. identify whether the fault is binary, config, collector backend, or packaging
2. stop rollout if a release is in progress
3. preserve logs, alerts, and version metadata
4. decide whether rollback or hotfix is lower risk
5. validate health on canary hosts before broader redeploy

### Detection-Quality Incidents

1. identify which rule bundle or config change introduced the issue
2. quantify blast radius:
   - number of hosts
   - alert volume
   - severity skew
3. suppress or disable the affected rule if necessary
4. revalidate with scenario fixtures and clean baseline checks

## Operational Runbooks

### Failed Deployment Rollback

### Trigger

New release causes startup failures, crash loops, or materially degraded collector coverage.

### Steps

1. halt rollout immediately
2. identify last known good binary and rule bundle versions
3. reinstall or reactivate previous package
4. restore prior config if the failure was config-induced
5. confirm with `zerotrace status` that collectors and rules return to expected state
6. preserve failed artifact, logs, and environment metadata for root cause analysis

### Validation

1. service stable for at least one restart cycle
2. collectors healthy or degraded only as previously known
3. alert output and schema unaffected

### Alert Ingestion Outage Handling

`Future Design:` Applies once an optional control plane exists.

### Trigger

Backend alert ingestion is unavailable or repeatedly rejecting valid payloads.

### Steps

1. keep local detection active
2. queue alerts locally if durable spool exists
3. expose backlog size in status and metrics
4. notify operations if backlog growth risks local disk exhaustion
5. replay queued alerts once backend recovers

### Failure Boundary

Remote delivery failure must not disable local alert generation.

### Corrupted Config or State Handling

### Trigger

Config file becomes invalid, missing, or semantically unsafe; runtime state becomes unreadable or inconsistent.

### Steps

1. reject invalid config and continue on last known good config if available
2. surface failure in logs and status output
3. if in-memory state is corrupted, restart the agent and rebuild fresh runtime state
4. preserve the invalid config artifact for investigation

### Notes

1. in-memory correlation state loss is acceptable on restart in the MVP
2. config corruption is higher priority than state loss because it can disable coverage persistently

### High False-Positive Incident Response

### Trigger

Unexpected spike in medium/high alerts on benign workloads after rule or config change.

### Steps

1. identify affected rules and first bad release or config version
2. pause rollout or revert rule bundle
3. add temporary suppression if operator impact is ongoing
4. replay known benign and adversary fixtures
5. publish tuning guidance or emergency rule update

### Success Criteria

1. alert rate returns to expected baseline
2. intended adversary scenarios still detect after fix

### High False-Negative or Coverage Gap Response

### Trigger

Known exfiltration scenario is missed in validation or field use, or a collector silently stops providing expected data.

### Steps

1. determine whether the miss was due to collection, enrichment, or rule logic
2. compare affected host against supported environment matrix
3. add or update simulation fixture reproducing the miss
4. decide whether urgent hotfix is required based on severity and exploitability

### Compromised Agent Scenario

### Trigger

Evidence suggests the ZeroTrace agent binary, config, runtime token, or update path has been tampered with.

### Immediate Actions

1. declare `SEV-1`
2. stop new rollouts
3. revoke affected credentials or enrollment tokens
4. rotate signing keys or trust roots if release integrity is in doubt
5. capture forensic evidence from affected systems

### Recovery

1. rebuild trusted artifacts
2. re-sign and republish
3. validate clean installation path
4. communicate affected versions and remediation steps clearly

### Compromised Backend Scenario

`Future Design:` Applies when optional control-plane services exist.

### Trigger

Config service, alert pipeline, or rule distribution service is compromised.

### Steps

1. disable backend-driven config and rule updates
2. fall back agents to local last known good config if possible
3. revoke backend credentials
4. assess whether telemetry exposure occurred
5. restore backend from clean infrastructure and trusted artifacts

## Communications Guidance

During incidents, communications should distinguish between:

1. product outage
2. degraded detection coverage
3. detection-quality issue
4. security compromise

This distinction matters because local detection may still be functioning even when optional control-plane services are impaired.

## Backup and Recovery Notes

### MVP

1. source code must be backed up in version control and protected branches
2. release artifacts and signing metadata must be retained
3. sample configs and rule bundles should be reproducible from source control

### Future Backend

1. back up rule definitions and config assignments
2. back up alert metadata stores and audit trails
3. test infrastructure rebuild from scratch

## Postmortem Template

### Summary

1. incident title
2. severity
3. start and end time
4. impact summary

### Timeline

1. first signal
2. escalation
3. mitigation actions
4. recovery

### Root Cause

1. direct cause
2. contributing factors
3. why safeguards failed or were absent

### Detection and Response

1. how the issue was discovered
2. what signals were missing
3. what slowed diagnosis or recovery

### Corrective Actions

1. code changes
2. rule or config changes
3. test additions
4. observability improvements
5. process changes
