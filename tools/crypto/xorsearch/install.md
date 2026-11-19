---
tool: "xorsearch"
build-system: "other"
upstream: "http://blog.didierstevens.com/programs/xorsearch/"
recipe: "blackarch"
source: "http://blog.didierstevens.com/programs/xorsearch/"
license: "custom:public domain"
blackarch-pkgver: "1.11.4"
---

# Installing xorsearch

> Program to search for a given string in an XOR, ROL or ROT encoded binary file.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xorsearch) (version `1.11.4`, license custom:public domain).

## Arch / BlackArch

```bash
sudo pacman -S xorsearch    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://blog.didierstevens.com/programs/xorsearch/) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Exact build steps from the recipe

```bash
gcc -o $pkgname XORSearch.c
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[xorsearch]] · [[BlackArch Tools]]

