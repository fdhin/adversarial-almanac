---
tool: "compp"
build-system: "python"
upstream: "https://github.com/sec-it/ComPP"
recipe: "blackarch"
source: "https://github.com/sec-it/ComPP"
license: "MIT"
blackarch-pkgver: "1.0.5"
---

# Installing compp

> Company Passwords Profiler helps making a bruteforce wordlist for a targeted company.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/compp) (version `1.0.5`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S compp    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/sec-it/ComPP) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `setuptools`
- **System libraries** (Arch name — translate for your distro):
  - `python`

## Exact build steps from the recipe

```bash
cd "$_pkgname-$pkgver"

  python setup.py build
```

## Installed layout (per recipe)

- No `/usr/bin` entry — scripts run from the source/`/usr/share` directory

Backlinks: [[cracker]] · [[compp]] · [[BlackArch Tools]]

