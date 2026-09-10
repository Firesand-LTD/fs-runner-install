# Security

This repository holds **no source** — only published `install.sh` release
assets. The code lives in the private `Firesand-LTD/FiresandRunner`
repository, which is where any fix would be made.

## Reporting a vulnerability

Use GitHub's private vulnerability reporting on this repository:
**Security → Advisories → Report a vulnerability**. Please do not open a
public issue for anything exploitable. (Issues are disabled here; this
repository is a distribution endpoint, not a support channel.)

There is **no response-time commitment**. This installer is published under
Apache-2.0 and provided AS IS, without warranty or support of any kind — see
`LICENSE`. Reports are read and acted on best-effort.

## What is worth reporting

The installer runs as **root** and is handed a GitHub App private key, so the
things that matter most:

- Anything that leaks that private key off the host, or out of
  `systemd-creds`.
- Anything that makes the installer execute attacker-controlled content — a
  payload that survives its SHA-256 verification, or a code path that runs
  before that verification.
- Privilege escalation out of a job container to host root.
- Anything letting one CI job read another's workspace, credentials or JIT
  registration.

## Already known and accepted

Design decisions, not undiscovered bugs:

- Jobs run as root **inside their own container** (uid 1001 on the host, with
  `--security-opt no-new-privileges`, the base image's passwordless-sudo path
  stripped, and a fresh ephemeral container per job).
- The runner can reach the Docker socket of a **rootless** daemon, so an
  escape lands on an unprivileged host account, not root.
- Toolchains baked into the image carry their own CVEs; a weekly rebuild is
  what refreshes them.

## Verifying what you downloaded

Every release ships `install.sh.sha256` next to `install.sh`. Check it before
running anything:

```bash
sha256sum -c install.sh.sha256
```
