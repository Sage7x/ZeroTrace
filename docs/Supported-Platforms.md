# ZeroTrace Supported Platforms

## Purpose

This document defines the initial support intent for ZeroTrace as an open-source Linux security agent. It is deliberately conservative because the project is still pre-implementation.

## Support Model

- `Assumption:` Before `1.0`, support is best-effort and driven by maintainer capacity.
- `Assumption:` Linux is the only in-scope operating system for the MVP.
- `TBD:` Exact support guarantees will tighten after collector backends are validated in code.

## Target Platform Matrix

| Platform Class | Status | Notes |
| --- | --- | --- |
| Ubuntu LTS on `amd64` | target | Strong first target for development and CI |
| Ubuntu LTS on `arm64` | target | Important for cloud and edge hosts |
| Debian stable on `amd64` | target | Common server baseline |
| RHEL-compatible 9.x on `amd64` | target | Important for production Linux use cases |
| Fedora current | best-effort | Useful for early collector validation |
| Amazon Linux 2023 | best-effort | Useful if compatibility emerges naturally |
| macOS, Windows, BSD | not supported | Outside MVP scope |

## Kernel and Runtime Expectations

1. `Assumption:` Start with modern LTS kernels in the `5.15+` range.
2. `TBD:` Minimum kernel version depends on the final process and network collector backend.
3. `Assumption:` `systemd` is the primary service manager for supported installs.
4. Containerized deployment is not the primary production model for host monitoring.

## Privilege Expectations

1. ZeroTrace will require elevated privileges or Linux capabilities for some collectors.
2. The project should prefer narrowly scoped capabilities over unrestricted root where possible.
3. The exact capability set remains `TBD` until collector backends are fixed.

## Explicit Non-Targets

1. container-only visibility as the primary deployment model
2. unsupported kernels without required collector capabilities
3. WSL or non-native Linux compatibility layers
4. desktop-focused UX for the MVP

## Compatibility Policy

1. New platform claims should not be added without tests or maintainer validation.
2. Breaking platform changes must be called out in release notes.
3. Issues reported on best-effort platforms may be triaged more slowly than target platforms.

## Reporting Platform Issues

When filing a compatibility issue, include:

1. distro and version
2. kernel version
3. architecture
4. init system
5. container runtime if relevant
6. exact failure mode and relevant logs
