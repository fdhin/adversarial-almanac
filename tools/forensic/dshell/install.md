---
tool: "dshell"
build-system: "scripts"
upstream: "https://github.com/USArmyResearchLab/Dshell"
recipe: "blackarch"
source: "https://github.com/USArmyResearchLab/Dshell"
license: "custom:unknown"
blackarch-pkgver: "142.695c891"
---

# Installing dshell

> A network forensic analysis framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dshell) (version `142.695c891`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S dshell    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/USArmyResearchLab/Dshell.git
cd Dshell
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `elasticsearch`
  - `geoip`
  - `python2`
- ⚠️ Python 2 only deps: python2-dpkt, python2-elasticsearch, python2-geoip, python2-ipy, python2-pypcap (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[dshell]] · [[BlackArch Tools]]

