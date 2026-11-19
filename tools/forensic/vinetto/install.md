---
tool: "vinetto"
build-system: "scripts"
upstream: "https://vinetto.sourceforge.net"
recipe: "blackarch"
aur-package: "vinetto"
source: "https://vinetto.sourceforge.net"
license: "GPL-2.0-or-later"
blackarch-pkgver: "0.07beta"
kali-package: "vinetto"
kali-version: "1:0.9.15+dfsg-1"
---

# Installing vinetto

> A forensics tool to examine Thumbs.db files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/vinetto) (version `0.07beta`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S vinetto    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install vinetto    # Kali package version 1:0.9.15+dfsg-1
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://vinetto.sourceforge.net) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pillow (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[vinetto]] · [[BlackArch Tools]]

