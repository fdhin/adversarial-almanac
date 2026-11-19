---
tool: "pown"
build-system: "node"
upstream: "https://github.com/pownjs/pown"
recipe: "blackarch"
source: "https://github.com/pownjs/pown"
license: "MIT"
blackarch-pkgver: "332.0e32edf"
---

# Installing pown

> Security testing and exploitation toolkit built on top of Node.js and NPM.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pown) (version `332.0e32edf`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S pown    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `nodejs` + `npm`):**

**Source:** download the release from the [upstream site](https://github.com/pownjs/pown) (the recipe uses a versioned archive).

```bash
npm install
sudo npm install -g .
```

## Dependencies (from recipe)

- **Build tools**: `git`, `nodejs`, `npm`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[pown]] · [[BlackArch Tools]]

