---
tool: "kimi"
build-system: "scripts"
upstream: "https://github.com/ChaitanyaHaritash/kimi"
recipe: "blackarch"
source: "https://github.com/ChaitanyaHaritash/kimi"
license: "custom:unknown"
blackarch-pkgver: "28.e7cafda"
---

# Installing kimi

> Script to generate malicious debian packages (debain trojans).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/kimi) (version `28.e7cafda`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S kimi    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ChaitanyaHaritash/kimi) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[backdoor]] · [[kimi]] · [[BlackArch Tools]]

