---
tool: "jd-cli"
build-system: "scripts"
upstream: "https://github.com/kwart/jd-cli"
recipe: "blackarch"
aur-package: "jd-cli"
source: "https://github.com/kwart/jd-cli"
license: "GPL3"
blackarch-pkgver: "1.2.0"
---

# Installing jd-cli

> Command line Java Decompiler.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/jd-cli) (version `1.2.0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S jd-cli    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/kwart/jd-cli) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[decompiler]] · [[jd-cli]] · [[BlackArch Tools]]

