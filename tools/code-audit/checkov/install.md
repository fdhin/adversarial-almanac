---
tool: "checkov"
build-system: "scripts"
upstream: "https://github.com/bridgecrewio/checkov"
recipe: "blackarch"
nix-package: "checkov"
source: "https://github.com/bridgecrewio/checkov"
license: "Apache"
blackarch-pkgver: "3.4.0.r1.g3bdc054cfb"
---

# Installing checkov

> Prevent cloud misconfigurations and find vulnerabilities during build-time in infrastructure as code, container images and open source packages.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/checkov) (version `3.4.0.r1.g3bdc054cfb`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S checkov    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/bridgecrewio/checkov) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `virtualenv`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[code-audit]] · [[checkov]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#checkov    # ad-hoc run, no install
nix profile install nixpkgs#checkov   # or persist it
```
