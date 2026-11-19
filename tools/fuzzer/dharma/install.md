---
tool: "dharma"
build-system: "python"
upstream: "https://github.com/MozillaSecurity/dharma"
recipe: "blackarch"
source: "https://github.com/MozillaSecurity/dharma"
license: "custom:unknown"
blackarch-pkgver: "98.6b1e511"
---

# Installing dharma

> Generation-based, context-free grammar fuzzer.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dharma) (version `98.6b1e511`, license custom:unknown).

## Arch / BlackArch

```bash
sudo pacman -S dharma    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/MozillaSecurity/dharma) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `setuptools`
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

Backlinks: [[fuzzer]] · [[dharma]] · [[BlackArch Tools]]

