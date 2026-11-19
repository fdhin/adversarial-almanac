---
tool: "redeye"
build-system: "scripts"
upstream: "https://github.com/cisagov/RedEye"
recipe: "blackarch"
source: "https://github.com/cisagov/RedEye"
license: "BSD-3"
blackarch-pkgver: "0.9.4"
---

# Installing redeye

> Visual analytic tool supporting Red & Blue Team operations.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/redeye) (version `0.9.4`, license BSD-3).

## Arch / BlackArch

```bash
sudo pacman -S redeye    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/cisagov/RedEye) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[redeye]] · [[BlackArch Tools]]

