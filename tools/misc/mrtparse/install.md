---
tool: "mrtparse"
build-system: "scripts"
upstream: "https://github.com/YoshiyukiYamauchi/mrtparse"
recipe: "blackarch"
source: "https://github.com/YoshiyukiYamauchi/mrtparse"
license: "APACHE"
blackarch-pkgver: "522.13cde30"
---

# Installing mrtparse

> A module to read and analyze the MRT format data.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mrtparse) (version `522.13cde30`, license APACHE).

## Arch / BlackArch

```bash
sudo pacman -S mrtparse    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/YoshiyukiYamauchi/mrtparse) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[mrtparse]] · [[BlackArch Tools]]

