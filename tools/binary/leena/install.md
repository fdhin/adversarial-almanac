---
tool: "leena"
build-system: "node"
upstream: "https://github.com/mmicu/leena"
recipe: "blackarch"
source: "https://github.com/mmicu/leena"
license: "GPL3"
blackarch-pkgver: "2.5119f56"
---

# Installing leena

> Symbolic execution engine for JavaScript

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/leena) (version `2.5119f56`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S leena    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `nodejs` + `npm`):**

**Source:** download the release from the [upstream site](https://github.com/mmicu/leena) (the recipe uses a versioned archive).

```bash
npm install
sudo npm install -g .
```

## Dependencies (from recipe)

- **Build tools**: `git`, `nodejs`, `npm`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[binary]] · [[leena]] · [[BlackArch Tools]]

