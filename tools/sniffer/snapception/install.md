---
tool: "snapception"
build-system: "scripts"
upstream: "https://github.com/thebradbain/snapception"
recipe: "blackarch"
source: "https://github.com/thebradbain/snapception"
license: "MIT"
blackarch-pkgver: "8.c156f9e"
---

# Installing snapception

> Intercept and decrypt all snapchats received over your network.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/snapception) (version `8.c156f9e`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S snapception    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/thebradbain/snapception) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `mitmproxy`
  - `python2`
  - `ruby`
- ⚠️ Python 2 only deps: python2-click, python2-requests, python2-setuptools, python2-sortedcontainers (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[sniffer]] · [[snapception]] · [[BlackArch Tools]]

