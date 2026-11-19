---
tool: "grokevt"
build-system: "scripts"
upstream: "https://projects.sentinelchicken.org/grokevt/"
recipe: "blackarch"
source: "https://projects.sentinelchicken.org/grokevt/"
license: "GPL-3.0-or-later"
blackarch-pkgver: "0.5.0.6.1.r0.gfb16294"
kali-package: "grokevt"
kali-version: "0.5.0-6.1"
---

# Installing grokevt

> A collection of scripts built for reading Windows® NT/2K/XP/2K eventlog files.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/grokevt) (version `0.5.0.6.1.r0.gfb16294`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S grokevt    # needs the [blackarch] repo enabled
```

## Kali / Debian-based

```bash
sudo apt install grokevt    # Kali package version 0.5.0-6.1
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://projects.sentinelchicken.org/grokevt/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `cpio`
  - `python`
  - `reglookup`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[grokevt]] · [[BlackArch Tools]]

