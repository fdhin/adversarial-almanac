---
tool: "tor-router"
build-system: "scripts"
upstream: "https://github.com/Edu4rdSHL/tor-router"
recipe: "blackarch"
aur-package: "tor-router"
source: "https://github.com/Edu4rdSHL/tor-router"
license: "GPL3"
blackarch-pkgver: "22.5b79c1c"
---

# Installing tor-router

> A tool that allow you to make TOR your default gateway and send all internet connections under TOR (as transparent proxy) for increase privacy/anonymity without extra unnecessary code.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/tor-router) (version `22.5b79c1c`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S tor-router    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/Edu4rdSHL/tor-router) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `tor`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[defensive]] · [[tor-router]] · [[BlackArch Tools]]

