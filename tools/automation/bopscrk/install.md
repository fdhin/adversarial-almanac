---
tool: "bopscrk"
build-system: "python"
upstream: "https://github.com/R3nt0n/bopscrk"
recipe: "blackarch"
source: "https://github.com/R3nt0n/bopscrk"
license: "GPL3"
blackarch-pkgver: "v2.4.7.r2.g7eede16"
---

# Installing bopscrk

> Tool to generate smart wordlists, eg. based on lyrics.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/bopscrk) (version `v2.4.7.r2.g7eede16`, license GPL3).

## Arch / BlackArch

```bash
sudo pacman -S bopscrk    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/R3nt0n/bopscrk) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `alive-progress`, `requests`, `setuptools`
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

Backlinks: [[automation]] · [[bopscrk]] · [[BlackArch Tools]]

