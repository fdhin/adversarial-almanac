---
tool: "intensio-obfuscator"
build-system: "python"
upstream: "https://github.com/Hnfull/Intensio-Obfuscator"
recipe: "blackarch"
source: "https://github.com/Hnfull/Intensio-Obfuscator"
license: "MIT"
blackarch-pkgver: "280.f66a22b"
provides: ["intensio_obfuscator"]
---

# Installing intensio-obfuscator

> Obfuscate a python code 2 and 3.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/intensio-obfuscator) (version `280.f66a22b`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S intensio-obfuscator    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Hnfull/Intensio-Obfuscator.git
cd Intensio-Obfuscator
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `progress`, `setuptools`, `tqdm`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- Commands: `/usr/bin/intensio_obfuscator`

Backlinks: [[misc]] · [[intensio-obfuscator]] · [[BlackArch Tools]]

