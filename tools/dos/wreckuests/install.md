---
tool: "wreckuests"
build-system: "scripts"
upstream: "https://github.com/JamesJGoodwin/wreckuests"
recipe: "blackarch"
source: "https://github.com/JamesJGoodwin/wreckuests"
license: "MIT"
blackarch-pkgver: "75.69b6c27"
---

# Installing wreckuests

> Yet another one hard-hitting tool to run DDoS attacks with HTTP-flood.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wreckuests) (version `75.69b6c27`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S wreckuests    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/JamesJGoodwin/wreckuests) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `netaddr`, `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[dos]] · [[wreckuests]] · [[BlackArch Tools]]

