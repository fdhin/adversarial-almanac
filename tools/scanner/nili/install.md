---
tool: "nili"
build-system: "python"
upstream: "https://github.com/niloofarkheirkhah/nili"
recipe: "blackarch"
source: "https://github.com/niloofarkheirkhah/nili"
license: "GPL-3.0-or-later"
blackarch-pkgver: "0.1.1.r4.g285220a"
---

# Installing nili

> Tool for Network Scan, Man in the Middle, Protocol Reverse Engineering and Fuzzing.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/nili) (version `0.1.1.r4.g285220a`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S nili    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/niloofarkheirkhah/nili) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `scapy`, `setuptools`
- **Build tools**: `git`
- **System libraries** (Arch name — translate for your distro):
  - `netzob`
  - `python`

## Exact build steps from the recipe

```bash
cd $pkgname

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[scanner]] · [[nili]] · [[BlackArch Tools]]

