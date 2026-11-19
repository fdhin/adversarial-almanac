---
tool: "recuperabit"
build-system: "scripts"
upstream: "https://github.com/Lazza/RecuperaBit"
recipe: "blackarch"
source: "https://github.com/Lazza/RecuperaBit"
license: "GPL3"
blackarch-pkgver: "98.90930dc"
---

# Installing recuperabit

> A tool for forensic file system reconstruction.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/recuperabit) (version `98.90930dc`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S recuperabit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Lazza/RecuperaBit.git
cd RecuperaBit
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[recuperabit]] · [[BlackArch Tools]]

