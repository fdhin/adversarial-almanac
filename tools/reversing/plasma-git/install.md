---
tool: "plasma-git"
ebuild-package: "plasma-git"
build-system: "python"
upstream: "https://github.com/plasma-disassembler/plasma"
recipe: "archstrike-pkgbuild"
recipe-url: "https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/plasma-git"
version: "20190304.r922"
license: "GPL3"
---

# Installing plasma-git

> Interactive disassembler for x86/ARM/MIPS. Generates indented pseudo-code with colored syntax code.

Recipe source of truth: ArchStrike [`PKGBUILD`](https://github.com/ArchStrike/ArchStrike/tree/master/archstrike/plasma-git) (version `20190304.r922`).

## Arch / BlackArch — via the [archstrike] repo

```bash
# enable the archstrike repo (keyring + Server line) per https://archstrike.org/setup
sudo pacman -S plasma-git
```

The same PKGBUILD is usually on the AUR (`paru -S plasma-git`).

## Any other Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/plasma-disassembler/plasma) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `capstone`, `future`, `keystone`, `msgpack`, `nose`, `pefile`, `pyelftools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `binutils`
  - `libxcrypt`

## Exact build steps from the recipe

```bash
cd "${srcdir}/${pkgname}"
  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry detected — see recipe for layout

Backlinks: [[reversing]] · [[plasma-git]] · [[ArchStrike Gap Analysis]]
