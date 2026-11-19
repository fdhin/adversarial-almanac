---
tool: "netcommander"
build-system: "scripts"
upstream: "https://github.com/evilsocket/netcommander"
recipe: "blackarch"
source: "https://github.com/evilsocket/netcommander"
license: "GPL3"
blackarch-pkgver: "1.3"
---

# Installing netcommander

> An easy-to-use arp spoofing tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/netcommander) (version `1.3`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S netcommander    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/evilsocket/netcommander) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-scapy (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[spoof]] · [[netcommander]] · [[BlackArch Tools]]

