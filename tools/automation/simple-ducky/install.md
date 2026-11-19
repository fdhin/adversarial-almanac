---
tool: "simple-ducky"
build-system: "scripts"
upstream: "https://code.google.com/p/simple-ducky-payload-generator"
recipe: "blackarch"
source: "https://code.google.com/p/simple-ducky-payload-generator"
license: "GPL3"
blackarch-pkgver: "20.f15079e"
---

# Installing simple-ducky

> A payload generator.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/simple-ducky) (version `20.f15079e`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S simple-ducky    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://code.google.com/p/simple-ducky-payload-generator) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[simple-ducky]] · [[BlackArch Tools]]

