---
tool: "thedorkbox"
build-system: "scripts"
upstream: "https://github.com/cybersafeblr/thedorkbox"
recipe: "blackarch"
source: "https://github.com/cybersafeblr/thedorkbox"
license: "GPL3"
blackarch-pkgver: "7.43852d3"
---

# Installing thedorkbox

> Comprehensive collection of Google Dorks & OSINT techniques to find Confidential Data.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/thedorkbox) (version `7.43852d3`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S thedorkbox    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/cybersafeblr/thedorkbox) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `php`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[thedorkbox]] · [[BlackArch Tools]]

