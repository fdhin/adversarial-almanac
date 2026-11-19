---
tool: "android-udev-rules"
build-system: "scripts"
upstream: "https://github.com/bbqlinux/android-udev-rules"
recipe: "blackarch"
source: "https://github.com/bbqlinux/android-udev-rules"
license: "GPL-1.0-or-later"
blackarch-pkgver: "20260922.r3.ga9c2aeb"
---

# Installing android-udev-rules

> Android udev rules.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/android-udev-rules) (version `20260922.r3.ga9c2aeb`, license GPL-1.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S android-udev-rules    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/bbqlinux/android-udev-rules) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[mobile]] · [[android-udev-rules]] · [[BlackArch Tools]]

