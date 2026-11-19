---
tool: "bless"
build-system: "scripts"
upstream: "https://github.com/afrantzis/bless"
recipe: "blackarch"
aur-package: "bless"
source: "https://github.com/afrantzis/bless"
license: "GPL-1.0-or-later"
blackarch-pkgver: "v0.6.3.r3.g103fbd6"
---

# Installing bless

> High-quality, full-featured hex editor.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bless) (version `v0.6.3.r3.g103fbd6`, license GPL-1.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S bless    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/afrantzis/bless) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `gtk-sharp-2`
  - `itstool`
  - `meson`
  - `ninja`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[bless]] · [[BlackArch Tools]]

