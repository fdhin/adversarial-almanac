---
tool: "ciscos"
build-system: "other"
upstream: ""
recipe: "blackarch"
source: ""
license: "custom:unknown"
blackarch-pkgver: "1.3"
---

# Installing ciscos

> Scans class A, B, and C networks for cisco routers which have telnet open and have not changed the default password from cisco.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ciscos) (version `1.3`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S ciscos    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the the upstream site (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Exact build steps from the recipe

```bash
gcc $CFLAGS -o $pkgname "$pkgname.c"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[ciscos]] · [[BlackArch Tools]]

