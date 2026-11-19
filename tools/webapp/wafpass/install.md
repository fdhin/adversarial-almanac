---
tool: "wafpass"
build-system: "scripts"
upstream: "https://github.com/wafpassproject/wafpass"
recipe: "blackarch"
source: "https://github.com/wafpassproject/wafpass"
license: "MIT"
blackarch-pkgver: "50.4211785"
---

# Installing wafpass

> Analysing parameters with all payloads' bypass methods, aiming at benchmarking security solutions like WAF.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wafpass) (version `50.4211785`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S wafpass    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/wafpassproject/wafpass) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `requests`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python3`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[wafpass]] · [[BlackArch Tools]]

