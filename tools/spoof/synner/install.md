---
tool: "synner"
build-system: "other"
upstream: "http://packetstormsecurity.com/files/69802/synner.c.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/69802/synner.c.html"
license: "custom:unknown"
blackarch-pkgver: "1.1"
---

# Installing synner

> A custom eth->ip->tcp packet generator (spoofer) for testing firewalls and dos attacks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/synner) (version `1.1`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S synner    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/69802/synner.c.html) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Exact build steps from the recipe

```bash
gcc -o $pkgname "$pkgname.c"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[spoof]] · [[synner]] · [[BlackArch Tools]]

