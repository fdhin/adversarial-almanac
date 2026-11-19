---
tool: "pwned"
build-system: "node"
upstream: "https://github.com/wKovacs64/pwned"
recipe: "blackarch"
source: "https://github.com/wKovacs64/pwned"
license: "MIT"
blackarch-pkgver: "3187.084cbb4"
---

# Installing pwned

> A command-line tool for querying the 'Have I been pwned?' service.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pwned) (version `3187.084cbb4`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S pwned    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source (needs `nodejs` + `npm`):**

**Source:** download the release from the [upstream site](https://github.com/wKovacs64/pwned) (the recipe uses a versioned archive).

```bash
npm install
sudo npm install -g .
```

## Dependencies (from recipe)

- **Build tools**: `git`, `nodejs`, `npm`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[pwned]] · [[BlackArch Tools]]

