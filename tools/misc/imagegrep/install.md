---
tool: "imagegrep"
build-system: "scripts"
upstream: "https://github.com/coderofsalvation/imagegrep-bash"
recipe: "blackarch"
source: "https://github.com/coderofsalvation/imagegrep-bash"
license: "LGPL"
blackarch-pkgver: "8.1f14af2"
---

# Installing imagegrep

> Grep word in pdf or image based on OCR.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/imagegrep) (version `8.1f14af2`, license LGPL).

## Arch / BlackArch

```bash
sudo pacman -S imagegrep    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/coderofsalvation/imagegrep-bash.git
cd imagegrep-bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `imagemagick` — Debian imagemagick / Fedora ImageMagick
  - `tesseract`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[imagegrep]] · [[BlackArch Tools]]

