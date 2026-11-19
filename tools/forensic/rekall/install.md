---
tool: "rekall"
build-system: "scripts"
upstream: "https://github.com/google/rekall"
recipe: "blackarch"
source: "https://github.com/google/rekall"
license: "GPL-2.0-only"
blackarch-pkgver: "1.7.2rc1.r68.g55d1925"
---

# Installing rekall

> Memory Forensic Framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/rekall) (version `1.7.2rc1.r68.g55d1925`, license GPL-2.0-only).

## Arch / BlackArch

```bash
sudo pacman -S rekall    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/google/rekall) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pip`, `setuptools`, `virtualenv`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[forensic]] · [[rekall]] · [[BlackArch Tools]]

