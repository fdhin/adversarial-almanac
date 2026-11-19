---
tool: "payloadsallthethings"
build-system: "scripts"
upstream: "https://github.com/swisskyrepo/PayloadsAllTheThings/"
recipe: "blackarch"
nix-package: "payloadsallthethings"
source: "https://github.com/swisskyrepo/PayloadsAllTheThings/"
license: "MIT"
blackarch-pkgver: "4.2.r117.g08ea40e"
---

# Installing payloadsallthethings

> A list of useful payloads and bypass for Web Application Security and Pentest/CTF.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/payloadsallthethings) (version `4.2.r117.g08ea40e`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S payloadsallthethings    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/swisskyrepo/PayloadsAllTheThings.git
cd PayloadsAllTheThings
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[payloadsallthethings]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#payloadsallthethings    # ad-hoc run, no install
nix profile install nixpkgs#payloadsallthethings   # or persist it
```
