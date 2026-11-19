---
tool: "galleta"
build-system: "scripts"
upstream: "https://www.jonesdykstra.com/"
recipe: "blackarch"
nix-package: "galleta"
source: "https://www.jonesdykstra.com/"
license: "GPL-2.0-or-later"
blackarch-pkgver: "20040505_1"
kali-package: "galleta"
kali-version: "1.0+20040505-12"
---

# Installing galleta

> Examine the contents of the IE's cookie files for forensic purposes.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/galleta) (version `20040505_1`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S galleta    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install galleta    # Kali package version 1.0+20040505-12
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.jonesdykstra.com/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[galleta]] · [[BlackArch Tools]]


## Nix (works on any distro)

```bash
nix run nixpkgs#galleta    # ad-hoc run, no install
nix profile install nixpkgs#galleta   # or persist it
```
