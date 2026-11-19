---
tool: "xsspy"
build-system: "scripts"
upstream: "https://github.com/faizann24/XssPy"
recipe: "blackarch"
source: "https://github.com/faizann24/XssPy"
license: "custom:unknown"
blackarch-pkgver: "60.b10d336"
---

# Installing xsspy

> Web Application XSS Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xsspy) (version `60.b10d336`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S xsspy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/faizann24/XssPy.git
cd XssPy
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-mechanize (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[xsspy]] · [[BlackArch Tools]]

