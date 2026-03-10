# Security Policy

## Reporting a Vulnerability

Do not open a public issue for vulnerabilities that could put users at risk.

Preferred path:

1. Use the repository's private vulnerability reporting mechanism if it is enabled.
2. If that is not available, contact the maintainers privately before any public disclosure.

`TBD:` A dedicated security contact address should be added once project maintainership is finalized.

## What To Report

Examples of in-scope security issues:

1. privilege escalation in the agent
2. remote code execution or unsafe command execution
3. credential or token leakage
4. telemetry leaks outside documented boundaries
5. signature, release, or update-path compromise
6. bypasses that break documented security guarantees

## Supported Versions

Until `1.0`, security support is best-effort and primarily focused on:

1. the default branch
2. the most recent tagged release, once releases exist

## Disclosure Expectations

1. Maintainers should acknowledge private reports as quickly as practical.
2. Public disclosure should wait until maintainers have had a reasonable chance to validate and mitigate the issue.
3. Fixes should include version impact, mitigation guidance, and any required credential or artifact rotation steps.

## Security Boundaries

ZeroTrace is designed around metadata collection for exfiltration detection. By default it should not capture:

1. file contents
2. packet payloads
3. keystrokes
4. screenshots
5. memory dumps

Any change that broadens those boundaries should be treated as security-sensitive and require explicit maintainer review.
