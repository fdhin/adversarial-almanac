---
tool: "network-app-stress-tester"
build-system: "other"
upstream: "https://github.com/PherricOxide/Network-App-Stress-Tester"
recipe: "blackarch"
source: "https://github.com/PherricOxide/Network-App-Stress-Tester"
license: "GPL3"
blackarch-pkgver: "19.df75391"
---

# Installing network-app-stress-tester

> Network Application Stress Testing Yammer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/network-app-stress-tester) (version `19.df75391`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S network-app-stress-tester    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/PherricOxide/Network-App-Stress-Tester.git
cd Network-App-Stress-Tester
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Exact build steps from the recipe

```bash
cd "$pkgname/src"

  g++ fast.c -o $pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[dos]] · [[network-app-stress-tester]] · [[BlackArch Tools]]

