---
tool: "bashfuscator"
build-system: "python"
upstream: "https://github.com/Bashfuscator/Bashfuscator"
recipe: "blackarch"
source: "https://github.com/Bashfuscator/Bashfuscator"
license: "MIT"
blackarch-pkgver: "338.7487348"
---

# Installing bashfuscator

> Fully configurable and extendable Bash obfuscation framework.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bashfuscator) (version `338.7487348`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S bashfuscator    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/Bashfuscator/Bashfuscator.git
cd Bashfuscator
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `argcomplete`, `pyperclip`, `setuptools`
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

Backlinks: [[automation]] · [[bashfuscator]] · [[BlackArch Tools]]

