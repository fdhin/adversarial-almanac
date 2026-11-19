---
tool: "enabler"
build-system: "other"
upstream: "http://packetstormsecurity.org/cisco/enabler.c"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/cisco/enabler.c"
license: "custom:unknown"
blackarch-pkgver: "1"
---

# Installing enabler

> Attempts to find the enable password on a cisco system via brute force.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/enabler) (version `1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S enabler    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.org/cisco/enabler.c) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `gcc`
- **System libraries** (Arch name — translate for your distro):
  - `glibc`

## Exact build steps from the recipe

```bash
gcc $CFLAGS "$pkgname.c" -o $pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[enabler]] · [[BlackArch Tools]]

