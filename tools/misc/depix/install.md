---
tool: "depix"
build-system: "scripts"
upstream: "https://github.com/beurtschipper/Depix"
recipe: "blackarch"
source: "https://github.com/beurtschipper/Depix"
license: "custom:unknown"
blackarch-pkgver: "64.850b268"
---

# Installing depix

> A tool for recovering passwords from pixelized screenshots.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/depix) (version `64.850b268`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S depix    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/beurtschipper/Depix.git
cd Depix
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pillow`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[depix]] · [[BlackArch Tools]]

