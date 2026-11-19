---
tool: "unstrip"
build-system: "scripts"
upstream: "https://github.com/pzread/unstrip"
recipe: "blackarch"
source: "https://github.com/pzread/unstrip"
license: "GPL3"
blackarch-pkgver: "13.05e00c2"
---

# Installing unstrip

> ELF Unstrip Tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/unstrip) (version `13.05e00c2`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S unstrip    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/pzread/unstrip) (the recipe uses a versioned archive).

```bash
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Python (pip)**: `capstone`, `msgpack`, `pyelftools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `sqlite3`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[disassembler]] · [[unstrip]] · [[BlackArch Tools]]

