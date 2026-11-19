---
tool: "rbndr"
build-system: "other"
upstream: "https://github.com/taviso/rbndr"
recipe: "blackarch"
source: "https://github.com/taviso/rbndr"
license: "GPL3"
blackarch-pkgver: "9.a189ffd"
---

# Installing rbndr

> Simple DNS Rebinding Service.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rbndr) (version `9.a189ffd`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S rbndr    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/taviso/rbndr) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  gcc rebinder.c -o $pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[spoof]] · [[rbndr]] · [[BlackArch Tools]]

