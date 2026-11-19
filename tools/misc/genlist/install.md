---
tool: "genlist"
build-system: "other"
upstream: ""
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/UNIX/scanners/genlist.c"
license: "GPL"
blackarch-pkgver: "0.1"
---

# Installing genlist

> Generates lists of IP addresses.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/genlist) (version `0.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S genlist    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the the upstream site (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Exact build steps from the recipe

```bash
gcc $CFLAGS "$pkgname.c" -o $pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[genlist]] · [[BlackArch Tools]]

