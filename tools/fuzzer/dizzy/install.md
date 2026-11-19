---
tool: "dizzy"
build-system: "python"
upstream: "http://www.c0decafe.de/"
recipe: "blackarch"
source: "http://www.c0decafe.de/"
license: "custom:unknown"
blackarch-pkgver: "2.0"
---

# Installing dizzy

> A Python based fuzzing framework with many features.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dizzy) (version `2.0`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S dizzy    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](http://www.c0decafe.de/) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `pcapy`, `setuptools`
- **System libraries** (Arch name — translate for your distro):
  - `exrex`
  - `python`

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[fuzzer]] · [[dizzy]] · [[BlackArch Tools]]

