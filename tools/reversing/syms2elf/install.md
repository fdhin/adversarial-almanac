---
tool: "syms2elf"
build-system: "scripts"
upstream: "https://github.com/danigargu/syms2elf"
recipe: "blackarch"
source: "https://github.com/danigargu/syms2elf"
license: "GPL3"
blackarch-pkgver: "12.329c2ce"
---

# Installing syms2elf

> A plugin for Hex-Ray's IDA Pro and radare2 to export the symbols recognized to the ELF symbol table.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/syms2elf) (version `12.329c2ce`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S syms2elf    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/danigargu/syms2elf) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[reversing]] · [[syms2elf]] · [[BlackArch Tools]]

