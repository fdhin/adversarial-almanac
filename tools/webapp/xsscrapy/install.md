---
tool: "xsscrapy"
build-system: "scripts"
upstream: "https://github.com/DanMcInerney/xsscrapy"
recipe: "blackarch"
source: "https://github.com/DanMcInerney/xsscrapy"
license: "custom:unknown"
blackarch-pkgver: "153.4966255"
---

# Installing xsscrapy

> XSS spider - 66/66 wavsep XSS detected.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xsscrapy) (version `153.4966255`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S xsscrapy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/DanMcInerney/xsscrapy) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pip, python2-typing, python2-virtualenv (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[xsscrapy]] · [[BlackArch Tools]]

