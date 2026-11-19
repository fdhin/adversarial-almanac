---
tool: "chiasm-shell"
build-system: "python"
upstream: "https://github.com/0xbc/chiasm-shell"
recipe: "blackarch"
source: "https://github.com/0xbc/chiasm-shell"
license: "MIT"
blackarch-pkgver: "33.e20ed9f"
---

# Installing chiasm-shell

> Python-based interactive assembler/disassembler CLI, powered byKeystone/Capstone.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/chiasm-shell) (version `33.e20ed9f`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S chiasm-shell    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/0xbc/chiasm-shell) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `capstone`, `keystone`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[disassembler]] · [[chiasm-shell]] · [[BlackArch Tools]]

