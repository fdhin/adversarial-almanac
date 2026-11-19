---
tool: "wol-e"
build-system: "scripts"
upstream: "https://code.google.com/archive/p/wol-e/"
recipe: "blackarch"
source: "https://code.google.com/archive/p/wol-e/"
license: "GPL-3.0-or-later"
blackarch-pkgver: "2.0"
---

# Installing wol-e

> A suite of tools for the Wake on LAN feature of network attached computers.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wol-e) (version `2.0`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S wol-e    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://code.google.com/archive/p/wol-e/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `impacket`
- ⚠️ Python 2 only deps: python2-pcapy, python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[wol-e]] · [[BlackArch Tools]]

