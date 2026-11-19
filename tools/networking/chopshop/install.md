---
tool: "chopshop"
build-system: "scripts"
upstream: "https://github.com/MITRECND/chopshop"
recipe: "blackarch"
source: "https://github.com/MITRECND/chopshop"
license: "MIT"
blackarch-pkgver: "444.8bdd393"
---

# Installing chopshop

> Protocol Analysis/Decoder Framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/chopshop) (version `444.8bdd393`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S chopshop    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/MITRECND/chopshop) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-dnslib, python2-m2crypto, python2-pylibemu, python2-pymongo, python2-pynids, python2-yaraprocessor (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[chopshop]] · [[BlackArch Tools]]

