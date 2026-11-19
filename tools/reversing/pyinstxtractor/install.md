---
tool: "pyinstxtractor"
build-system: "scripts"
upstream: "https://github.com/extremecoders-re/pyinstxtractor"
recipe: "blackarch"
source: "https://github.com/extremecoders-re/pyinstxtractor"
license: "GPL3"
blackarch-pkgver: "2026.07.r0.g815d31c"
---

# Installing pyinstxtractor

> PyInstaller Extractor.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pyinstxtractor) (version `2026.07.r0.g815d31c`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S pyinstxtractor    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/extremecoders-re/pyinstxtractor) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[reversing]] · [[pyinstxtractor]] · [[BlackArch Tools]]

