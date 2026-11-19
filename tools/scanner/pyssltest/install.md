---
tool: "pyssltest"
build-system: "scripts"
upstream: "https://github.com/moheshmohan/pyssltest"
recipe: "blackarch"
source: "https://github.com/moheshmohan/pyssltest"
license: "BSD"
blackarch-pkgver: "9.d7703f0"
---

# Installing pyssltest

> A python multithreaded script to make use of Qualys ssllabs api to test SSL flaws.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pyssltest) (version `9.d7703f0`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S pyssltest    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/moheshmohan/pyssltest) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-linecache2, python2-unirest (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[pyssltest]] · [[BlackArch Tools]]

