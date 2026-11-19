---
tool: "firecat"
build-system: "scripts"
upstream: "https://github.com/BishopFox/firecat"
recipe: "blackarch"
source: "https://github.com/BishopFox/firecat"
license: "MIT"
blackarch-pkgver: "6.b5205c8"
---

# Installing firecat

> A penetration testing tool that allows you to punch reverse TCP tunnels out of a compromised network.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/firecat) (version `6.b5205c8`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S firecat    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/BishopFox/firecat) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[networking]] · [[firecat]] · [[BlackArch Tools]]

