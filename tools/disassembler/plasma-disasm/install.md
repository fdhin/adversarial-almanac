---
tool: "plasma-disasm"
build-system: "python"
upstream: "https://github.com/joelpx/plasma"
recipe: "blackarch"
source: "https://github.com/joelpx/plasma"
license: "GPL-3.0-or-later"
blackarch-pkgver: "922.ec7df9b"
---

# Installing plasma-disasm

> An interactive disassembler for x86/ARM/MIPS. It can generates indented pseudo-code with colored syntax.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/plasma-disasm) (version `922.ec7df9b`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S plasma-disasm    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/joelpx/plasma) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `capstone`, `msgpack`, `nose`, `pefile`, `pip`, `pyelftools`, `pyqt4`, `setuptools`
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

Backlinks: [[disassembler]] · [[plasma-disasm]] · [[BlackArch Tools]]

