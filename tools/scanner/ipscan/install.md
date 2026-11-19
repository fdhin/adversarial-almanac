---
tool: "ipscan"
build-system: "scripts"
upstream: "https://github.com/angryip/ipscan"
recipe: "blackarch"
nix-package: "ipscan"
aur-package: "ipscan"
source: "https://github.com/angryip/ipscan"
license: "GPL-2.0-only"
blackarch-pkgver: "3.9.3"
---

# Installing ipscan

> A very fast IP address and port scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ipscan) (version `3.9.3`, license GPL-2.0-only).

## Arch / BlackArch

```bash
sudo pacman -S ipscan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/angryip/ipscan) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[ipscan]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#ipscan    # ad-hoc run, no install
nix profile install nixpkgs#ipscan   # or persist it
```
