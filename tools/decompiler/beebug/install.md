---
tool: "beebug"
build-system: "scripts"
upstream: "https://github.com/invictus1306/beebug"
recipe: "blackarch"
source: "https://github.com/invictus1306/beebug"
license: "GPL3"
blackarch-pkgver: "25.cddb375"
---

# Installing beebug

> A tool for checking exploitability.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/beebug) (version `25.cddb375`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S beebug    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/invictus1306/beebug) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `pydot`, `pygraphviz`, `pyqtgraph`, `r2pipe`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `radare2`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[decompiler]] · [[beebug]] · [[BlackArch Tools]]

