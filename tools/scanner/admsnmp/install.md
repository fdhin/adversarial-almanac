---
tool: "admsnmp"
build-system: "other"
upstream: ""
recipe: "blackarch"
source: ""
license: "GPL"
blackarch-pkgver: "0.1"
---

# Installing admsnmp

> ADM SNMP audit scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/admsnmp) (version `0.1`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S admsnmp    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the the upstream site (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
cd $_pkgname

  gcc $CFLAGS snmp.c -o $pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[admsnmp]] · [[BlackArch Tools]]

