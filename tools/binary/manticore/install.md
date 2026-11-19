---
tool: "manticore"
build-system: "python"
upstream: "https://github.com/trailofbits/manticore"
recipe: "blackarch"
aur-package: "manticore"
source: "https://github.com/trailofbits/manticore"
license: "AGPL-3.0-or-later"
blackarch-pkgver: "0.3.7.r88.gcdf6c4b"
---

# Installing manticore

> Symbolic execution tool.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/manticore) (version `0.3.7.r88.gcdf6c4b`, license AGPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S manticore    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/trailofbits/manticore) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `capstone`, `cbor2`, `crytic-compile`, `intervaltree`, `ply`, `prettytable`, `protobuf`, `pyelftools`, `pyevmasm`, `pysha3`, `redis`, `rlp`, `setuptools`, `unicorn`, `wasm`, `yaml`, `z3-solver`
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

Backlinks: [[binary]] · [[manticore]] · [[BlackArch Tools]]

