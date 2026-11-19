---
tool: "httprint"
build-system: "scripts"
upstream: "https://www.net-square.com/httprint.html"
recipe: "blackarch"
aur-package: "httprint"
source: "https://www.net-square.com/httprint.html"
license: "custom:unknown"
blackarch-pkgver: "301"
---

# Installing httprint

> A web server fingerprinting tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/httprint) (version `301`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S httprint    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://www.net-square.com/httprint.html) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fingerprint]] · [[httprint]] · [[BlackArch Tools]]

