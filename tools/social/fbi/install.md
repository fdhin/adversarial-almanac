---
tool: "fbi"
build-system: "scripts"
upstream: "https://github.com/xHak9x/fbi"
recipe: "blackarch"
source: "https://github.com/xHak9x/fbi"
license: "GPL2"
blackarch-pkgver: "28.0f94e99"
---

# Installing fbi

> An accurate facebook account information gathering.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fbi) (version `28.0f94e99`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S fbi    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/xHak9x/fbi) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-requests (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[fbi]] · [[BlackArch Tools]]

