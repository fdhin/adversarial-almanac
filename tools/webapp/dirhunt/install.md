---
tool: "dirhunt"
build-system: "python"
upstream: "https://github.com/hahwul/dirhunt"
recipe: "blackarch"
source: "https://github.com/hahwul/dirhunt"
license: "MIT"
blackarch-pkgver: "329.a5ea20d"
---

# Installing dirhunt

> Find web directories without bruteforce.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/dirhunt) (version `329.a5ea20d`, license MIT).

## Arch / BlackArch

```bash
sudo pacman -S dirhunt    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/hahwul/dirhunt) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `beautifulsoup4`, `click`, `colorama`, `google`, `humanize`, `legacy-cgi`, `proxy-db`, `requests`, `requests-mock`, `setuptools`, `six`
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

Backlinks: [[webapp]] · [[dirhunt]] · [[BlackArch Tools]]

