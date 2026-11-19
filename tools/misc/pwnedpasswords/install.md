---
tool: "pwnedpasswords"
build-system: "python"
upstream: "https://github.com/lionheart/pwnedpasswords"
recipe: "blackarch"
source: "https://github.com/lionheart/pwnedpasswords"
license: "Apache"
blackarch-pkgver: "3.0.0.r7.ge33d4bb"
---

# Installing pwnedpasswords

> Generate and verify pwnedpasswords check digits.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/pwnedpasswords) (version `3.0.0.r7.ge33d4bb`, license Apache).

## Arch / BlackArch

```bash
sudo pacman -S pwnedpasswords    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/lionheart/pwnedpasswords) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `future`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $_pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[misc]] · [[pwnedpasswords]] · [[BlackArch Tools]]

