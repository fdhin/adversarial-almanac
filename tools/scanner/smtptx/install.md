---
tool: "smtptx"
build-system: "scripts"
upstream: "http://www.0x90.se/"
recipe: "blackarch"
source: "http://www.0x90.se/"
license: "GPL3"
blackarch-pkgver: "1.0"
---

# Installing smtptx

> A very simple tool used for sending simple email and do some basic email testing from a pentester perspective.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/smtptx) (version `1.0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S smtptx    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.0x90.se/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-dnspython (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[smtptx]] · [[BlackArch Tools]]

