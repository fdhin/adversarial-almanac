---
tool: "firstorder"
build-system: "scripts"
upstream: "https://github.com/tearsecurity/firstorder"
recipe: "blackarch"
source: "https://github.com/tearsecurity/firstorder"
license: "custom:BSD"
blackarch-pkgver: "8.107eb6a"
---

# Installing firstorder

> A traffic analyzer to evade Empire communication from Anomaly-Based IDS.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/firstorder) (version `8.107eb6a`, license custom:BSD).

## Arch / BlackArch

```bash
sudo pacman -S firstorder    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/tearsecurity/firstorder) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-requests, python2-scapy, python2-scapy-http (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[sniffer]] · [[firstorder]] · [[BlackArch Tools]]

