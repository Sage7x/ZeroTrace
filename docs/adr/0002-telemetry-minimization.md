# ADR 0002: Telemetry Minimization and No Content Capture by Default

## Status

Accepted

## Context

ZeroTrace is a security product handling sensitive endpoint activity. It needs enough metadata to detect data exfiltration sequences, but excessive collection would create privacy risk, increase storage cost, and complicate adoption.

## Decision

ZeroTrace will default to metadata-only telemetry and will not capture file contents, packet payloads, keystrokes, screenshots, or kernel memory.

## Rationale

1. Exfiltration behavior can often be detected from metadata sequence patterns.
2. Users and operators need clear privacy boundaries for endpoint tooling.
3. Lower-volume telemetry improves performance and retention economics.
4. Simpler data handling reduces compliance burden in the MVP.

## Consequences

Positive:

1. Stronger privacy posture
2. Reduced operational overhead
3. Easier secure defaults for local-only operation

Negative:

1. Some detections will have lower fidelity than content-aware DLP
2. Analysts may need other tools for deep content confirmation
3. Path and command-line fields still require careful redaction

## Follow-Up

1. Define path redaction modes for optional control-plane deployments.
2. Separate product telemetry from monitored-host telemetry in all observability design.
3. Reassess whether any opt-in deep telemetry modes are justified after MVP validation.
