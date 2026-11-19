---
tool: "cisco-ocs"
build-system: "other"
upstream: "http://www.question-defense.com/2013/01/11/ocs-version-0-2-release-ocs-cisco-router-default-password-scanner"
recipe: "blackarch"
source: "http://www.question-defense.com/2013/01/11/ocs-version-0-2-release-ocs-cisco-router-default-password-scanner"
license: "GPL-2.0-or-later"
blackarch-pkgver: "0.2"
kali-package: "cisco-ocs"
kali-version: "0.2-1kali4"
---

# Installing cisco-ocs

> Cisco Router Default Password Scanner.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/cisco-ocs) (version `0.2`, license GPL-2.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S cisco-ocs    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install cisco-ocs    # Kali package version 0.2-1kali4
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.question-defense.com/2013/01/11/ocs-version-2-release-ocs-cisco-router-default-password-scanner) (the recipe uses a versioned archive).

```bash
# see exact recipe steps below
```

## Dependencies (from recipe)

- **Build tools**: `gcc`, `git`

## Exact build steps from the recipe

```bash
cd $pkgname

  gcc -Wall -Wextra -lpthread -std=gnu89 "OCS-$pkgver.c" -o $pkgname
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[cisco-ocs]] · [[BlackArch Tools]]

