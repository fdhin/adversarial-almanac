---
tool: "shootback"
build-system: "scripts"
upstream: "https://github.com/aploium/shootback"
recipe: "blackarch"
source: "https://github.com/aploium/shootback"
license: "MIT"
blackarch-pkgver: "85.2c6e6a8"
provides: ["shootback-master"]
---

# Installing shootback

> A reverse TCP tunnel let you access target behind NAT or firewall.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/shootback) (version `85.2c6e6a8`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S shootback    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/aploium/shootback) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- Commands: `/usr/bin/shootback-master`

Backlinks: [[backdoor]] · [[shootback]] · [[BlackArch Tools]]

