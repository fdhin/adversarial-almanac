---
tool: "webshells"
build-system: "scripts"
upstream: "https://github.com/BlackArch/webshells"
recipe: "blackarch"
source: "https://github.com/BlackArch/webshells"
license: "custom:unknown"
blackarch-pkgver: "46.e8e1a37"
kali-package: "webshells"
kali-version: "1.1+kali8"
---

# Installing webshells

> Web Backdoors.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/webshells) (version `46.e8e1a37`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S webshells    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install webshells    # Kali package version 1.1+kali8
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/BlackArch/webshells) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[webshells]] · [[BlackArch Tools]]

