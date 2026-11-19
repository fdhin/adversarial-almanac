---
tool: "grabbb"
build-system: "other"
upstream: "https://packetstormsecurity.com/files/11372/grabbb-0.0.7.tar.gz.html"
recipe: "blackarch"
source: "https://packetstormsecurity.com/files/11372/grabbb-0.0.7.tar.gz.html"
license: "custom:unknown"
blackarch-pkgver: "0.0.7"
---

# Installing grabbb

> Clean, functional, and fast banner scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/grabbb) (version `0.0.7`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S grabbb    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://packetstormsecurity.com/files/11372/grabbb.0.7.tar.gz.html) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Exact build steps from the recipe

```bash
cd $pkgname

  gcc -o $pkgname "$pkgname.c"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[grabbb]] · [[BlackArch Tools]]

