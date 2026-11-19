---
tool: "xspy"
build-system: "other"
upstream: "http://www.freshports.org/security/xspy/"
recipe: "blackarch"
source: "http://www.freshports.org/security/xspy/"
license: "GPL"
blackarch-pkgver: "1.0c"
---

# Installing xspy

> A utility for monitoring keypresses on remote X servers

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xspy) (version `1.0c`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S xspy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.freshports.org/security/xspy/) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `imake`
  - `libx11`

## Exact build steps from the recipe

```bash
cd $pkgname

  gcc -lX11 -s support.c usleep.c "$pkgname.c" -o $pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[keylogger]] · [[xspy]] · [[BlackArch Tools]]

