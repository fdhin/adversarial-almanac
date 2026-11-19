---
tool: "xxxpwn"
build-system: "scripts"
upstream: "https://github.com/feakk/xxxpwn"
recipe: "blackarch"
source: "https://github.com/feakk/xxxpwn"
license: "GPL3"
blackarch-pkgver: "12.fcca6b0"
---

# Installing xxxpwn

> A tool Designed for blind optimized XPath 1 injection attacks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/xxxpwn) (version `12.fcca6b0`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S xxxpwn    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/feakk/xxxpwn) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[xxxpwn]] · [[BlackArch Tools]]

