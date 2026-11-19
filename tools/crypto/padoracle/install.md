---
tool: "padoracle"
build-system: "node"
upstream: "https://github.com/imyelo/padoracle"
recipe: "blackarch"
source: "https://github.com/imyelo/padoracle"
license: "Apache-2.0"
blackarch-pkgver: "0.1.1.r30.g535ae89"
---

# Installing padoracle

> Padding Oracle Attack with Node.js.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/padoracle) (version `0.1.1.r30.g535ae89`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S padoracle    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `nodejs` + `npm`):**

**Source:** download the release from the [upstream site](https://github.com/imyelo/padoracle) (the recipe uses a versioned archive).

```bash
npm install
sudo npm install -g .
```

## Dependencies (from recipe)

- **Build tools**: `git`, `nodejs`, `npm`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[crypto]] · [[padoracle]] · [[BlackArch Tools]]

