---
tool: "snyk"
build-system: "scripts"
upstream: "https://github.com/snyk/cli"
recipe: "blackarch"
nix-package: "snyk"
aur-package: "snyk"
source: "https://github.com/snyk/cli"
license: "Apache"
blackarch-pkgver: "1.1283.0"
---

# Installing snyk

> CLI and build-time tool to find and fix known vulnerabilities in open-source dependencies.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/snyk) (version `1.1283.0`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S snyk    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/snyk/cli) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[code-audit]] · [[snyk]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#snyk    # ad-hoc run, no install
nix profile install nixpkgs#snyk   # or persist it
```
