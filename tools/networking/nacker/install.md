---
tool: "nacker"
build-system: "scripts"
upstream: "https://github.com/carmaa/nacker"
recipe: "blackarch"
source: "https://github.com/carmaa/nacker"
license: "GPL2"
blackarch-pkgver: "23.b67bb39"
---

# Installing nacker

> A tool to circumvent 802.1x Network Access Control on a wired LAN.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nacker) (version `23.b67bb39`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S nacker    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/carmaa/nacker) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-netaddr, python2-netifaces, python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[nacker]] · [[BlackArch Tools]]

