---
tool: "one-lin3r"
build-system: "python"
upstream: "https://github.com/D4Vinci/One-Lin3r"
recipe: "blackarch"
source: "https://github.com/D4Vinci/One-Lin3r"
license: "GPL-1.0-or-later"
blackarch-pkgver: "66.711a040"
---

# Installing one-lin3r

> Gives you one-liners that aids in penetration testing and more.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/one-lin3r) (version `66.711a040`, license GPL-1.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S one-lin3r    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/D4Vinci/One-Lin3r.git
cd One-Lin3r
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `prompt_toolkit`, `pyperclip`, `setuptools`, `terminaltables`
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

Backlinks: [[misc]] · [[one-lin3r]] · [[BlackArch Tools]]

