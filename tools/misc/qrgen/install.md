---
tool: "qrgen"
build-system: "scripts"
upstream: "https://github.com/h0nus/QRGen"
recipe: "blackarch"
aur-package: "qrgen"
source: "https://github.com/h0nus/QRGen"
license: "custom:unknown"
blackarch-pkgver: "37.82a015b"
---

# Installing qrgen

> Simple script for generating Malformed QRCodes.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/qrgen) (version `37.82a015b`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S qrgen    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/h0nus/QRGen.git
cd QRGen
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pillow`, `qrcode`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[qrgen]] · [[BlackArch Tools]]

