---
tool: "howmanypeoplearearound"
build-system: "python"
upstream: "https://github.com/schollz/howmanypeoplearearound"
recipe: "blackarch"
source: "https://github.com/schollz/howmanypeoplearearound"
license: "MIT"
blackarch-pkgver: "123.b05e06a"
---

# Installing howmanypeoplearearound

> Count the number of people around you by monitoring wifi signals.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/howmanypeoplearearound) (version `123.b05e06a`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S howmanypeoplearearound    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/schollz/howmanypeoplearearound) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `click`, `netifaces`, `pick`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`
  - `wireshark-cli`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[recon]] · [[howmanypeoplearearound]] · [[BlackArch Tools]]

