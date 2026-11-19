---
tool: "matroschka"
build-system: "scripts"
upstream: "https://github.com/fgrimme/Matroschka"
recipe: "blackarch"
source: "https://github.com/fgrimme/Matroschka"
license: "GPL-3.0-or-later"
blackarch-pkgver: "58.2f026a4"
---

# Installing matroschka

> Python steganography tool to hide images or text in images.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/matroschka) (version `58.2f026a4`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S matroschka    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/fgrimme/Matroschka.git
cd Matroschka
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`
- ⚠️ Python 2 only deps: python2-pillow (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[stego]] · [[matroschka]] · [[BlackArch Tools]]

