---
tool: "d-tect"
build-system: "scripts"
upstream: "https://github.com/shawarkhanethicalhacker/D-TECT"
recipe: "blackarch"
source: "https://github.com/shawarkhanethicalhacker/D-TECT"
license: "GPL3"
blackarch-pkgver: "13.9555c25"
---

# Installing d-tect

> Pentesting the Modern Web.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/d-tect) (version `13.9555c25`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S d-tect    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/shawarkhanethicalhacker/D-TECT.git
cd D-TECT
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[d-tect]] · [[BlackArch Tools]]

