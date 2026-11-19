---
tool: "chkrootkit"
build-system: "scripts"
upstream: "http://www.chkrootkit.org/"
recipe: "blackarch"
aur-package: "chkrootkit"
source: "http://www.chkrootkit.org/"
license: "GPL2"
blackarch-pkgver: "0.59"
kali-package: "chkrootkit"
kali-version: "0.59-2"
---

# Installing chkrootkit

> Checks for rootkits on a system.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/chkrootkit) (version `0.59`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S chkrootkit    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install chkrootkit    # Kali package version 0.59-2
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.chkrootkit.org/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[chkrootkit]] · [[BlackArch Tools]]

