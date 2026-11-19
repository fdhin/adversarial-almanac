---
tool: "python2-jsbeautifier"
build-system: "scripts"
upstream: "https://github.com/beautify-web/js-beautify"
recipe: "blackarch"
source: "https://github.com/beautify-web/js-beautify"
license: "MIT"
blackarch-pkgver: "1.13.4"
---

# Installing python2-jsbeautifier

> JavaScript unobfuscator and beautifier.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/python2-jsbeautifier) (version `1.13.4`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S python2-jsbeautifier    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/beautify-web/js-beautify) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-editorconfig, python2-setuptools, python2-six (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[python2-jsbeautifier]] · [[BlackArch Tools]]

