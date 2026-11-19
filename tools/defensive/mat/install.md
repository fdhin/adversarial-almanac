---
tool: "mat"
build-system: "scripts"
upstream: "https://mat.boum.org/"
recipe: "blackarch"
source: "https://mat.boum.org/"
license: "GPL2"
blackarch-pkgver: "0.6.1"
---

# Installing mat

> Metadata Anonymisation Toolkit composed of a GUI application, a CLI application and a library.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/mat) (version `0.6.1`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S mat    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://mat.boum.org/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `hachoir-core`
  - `hachoir-parser`
  - `python2`
- ⚠️ Python 2 only deps: python2-cairo, python2-distutils-extra, python2-mutagen, python2-pdfrw, python2-poppler, python2-setuptools (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[mat]] · [[BlackArch Tools]]

