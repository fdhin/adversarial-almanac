---
tool: "seeker"
build-system: "scripts"
upstream: "https://github.com/thewhiteh4t/seeker"
recipe: "blackarch"
aur-package: "seeker"
source: "https://github.com/thewhiteh4t/seeker"
license: "MIT"
blackarch-pkgver: "383.47298d9"
---

# Installing seeker

> Accurately Locate People using Social Engineering.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/seeker) (version `383.47298d9`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S seeker    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/thewhiteh4t/seeker) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `openssh`
  - `php`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[social]] · [[seeker]] · [[BlackArch Tools]]

