---
tool: "pastejacker"
build-system: "python"
upstream: "https://github.com/D4Vinci/PasteJacker"
recipe: "blackarch"
source: "https://github.com/D4Vinci/PasteJacker"
license: "custom:unknown"
blackarch-pkgver: "12.ed9f153"
---

# Installing pastejacker

> Hacking systems with the automation of PasteJacking attacks.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pastejacker) (version `12.ed9f153`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S pastejacker    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

```bash
git clone https://github.com/D4Vinci/PasteJacker.git
cd PasteJacker
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `gnureadline`, `jinja`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `metasploit`
  - `ncurses`
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[automation]] · [[pastejacker]] · [[BlackArch Tools]]

