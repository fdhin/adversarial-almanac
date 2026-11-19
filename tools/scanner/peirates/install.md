---
tool: "peirates"
build-system: "go"
upstream: "https://github.com/inguardians/peirates"
recipe: "upstream"
license: "GPL-2.0"
nix-package: "peirates"
---

# Installing peirates

> Kubernetes penetration testing tool: privilege escalation and pivoting through a cluster.

Source: pentest-book feed; upstream [`inguardians/peirates`](https://github.com/inguardians/peirates). Command reference: [`docs/commands`](https://github.com/inguardians/peirates/blob/main/docs/commands/README.md).

## Nix / NixOS

```bash
nix-shell -p peirates          # ad-hoc shell
nix-env -iA nixpkgs.peirates   # profile install
```

## Prebuilt binary

Grab the latest statically linked binary from [upstream releases](https://github.com/inguardians/peirates/releases/latest) — copy it into the compromised container or onto the node you are testing from.

## Container image

```bash
docker pull bustakube/alpine-peirates:latest
```

The image's version tag tracks the Peirates release (`v1.1.35` contains peirates `v1.1.35`).

## Build from source

```bash
git clone https://github.com/inguardians/peirates
cd peirates
make build        # static Linux AMD64 `peirates` in the repo root
```

Per-architecture targets (`make build-arm64`, `make build-amd64`, ...) and distribution archives (`make dist`) are also available.

Backlinks: [[scanner]] · [[peirates]]
