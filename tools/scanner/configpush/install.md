---
tool: "configpush"
build-system: "other"
upstream: "http://packetstormsecurity.com/files/126621/Config-Push-snmpset-Utility.html"
recipe: "blackarch"
source: "https://dl.packetstormsecurity.net/UNIX/utilities/configpush.c"
license: "custom:unknown"
blackarch-pkgver: "0.8.5"
---

# Installing configpush

> This is a tool to span /8-sized networks quickly sending snmpset requests with default or otherwise specified community string to Cisco devices.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/configpush) (version `0.8.5`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S configpush    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://packetstormsecurity.com/files/126621/Config-Push-snmpset-Utility.html) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Exact build steps from the recipe

```bash
gcc "$pkgname.c" -o $pkgname -lpthread -lm
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[configpush]] · [[BlackArch Tools]]

