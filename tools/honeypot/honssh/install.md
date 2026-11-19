---
tool: "honssh"
build-system: "scripts"
upstream: ""
recipe: "blackarch"
source: ""
license: "BSD"
blackarch-pkgver: "1.1.2.r2.g821ce87"
---

# Installing honssh

> A high-interaction Honey Pot solution designed to log all SSH communications between a client and server.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/honssh) (version `1.1.2.r2.g821ce87`, license BSD).

## Arch / BlackArch

```bash
sudo pacman -S honssh    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the the upstream site (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[honeypot]] · [[honssh]] · [[BlackArch Tools]]

