---
tool: "rlogin-scanner"
build-system: "other"
upstream: "http://wayreth.eu.org/old_page/"
recipe: "blackarch"
source: "http://wayreth.eu.org/rlogin_scanner.c"
license: "custom:unknown"
blackarch-pkgver: "0.2"
---

# Installing rlogin-scanner

> Multithreaded rlogin scanner. Tested on Linux, OpenBSD and Solaris.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rlogin-scanner) (version `0.2`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S rlogin-scanner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://wayreth.eu.org/old_page/) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Exact build steps from the recipe

```bash
gcc rlogin_scanner.c -o $pkgname -lpthread
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[rlogin-scanner]] · [[BlackArch Tools]]

