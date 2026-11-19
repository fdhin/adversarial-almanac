---
tool: "gtfoblookup"
build-system: "scripts"
upstream: "https://github.com/nccgroup/GTFOBLookup"
recipe: "blackarch"
source: "https://github.com/nccgroup/GTFOBLookup"
license: "GPL3"
blackarch-pkgver: "69.cc50e6e"
---

# Installing gtfoblookup

> Offline command line lookup utility for GTFOBins and LOLBAS.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/gtfoblookup) (version `69.cc50e6e`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S gtfoblookup    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/nccgroup/GTFOBLookup.git
cd GTFOBLookup
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `appdirs`, `colorama`, `gitpython`, `yaml`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/man`

Backlinks: [[misc]] · [[gtfoblookup]] · [[BlackArch Tools]]

