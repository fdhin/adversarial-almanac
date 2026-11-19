---
tool: "seekr"
build-system: "scripts"
upstream: "https://github.com/seekr-osint/seekr"
recipe: "blackarch"
aur-package: "seekr"
source: "https://github.com/seekr-osint/seekr"
license: "GPL3"
blackarch-pkgver: "0.4.0"
---

# Installing seekr

> A multi-purpose OSINT toolkit with a neat web-interface.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/seekr) (version `0.4.0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S seekr    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/seekr-osint/seekr) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[seekr]] · [[BlackArch Tools]]

