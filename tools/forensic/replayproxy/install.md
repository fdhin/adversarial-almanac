---
tool: "replayproxy"
build-system: "scripts"
upstream: "https://code.google.com/archive/p/replayproxy/"
recipe: "blackarch"
source: "https://code.google.com/archive/p/replayproxy/"
license: "BSD-3-Clause"
blackarch-pkgver: "1.1"
---

# Installing replayproxy

> Forensic tool to replay web-based attacks (and also general HTTP traffic) that were captured in a pcap file.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/replayproxy) (version `1.1`, license BSD-3-Clause).

## Arch / BlackArch

```bash
sudo pacman -S replayproxy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://code.google.com/archive/p/replayproxy/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-dpkt, python2-pynids (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[replayproxy]] · [[BlackArch Tools]]

