---
tool: "dirb"
build-system: "scripts"
upstream: "http://dirb.sourceforge.net/"
recipe: "blackarch"
nix-package: "dirb"
aur-package: "dirb"
source: "http://dirb.sourceforge.net/"
license: "GPL2"
blackarch-pkgver: "2.22"
provides: ["dirb-gendict", "gendict"]
kali-package: "dirb"
kali-version: "2.22+dfsg-7"
---

# Installing dirb

> A web content scanner, brute forceing for hidden files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dirb) (version `2.22`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S dirb    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install dirb    # Kali package version 2.22+dfsg-7
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://dirb.sourceforge.net/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `curl`

## Installed layout (per recipe)

- Commands: `/usr/bin/dirb-gendict`, `/usr/bin/gendict`

Backlinks: [[scanner]] · [[dirb]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#dirb    # ad-hoc run, no install
nix profile install nixpkgs#dirb   # or persist it
```
