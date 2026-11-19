---
tool: "pinkerton"
build-system: "scripts"
upstream: "https://github.com/oppsec/Pinkerton"
recipe: "blackarch"
source: "https://github.com/oppsec/Pinkerton"
license: "MIT"
blackarch-pkgver: "1.7.r13.g6deb355"
---

# Installing pinkerton

> JavaScript file crawler and secret finder.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pinkerton) (version `1.7.r13.g6deb355`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S pinkerton    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/oppsec/Pinkerton) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `jsbeautifier`, `requests`, `rich`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `openssl` — Debian libssl-dev / Fedora openssl-devel
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[webapp]] · [[pinkerton]] · [[BlackArch Tools]]

