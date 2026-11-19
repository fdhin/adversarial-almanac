---
tool: "fuddly"
build-system: "python"
upstream: "https://github.com/k0retux/fuddly"
recipe: "blackarch"
source: "https://github.com/k0retux/fuddly"
license: "GPL3"
blackarch-pkgver: "0.30.dev.r420.g84b0846"
---

# Installing fuddly

> Fuzzing and Data Manipulation Framework (for GNU/Linux).

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/fuddly) (version `0.30.dev.r420.g84b0846`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S fuddly    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/k0retux/fuddly) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `argcomplete`, `build`, `crcmod`, `graphviz`, `matplotlib`, `paramiko`, `pip`, `pycups`, `pyserial`, `pyxdg`, `rpyc`, `xtermcolor`, `z3-solver`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python -m build --wheel --outdir="$startdir/dist"
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[fuddly]] · [[BlackArch Tools]]

