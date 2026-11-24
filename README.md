# Amadioha releases

Amadioha is a security operations platform: agents and container monitors feed
a detection engine with SigmaHQ rules, IP reputation enriches what it finds, and
one console shows alerts, hosts, image scans and exploited CVEs in one place.

This repository holds the install files for each release. The source code is
not public.

## What a release contains

| File | What it is |
|---|---|
| `amadioha-<version>.tar.gz` | The install bundle: `install.sh`, the compose file, the agents for Linux, macOS, Windows, containers and Kubernetes, and the install guide (`INSTALL.md`) |
| `SHA256SUMS` | The bundle's checksum |
| `amadioha-soc` `.deb` and `.rpm` | The server as a package, for Ubuntu, Debian and the RHEL family (Rocky, Alma, RHEL, Fedora) |
| `amadioha-agent` `.deb` and `.rpm` | The Linux agent that sends a host's logs and metrics to the server |
| `SHA256SUMS-packages` | The packages' checksums |
| `amadioha-agent-<version>.tgz` | The agent's Helm chart, for Kubernetes |
| `*.sigstore.json`, `cosign.pub` | Signatures of the checksum files, and the key to check them with (below) |

Releases marked *Pre-release* are release candidates. A release candidate's
packages carry its version with a dot: for `v1.0.0-rc.3` the bundle is
`amadioha-1.0.0-rc.3.tar.gz` and the Ubuntu package
`amadioha-soc_1.0.0.rc.3_all.deb`.

## Install

You need a Linux host with Docker Engine and Compose v2 (from
[Docker's repositories](https://docs.docker.com/engine/install/)), and at least
2 vCPU, 4 GB of memory and 40 GB of disk.

With the bundle:

```sh
version=1.0.0
base=https://github.com/sebastined/amadioha-releases/releases/download/v$version
curl -fsSLO "$base/amadioha-$version.tar.gz"
curl -fsSLO "$base/SHA256SUMS"
sha256sum -c --ignore-missing SHA256SUMS
tar xzf "amadioha-$version.tar.gz" && cd "amadioha-$version"
./install.sh --admin-email you@example.com
```

Or with a package, on Ubuntu or Debian:

```sh
curl -fsSLO "$base/amadioha-soc_${version}_all.deb" && sudo apt install "./amadioha-soc_${version}_all.deb"
sudo amadioha-soc-setup --admin-email you@example.com
```

and on the RHEL family:

```sh
curl -fsSLO "$base/amadioha-soc-$version-1.noarch.rpm" && sudo dnf install "./amadioha-soc-$version-1.noarch.rpm"
sudo amadioha-soc-setup --admin-email you@example.com
```

`install.sh` generates every secret, creates the first admin and prints how to
sign in. `INSTALL.md` in the bundle covers TLS for agents, single sign-on,
upgrades and backups.

## Images

The containers come from the GitHub Container Registry (`ghcr.io/sebastined`)
and, as a mirror, Docker Hub (`docker.io/sebastined`). Every image is signed
with [cosign](https://docs.sigstore.dev/), so you can check one before
running it. Releases are signed with Amadioha's key, whose public half is
`cosign.pub` in this repository:

```sh
cosign verify --key cosign.pub ghcr.io/sebastined/amadioha-soc-manager:1.0.0
```

Each release's checksum files have a signature beside them
(`SHA256SUMS.sigstore.json`):

```sh
cosign verify-blob --key cosign.pub --bundle SHA256SUMS.sigstore.json SHA256SUMS
```

## Licence

Amadioha is free to use under the Amadioha Free Use License 1.0 (`LICENSE`).
It includes third-party software under its own licences
(`THIRD_PARTY_NOTICES.md`).
