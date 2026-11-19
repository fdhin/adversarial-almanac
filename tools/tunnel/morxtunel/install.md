---
tool: "morxtunel"
build-system: "other"
upstream: "http://www.morxploit.com/tools/"
recipe: "blackarch"
source: "http://web.archive.org/web/20150428060204/http://www.morxploit.com:80/morxtools/MorXTunel.c"
license: "GPL2"
blackarch-pkgver: "1.0"
---

# Installing morxtunel

> Network Tunneling using TUN/TAP interfaces over TCP tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/morxtunel) (version `1.0`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S morxtunel    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.morxploit.com/tools/) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Exact build steps from the recipe

```bash
gcc -Wall MorXTunel.c -o $pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[tunnel]] · [[morxtunel]] · [[BlackArch Tools]]

