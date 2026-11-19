---
tool: "scratchabit"
build-system: "scripts"
upstream: "https://github.com/pfalcon/ScratchABit"
recipe: "blackarch"
source: "https://github.com/pfalcon/ScratchABit"
license: "custom:unknown"
blackarch-pkgver: "565.d93d759"
---

# Installing scratchabit

> Easily retargetable and hackable interactive disassembler with IDAPython-compatible plugin API.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/scratchabit) (version `565.d93d759`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S scratchabit    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/pfalcon/ScratchABit) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python3`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[disassembler]] · [[scratchabit]] · [[BlackArch Tools]]

