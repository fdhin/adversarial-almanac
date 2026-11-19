---
tool: "binaryninja"
build-system: "scripts"
upstream: "https://binary.ninja/free/"
recipe: "blackarch"
source: "https://cdn.binary.ninja/installers/binaryninja_free_linux.zip"
license: "custom:commercial, evaluation"
blackarch-pkgver: "6.0.10601"
---

# Installing binaryninja

> A new kind of reversing platform (demo version).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/binaryninja) (version `6.0.10601`, license custom:commercial, evaluation).

## Arch / BlackArch

```bash
sudo pacman -S binaryninja    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
curl -fLO https://cdn.binary.ninja/installers/binaryninja_free_linux.zip
tar xf binaryninja_free_linux.zip
cd binaryninja_free_linux
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **System libraries** (Arch name — translate for your distro):
  - `libglvnd`
  - `libxkbcommon-x11`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[reversing]] · [[binaryninja]] · [[BlackArch Tools]]

