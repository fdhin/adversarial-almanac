---
tool: "trivy"
build-system: "go"
upstream: "https://github.com/aquasecurity/trivy"
recipe: "upstream"
license: "Apache-2.0"
nix-package: "trivy"
---

# Installing trivy

> Comprehensive scanner for vulnerabilities, misconfigurations, and secrets in containers, filesystems, and clusters.

Source: pentest-book feed; upstream [`aquasecurity/trivy`](https://github.com/aquasecurity/trivy). Full options: [trivy.dev docs](https://trivy.dev/docs/latest/).

## Arch Linux

```bash
sudo pacman -S trivy    # official [extra] repo
```

## Nix / NixOS

```bash
nix-shell -p trivy          # ad-hoc shell
nix-env -iA nixpkgs.trivy   # profile install
```

## Docker

```bash
docker run aquasec/trivy image python:3.4-alpine
```

## Prebuilt binary / other distros

Download from [upstream releases](https://github.com/aquasecurity/trivy/releases/latest/) (also on Homebrew: `brew install trivy`).

Backlinks: [[scanner]] · [[trivy]]
