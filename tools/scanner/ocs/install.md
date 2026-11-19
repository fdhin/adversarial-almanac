---
tool: "ocs"
build-system: "other"
upstream: "http://packetstormsecurity.com/files/119462/OCS-Cisco-Scanner-0.2.html"
recipe: "blackarch"
source: "http://packetstormsecurity.com/files/119462/OCS-Cisco-Scanner-0.2.html"
license: "GPL"
blackarch-pkgver: "0.2"
---

# Installing ocs

> Compact mass scanner for Cisco routers with default telnet/enable passwords.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/ocs) (version `0.2`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S ocs    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/119462/OCS-Cisco-Scanner.2.html) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `gcc`

## Exact build steps from the recipe

```bash
gcc -o OCS "OCS-$pkgver.c" -lpthread
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[ocs]] · [[BlackArch Tools]]

