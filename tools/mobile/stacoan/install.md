---
tool: "stacoan"
build-system: "scripts"
upstream: "https://github.com/vincentcox/StaCoAn"
recipe: "blackarch"
source: "https://github.com/vincentcox/StaCoAn"
license: "MIT"
blackarch-pkgver: "0.90"
---

# Installing stacoan

> Crossplatform tool which aids developers, bugbounty hunters and ethical hackers performing static code analysis on mobile applications.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/stacoan) (version `0.90`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S stacoan    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/vincentcox/StaCoAn) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `unzip`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[stacoan]] · [[BlackArch Tools]]

