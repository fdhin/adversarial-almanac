---
tool: "autonessus"
build-system: "scripts"
upstream: "https://github.com/redteamsecurity/AutoNessus"
recipe: "blackarch"
source: "https://github.com/redteamsecurity/AutoNessus"
license: "GPL3"
blackarch-pkgver: "24.7933022"
---

# Installing autonessus

> This script communicates with the Nessus API in an attempt to help with automating scans.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/autonessus) (version `24.7933022`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S autonessus    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/redteamsecurity/AutoNessus.git
cd AutoNessus
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-requests (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[autonessus]] · [[BlackArch Tools]]

