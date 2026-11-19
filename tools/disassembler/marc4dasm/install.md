---
tool: "marc4dasm"
build-system: "scripts"
upstream: "https://github.com/ApertureLabsLtd/marc4dasm"
recipe: "blackarch"
source: "https://github.com/ApertureLabsLtd/marc4dasm"
license: "custom:unknown"
blackarch-pkgver: "6.f11860f"
---

# Installing marc4dasm

> A disassembler for the Atmel MARC4 (a 4 bit Harvard micro).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/marc4dasm) (version `6.f11860f`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S marc4dasm    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ApertureLabsLtd/marc4dasm) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[disassembler]] · [[marc4dasm]] · [[BlackArch Tools]]

