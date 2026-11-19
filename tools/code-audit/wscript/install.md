---
tool: "wscript"
build-system: "scripts"
upstream: "https://github.com/mrpapercut/wscript"
recipe: "blackarch"
source: "https://github.com/mrpapercut/wscript"
license: "MIT"
blackarch-pkgver: "201.0410be2"
---

# Installing wscript

> Emulator/tracer of the Windows Script Host functionality.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/wscript) (version `201.0410be2`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S wscript    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/mrpapercut/wscript) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`, `npm`
- **System libraries** (Arch name — translate for your distro):
  - `xdg-utils`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[code-audit]] · [[wscript]] · [[BlackArch Tools]]

