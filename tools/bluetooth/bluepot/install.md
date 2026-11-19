---
tool: "bluepot"
build-system: "scripts"
upstream: "https://github.com/andrewmichaelsmith/bluepot"
recipe: "blackarch"
source: "https://github.com/andrewmichaelsmith/bluepot"
license: "GPL3"
blackarch-pkgver: "0.2"
---

# Installing bluepot

> A Bluetooth Honeypot written in Java, it runs on Linux.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bluepot) (version `0.2`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S bluepot    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/andrewmichaelsmith/bluepot) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `java-environment`
- **System libraries** (Arch name — translate for your distro):
  - `gksu`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[bluetooth]] · [[bluepot]] · [[BlackArch Tools]]

