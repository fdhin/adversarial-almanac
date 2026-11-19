---
tool: "retire"
build-system: "node"
upstream: "http://retirejs.github.io/retire.js/"
recipe: "blackarch"
source: "http://retirejs.github.io/retire.js/"
license: "Apache"
blackarch-pkgver: "6.1.0.r6.g970fc88"
---

# Installing retire

> Scanner detecting the use of JavaScript libraries with known vulnerabilities.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/retire) (version `6.1.0.r6.g970fc88`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S retire    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `nodejs` + `npm`):**

**Source:** download the release from the [upstream site](http://retirejs.github.io/retire.js/) (the recipe uses a versioned archive).

```bash
npm install
sudo npm install -g .
```

## Dependencies (from recipe)

- **Build tools**: `git`, `nodejs`, `npm`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[retire]] · [[BlackArch Tools]]

