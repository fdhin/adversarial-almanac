---
tool: "uberfile"
build-system: "python"
upstream: "https://github.com/ShutdownRepo/uberfile"
recipe: "blackarch"
source: "https://github.com/ShutdownRepo/uberfile"
license: "GPL-3.0-or-later"
blackarch-pkgver: "14.4414c2a"
---

# Installing uberfile

> CLI tool for the generation of downloader oneliners for UNIX-like or Windows systems.

Recipe source of truth: BlackArch [`PKGBUILD`](https://github.com/BlackArch/blackarch/tree/master/packages/uberfile) (version `14.4414c2a`, license GPL-3.0-or-later).

## Arch / BlackArch

```bash
sudo pacman -S uberfile    # needs the [blackarch] repo enabled
```

## Any Linux distro

**From source:**

**Source:** download the release from the [upstream site](https://github.com/ShutdownRepo/uberfile) (the recipe uses a versioned archive).

```bash
python3 -m pip install --user .   # or: pip install -r requirements.txt
```

## Dependencies (from recipe)

- **Python (pip)**: `colorama`, `console-menu`, `psutil`, `setuptools`, `simple-term-menu`
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

Backlinks: [[misc]] · [[uberfile]] · [[BlackArch Tools]]

