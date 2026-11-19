---
tool: "openstego"
build-system: "scripts"
upstream: "https://github.com/syvaidya/openstego/releases"
recipe: "blackarch"
aur-package: "openstego"
source: "https://github.com/syvaidya/openstego/releases"
license: "GPL2"
blackarch-pkgver: "0.8.6"
---

# Installing openstego

> A tool implemented in Java for generic steganography, with support for password-based encryption of the data.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/openstego) (version `0.8.6`, license GPL2).

## Arch / BlackArch

```bash
sudo pacman -S openstego    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/syvaidya/openstego/releases) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[openstego]] · [[BlackArch Tools]]

