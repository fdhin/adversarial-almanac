---
tool: "elidecode"
build-system: "scripts"
upstream: "https://github.com/DeveloppSoft/EliDecode"
recipe: "blackarch"
source: "https://github.com/DeveloppSoft/EliDecode"
license: "GPL3"
blackarch-pkgver: "48.38fa5ba"
---

# Installing elidecode

> A tool to decode obfuscated shellcodes using the unicorn-engine for the emulation and the capstone-engine to print the asm code.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/elidecode) (version `48.38fa5ba`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S elidecode    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/DeveloppSoft/EliDecode.git
cd EliDecode
# run scripts directly (see recipe for interpreter and layout)
```

## Dependencies (from recipe)

- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `bash`
  - `capstone`
  - `python2`
  - `unicorn`
- ⚠️ Python 2 only deps: python2-argparse, python2-capstone, python2-setuptools, python2-unicorn (legacy; needs python2)

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[reversing]] · [[elidecode]] · [[BlackArch Tools]]

