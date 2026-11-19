---
tool: "habu"
build-system: "python"
upstream: "https://github.com/portantier/habu"
recipe: "blackarch"
source: "https://github.com/portantier/habu"
license: "custom:unknown"
blackarch-pkgver: "0.1.6.r171.g9868861"
---

# Installing habu

> Python Network Hacking Toolkit.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/habu) (version `0.1.6.r171.g9868861`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S habu    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/portantier/habu) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `click`, `delegator.py`, `requests`, `requests-cache`, `scapy`, `setuptools`
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

Backlinks: [[scanner]] · [[habu]] · [[BlackArch Tools]]

