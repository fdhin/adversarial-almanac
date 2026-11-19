---
tool: "subbrute"
build-system: "scripts"
upstream: "https://github.com/TheRook/subbrute"
recipe: "blackarch"
aur-package: "subbrute"
source: "https://github.com/TheRook/subbrute"
license: "GPL-3.0-or-later"
blackarch-pkgver: "77.r30.g07d2925"
---

# Installing subbrute

> A DNS meta-query spider that enumerates DNS records, and subdomains.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/subbrute) (version `77.r30.g07d2925`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S subbrute    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/TheRook/subbrute) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `dnslib`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[subbrute]] · [[BlackArch Tools]]

