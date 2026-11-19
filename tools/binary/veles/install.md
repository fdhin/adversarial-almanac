---
tool: "veles"
build-system: "scripts"
upstream: "https://codisec.com/veles/"
recipe: "blackarch"
aur-package: "veles"
source: "https://codisec.com/veles/"
license: "Apache-2.0"
blackarch-pkgver: "2018.05"
---

# Installing veles

> New open source tool for binary data analysis.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/veles) (version `2018.05`, license Apache-2.0).

## Arch / BlackArch

```bash
sudo pacman -S veles    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://codisec.com/veles/) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `msgpack`, `pbr`, `pyopenssl`, `six`
- **System libraries** (Arch name — translate for your distro):
  - `double-conversion`
  - `python`
  - `qt5-base`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory
- Data: `/usr/share/veles-server`

Backlinks: [[binary]] · [[veles]] · [[BlackArch Tools]]

