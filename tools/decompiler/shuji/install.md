---
tool: "shuji"
build-system: "node"
upstream: "https://github.com/paazmaya/shuji"
recipe: "blackarch"
source: "https://github.com/paazmaya/shuji"
license: "MIT"
blackarch-pkgver: "0.8.0.r141.ge7bce98"
---

# Installing shuji

> Reverse engineering JavaScript and CSS sources from sourcemaps.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/shuji) (version `0.8.0.r141.ge7bce98`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S shuji    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `nodejs` + `npm`):**

**Source:** download the release from the [upstream site](https://github.com/paazmaya/shuji) (the recipe uses a versioned archive).

```bash
npm install
sudo npm install -g .
```

## Dependencies (from recipe)

- **Build tools**: `git`, `nodejs`, `npm`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[decompiler]] · [[shuji]] · [[BlackArch Tools]]

