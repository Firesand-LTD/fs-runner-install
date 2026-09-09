# Firesand runner installer

Public distribution point for `install.sh`, the installer for the Firesand
self-hosted GitHub Actions runner fleet.

**There is no source here.** The installer is built, tested and published
automatically from the private `Firesand-LTD/FiresandRunner` repository every
time a release is published there. This repository exists only because GitHub
release assets inherit repository visibility — an asset on a private repo
returns `404` to an unauthenticated download, and there is no per-release
visibility setting.

## Install

```bash
curl -fsSLO https://github.com/Firesand-LTD/fs-runner-install/releases/latest/download/install.sh
curl -fsSLO https://github.com/Firesand-LTD/fs-runner-install/releases/latest/download/install.sh.sha256
sha256sum -c install.sh.sha256

sudo sh install.sh --app-id <APP_ID>
```

See exactly what it would do first — changes nothing, needs no root:

```bash
sh install.sh --dry-run --non-interactive --app-id <APP_ID> --key-file /dev/null
```

## Why not `curl … | sh`

The installer runs as **root** and rewrites system state: it creates a service
account, installs Docker, writes systemd units and seals a private key. Piping
it straight into a shell means running code you have not seen and cannot check.
Download it, verify the checksum, read it — it is one readable POSIX shell file
— then run it.

## What it does

Automates the host setup: preflight checks, the `fs-runner` service account,
Docker with rootless extras, the rootless daemon, a daily prune timer, sealing
the GitHub App private key with `systemd-creds`, `config.env`, the systemd
units, and starting the first slot. It is idempotent — re-running is safe.

Two steps it deliberately does **not** automate, and prints at the end instead:
checking the runner labels before pointing any workflow at the fleet, and
proving the fleet works with one real job.

`--help` lists every flag.

## Reporting problems

Issues belong on the private `FiresandRunner` repository, where the source
lives. This repository holds no code to fix.
