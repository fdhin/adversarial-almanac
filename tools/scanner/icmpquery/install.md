---
tool: "icmpquery"
build-system: "other"
upstream: "http://www.angio.net/security/"
recipe: "blackarch"
source: "http://www.angio.net/security/icmpquery.c"
license: "custom:unknown"
blackarch-pkgver: "1.0"
---

# Installing icmpquery

> Send and receive ICMP queries for address mask and current time.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/icmpquery) (version `1.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S icmpquery    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.angio.net/security/) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Exact build steps from the recipe

```bash
gcc "$pkgname.c" -o $pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[icmpquery]] · [[BlackArch Tools]]

