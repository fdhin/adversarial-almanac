---
tool: "skyjack"
build-system: "scripts"
upstream: "https://github.com/samyk/skyjack"
recipe: "blackarch"
source: "https://github.com/samyk/skyjack"
license: "GPL"
blackarch-pkgver: "16.24e3878"
---

# Installing skyjack

> Takes over Parrot drones, deauthenticating their true owner and taking over control, turning them into zombie drones under your own control.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/skyjack) (version `16.24e3878`, license GPL).

## Arch / BlackArch

```bash
sudo pacman -S skyjack    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/samyk/skyjack) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `net-tools`
  - `node-ar-drone`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[drone]] · [[skyjack]] · [[BlackArch Tools]]

